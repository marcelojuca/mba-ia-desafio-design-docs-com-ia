# ADR-005: Garantia de entrega at-least-once com deduplicação por X-Event-Id

- **Status:** Aceito
- **Data:** dia da reunião técnica (ver `TRANSCRICAO.md`, 09:24 a 09:26)
- **Decisores:** Larissa, Diego, Sofia, Marcos
- **Relacionados:** [ADR-001](ADR-001-outbox-no-mysql.md), [ADR-003](ADR-003-retry-com-backoff-e-dlq.md), [FDD](../FDD.md)

## Contexto

Com outbox + worker + retry, o mesmo evento pode ser enviado mais de uma vez. Por exemplo, o cliente processa o evento mas a resposta não chega em 10 s, e o worker tenta de novo. Ou o worker cai depois do POST e antes de marcar o evento como entregue. É preciso definir a semântica de entrega e como o cliente lida com duplicatas.

## Decisão

1. A plataforma garante **at-least-once**: todo evento registrado é entregue pelo menos uma vez (ou termina na DLQ), podendo ser entregue mais de uma vez ([09:24] Diego).
2. Cada evento recebe um **`event_id` UUID gerado no momento em que entra na outbox**, enviado no header **`X-Event-Id`** e repetido no corpo. O valor é o mesmo em todas as retentativas e em replays da DLQ ([09:25] Diego).
3. O cliente é responsável por deduplicar pelo `event_id` ([09:25] Diego, [09:26] Larissa).
4. Esse contrato é documentado com destaque no portal do desenvolvedor ([09:26] Marcos).

## Alternativas consideradas

| Alternativa | Por que foi descartada |
|---|---|
| **Exactly-once** | Exigiria coordenação entre os dois lados (confirmação de dois estágios, armazenamento de dedup compartilhado); bem mais complexo para pouco ganho ([09:25] Diego). |
| **At-most-once (sem retry)** | Incompatível com a política de retry decidida e com o objetivo de não perder eventos ([09:15] Diego). |

## Consequências

**Positivas**
- Mesmo modelo usado por Stripe e GitHub ([09:25] Diego), familiar para integradores.
- Implementação simples do nosso lado; nenhum estado distribuído.

**Negativas**
- Transfere responsabilidade ao cliente, ponto levantado pela Sofia ([09:25] Sofia). Mitigado com documentação no portal.
- Clientes que não deduplicam podem processar o mesmo evento duas vezes.

**Trade-off explícito:** simplicidade e robustez do nosso lado em troca de exigir idempotência do consumidor.
