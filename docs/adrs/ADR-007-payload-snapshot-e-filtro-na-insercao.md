# ADR-007: Payload renderizado como snapshot e filtro de eventos aplicados na inserção da outbox

- **Status:** Aceito
- **Data:** dia da reunião técnica (ver `TRANSCRICAO.md`, 09:33 a 09:34, 09:43 e 09:51 a 09:52)
- **Decisores:** Larissa, Diego, Bruno, Marcos
- **Relacionados:** [ADR-001](ADR-001-outbox-no-mysql.md), [FDD](../FDD.md)

## Contexto

Duas perguntas complementares sobre o conteúdo da outbox:

1. **Quando renderizar o payload?** Se a outbox guardar apenas `order_id`, o worker montaria o JSON no envio, e o pedido pode ter mudado de novo até lá. Isso se agrava com retries de até ~15 h ([ADR-003](ADR-003-retry-com-backoff-e-dlq.md)) ([09:51] Bruno).
2. **Onde aplicar o filtro de eventos?** Cada webhook escolhe os status que quer receber ([09:33] Marcos). O filtro pode rodar na inserção da outbox ou no envio ([09:34] Diego).

## Decisão

1. **Snapshot na inserção:** o payload JSON é montado e gravado na outbox dentro da transação de `changeStatus`. O evento reflete o estado do pedido no momento da mudança ([09:52] Larissa, [09:52] Diego, [09:52] Bruno).
2. **Filtro na inserção:** só se insere uma linha na outbox para webhooks ativos do `customer_id` do pedido cujo filtro contém o `to_status`. Se nenhum webhook se interessa, nada é inserido ([09:34] Bruno, [09:34] Diego).
3. **Payload enxuto:** `event_id`, `event_type` (`order.status_changed`), `timestamp` ISO 8601, `order_id`, `order_number`, `from_status`, `to_status`, `customer_id` e campos básicos do pedido como `total_cents`. **Sem itens**; detalhes via `GET /orders/:id` ([09:43] Diego, [09:44] Bruno).

## Alternativas consideradas

| Alternativa | Por que foi descartada |
|---|---|
| **Guardar só `order_id` e renderizar no envio** | O evento poderia carregar um estado posterior ao da mudança, gerando "caso esquisito" ([09:52] Larissa). |
| **Filtrar no momento do envio** | Gravaria linhas que nunca serão enviadas; filtrar na inserção economiza linhas ([09:34] Bruno). |
| **Incluir os itens do pedido no payload** | Inflaria o payload sem necessidade ([09:43] Diego). |

## Consequências

**Positivas**
- O evento é imutável e fiel ao momento da transição, inclusive em retries e replays.
- Outbox menor; o worker não precisa consultar `orders` nem `webhooks` para montar o corpo.
- Payload pequeno, bem abaixo do teto de 64 KB ([09:24] Diego).

**Negativas**
- `changeStatus` passa a ler a configuração de webhooks do cliente dentro da transação (uma consulta a mais).
- Mudanças no filtro **não** afetam eventos já enfileirados. O tratamento de eventos pendentes de webhooks desativados ou removidos fica definido no FDD.
- Duplicação de dados do pedido em JSON na outbox.

**Trade-off explícito:** custo de leitura e armazenamento na escrita em troca de eventos imutáveis e um worker mais simples.
