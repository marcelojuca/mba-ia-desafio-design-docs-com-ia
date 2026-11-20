# ADR-004: Autenticação das entregas por HMAC-SHA256 com secret por endpoint e rotação com grace period de 24h

- **Status:** Aceito (sujeito à revisão de segurança antes do deploy)
- **Data:** dia da reunião técnica (ver `TRANSCRICAO.md`, 09:19 a 09:23)
- **Decisores:** Sofia (Segurança), Larissa, Diego, Bruno
- **Relacionados:** [ADR-005](ADR-005-entrega-at-least-once-com-x-event-id.md), [FDD](../FDD.md)

## Contexto

Os webhooks levam dados de pedidos para endpoints fora da nossa infraestrutura. O cliente precisa conseguir verificar que a requisição veio de nós e que o payload não foi adulterado ([09:19] Sofia). Já aconteceu de um cliente vazar secret em log da própria aplicação ([09:22] Diego). O fluxo é apenas outbound: nós enviamos, o cliente recebe ([09:02] Marcos, [09:03] Sofia).

## Decisão

1. **HMAC-SHA256 sobre o corpo do request**, enviado no header `X-Signature` ([09:20] Sofia, [09:22] Sofia).
2. **Secret única por endpoint de webhook**, nunca uma secret global da plataforma ([09:21] Sofia).
3. **Secret gerada pela plataforma** e devolvida ao cliente na criação do webhook ([09:31] Marcos).
4. **Rotação via API**: o cliente pede uma nova secret; a antiga continua válida em paralelo por **24 horas** e depois é invalidada ([09:21] Sofia).
5. **TLS obrigatório**: URLs que não sejam `https` são recusadas na validação do schema Zod ([09:23] Sofia).
6. Revisão de segurança dedicada (pelo menos 2 dias úteis) do código de HMAC e geração de secret antes do deploy ([09:46] Sofia).

## Alternativas consideradas

| Alternativa | Por que foi descartada |
|---|---|
| **Secret global da plataforma** | Um vazamento comprometeria todos os clientes de uma vez ([09:21] Sofia). |
| **Rotação com troca imediata (sem grace period)** | Quebraria a integração do cliente até ele atualizar seus sistemas; 24 h em paralelo dão tempo de migrar ([09:21] Sofia). |
| **Aceitar `http`** | Payload e assinatura trafegariam em claro; recusado na validação ([09:23] Sofia). |

## Consequências

**Positivas**
- Padrão de mercado: qualquer cliente sério tem biblioteca de HMAC-SHA256 ([09:20] Sofia).
- Vazamento fica contido a um único endpoint.
- Rotação sem downtime para o cliente.

**Negativas / pontos a resolver na revisão de segurança**
- Como o servidor precisa da secret em claro para assinar, ela não pode ser guardada como hash; a forma de armazenamento (texto puro vs. cifrada) **não foi decidida** na reunião.
- A forma de sinalizar duas assinaturas válidas durante o grace period não foi detalhada; o FDD traz uma proposta marcada como hipótese.
- A assinatura cobre só o corpo. O `X-Timestamp` existe para o cliente detectar replay "se quiser" ([09:44] Diego), mas não entra na assinatura.

**Trade-off explícito:** mais estado por webhook (secret atual e anterior com expiração) e mais complexidade de rotação, em troca de isolamento de vazamentos e verificação de autenticidade pelo cliente.
