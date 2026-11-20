# ADR-002: Worker em processo separado, lendo a outbox por polling de 2 segundos

- **Status:** Aceito
- **Data:** dia da reunião técnica (ver `TRANSCRICAO.md`, 09:09 a 09:13)
- **Decisores:** Larissa, Diego, Bruno, Marcos
- **Relacionados:** [ADR-001](ADR-001-outbox-no-mysql.md), [ADR-006](ADR-006-reuso-dos-padroes-do-projeto.md), [FDD](../FDD.md)

## Contexto

Com a outbox definida ([ADR-001](ADR-001-outbox-no-mysql.md)), é preciso decidir **como** e **onde** os eventos são consumidos. O requisito de produto é entrega em menos de 10 segundos ([09:02] Marcos).

Fatos do ambiente:
- MySQL não tem mecanismo nativo de notificação a processos externos como o `LISTEN/NOTIFY` do Postgres; triggers só executam SQL ([09:09] Diego).
- A API sobe por um único entry-point, `src/server.ts`, que cria o app Express e trata `SIGINT`/`SIGTERM`.
- O `PrismaClient` é criado por processo em `src/config/database.ts`.

## Decisão

1. **Polling em loop a cada 2 segundos**: o worker busca, em lote pequeno, os eventos pendentes mais antigos (ordem de `created_at`), processa e marca o resultado ([09:08] Diego, [09:09] Diego). Latência adicional de até 2 s no pior caso, aceita pelo time ([09:10] Larissa, [09:10] Marcos).
2. **Processo separado da API**: novo entry-point `src/worker.ts`, executado por um script `npm run worker` ([09:11] Diego, [09:11] Larissa). Se a API reiniciar, o worker não cai junto.
3. **Mesmo banco, mesma stack, novo `PrismaClient`**: mesma `DATABASE_URL`, instância própria porque é outro processo Node ([09:11] Bruno, [09:30] Bruno).
4. **Single-worker**: apenas uma instância roda por vez. A lógica de processamento fica dentro do módulo, em `src/modules/webhooks/webhook.processor.ts` ([09:28] Bruno).

## Alternativas consideradas

| Alternativa | Por que foi descartada |
|---|---|
| **Trigger no banco para reagir à inserção** | MySQL não notifica processo externo; seria preciso improvisar (escrever em arquivo, chamar endpoint), solução frágil ([09:09] Bruno, [09:09] Diego). |
| **Worker dentro do processo da API** | Reinício ou deploy da API derrubaria o worker; escalar a API duplicaria workers ([09:11] Diego). |
| **Múltiplos workers em paralelo** | Perde a garantia de ordem por pedido. Adiado: no futuro, particionar por `order_id` ou usar lock pessimista ([09:12] Diego, [09:13] Diego). |

## Consequências

**Positivas**
- Simples de implementar e operar; sem dependência nova.
- 2 s de polling cabem com folga no requisito de 10 s ([09:09] Diego).
- Com um único worker processando por `created_at`, eventos do mesmo pedido saem na ordem em que foram gerados ([09:12] Diego).

**Negativas**
- Consultas periódicas ao banco mesmo sem eventos (custo baixo graças ao índice em `status`/`created_at`).
- **Limitação conhecida:** não há garantia de ordem global; a ordem é apenas por `order_id` e enquanto houver um único worker ([09:13] Larissa). Os clientes não pediram ordem global ([09:14] Marcos).
- Throughput limitado a um processo; escalar exige revisitar esta decisão.
- Um novo processo para implantar e monitorar.

**Trade-off explícito:** aceita-se latência de até 2 s e escala vertical limitada em troca de uma solução sem infraestrutura nova e com ordem por pedido preservada.
