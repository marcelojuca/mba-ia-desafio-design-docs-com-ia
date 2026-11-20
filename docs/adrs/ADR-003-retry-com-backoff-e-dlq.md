# ADR-003: Retry com backoff exponencial fixo (1m/5m/30m/2h/12h) e DLQ em tabela separada

- **Status:** Aceito
- **Data:** dia da reunião técnica (ver `TRANSCRICAO.md`, 09:14 a 09:19)
- **Decisores:** Larissa, Diego, Bruno, Marcos
- **Relacionados:** [ADR-002](ADR-002-worker-separado-em-polling.md), [ADR-005](ADR-005-entrega-at-least-once-com-x-event-id.md), [FDD](../FDD.md)

## Contexto

Endpoints de clientes ficam indisponíveis: já houve cliente com duas horas de manutenção planejada ([09:16] Diego). É preciso tentar de novo sem martelar o cliente e sem deixar eventos pendurados para sempre ([09:15] Diego). Também é preciso um lugar para eventos que esgotaram as tentativas, com evidência para debug e reprocessamento.

## Decisão

1. **5 tentativas com backoff exponencial em degraus fixos: 1 min, 5 min, 30 min, 2 h, 12 h**, cerca de 15 horas entre a primeira falha e a última tentativa ([09:15] Diego, [09:17] Diego, [09:17] Larissa).
2. **Falha** = erro de rede, resposta não-2xx ou ausência de resposta em **10 segundos** ([09:42] Diego).
3. Esgotadas as tentativas, o evento vai para a **DLQ em tabela separada `webhook_dead_letter`**, com payload, motivo da falha e timestamp ([09:18] Diego).
4. **Reprocessamento manual** via `POST /admin/webhooks/dead-letter/:id/replay`, que recoloca o evento na outbox como pendente ([09:18] Diego). Exige role `ADMIN` e registra em log quem fez o replay ([09:36] Sofia).

> **Interpretação registrada:** a progressão 1m/5m/30m/2h/12h tem 5 intervalos e soma ~14,6 h, que bate com "quase 15 horas entre primeira falha e última tentativa" ([09:17] Diego). Por isso o FDD trata como **envio inicial + 5 retentativas**. O resumo da reunião diz "total 5 tentativas" ([09:48] Larissa); a divergência está listada como questão em aberto no [RFC](../RFC.md#5-questões-em-aberto).

## Alternativas consideradas

| Alternativa | Por que foi descartada |
|---|---|
| **Retry indefinido com backoff** | Evento fica pendurado para sempre se o cliente sumiu ([09:15] Diego). |
| **3 tentativas** (proposta do Bruno) | Pouco: três retries em ~30 min matariam o evento durante uma indisponibilidade de 2 h ([09:16] Bruno, [09:16] Diego). |
| **Marcar como `failed` na própria outbox (sem tabela de DLQ)** | Polui a leitura da outbox principal; tabela separada deixa a consulta do worker mais limpa e serve de evidência ([09:17] Larissa, [09:18] Diego). |

## Consequências

**Positivas**
- Cobre indisponibilidades de até ~15 h, maiores que o caso real de 2 h.
- Política determinística e fácil de testar (tabela de delays fixa).
- DLQ dá visibilidade e caminho de recuperação controlado e auditado.

**Negativas**
- Evento pode chegar com até ~15 h de atraso; quem ficar fora mais que isso depende de replay manual. O PM considera aceitável ([09:17] Marcos).
- Replay é manual: não há reprocessamento automático da DLQ nem aviso ao cliente (e-mail adiado para a próxima fase, [09:37] Larissa).
- Retentativas de um evento antigo podem chegar ao cliente depois de eventos mais novos do mesmo pedido (ver riscos no FDD).

**Trade-off explícito:** prioriza-se não perder eventos em indisponibilidades realistas, aceitando atraso longo no pior caso e operação manual da DLQ.
