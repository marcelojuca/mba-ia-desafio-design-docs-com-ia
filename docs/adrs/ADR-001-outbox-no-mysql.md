# ADR-001: Padrão Outbox no MySQL existente para eventos de webhook

- **Status:** Aceito
- **Data:** dia da reunião técnica (ver `TRANSCRICAO.md`, 09:06 a 09:08)
- **Decisores:** Larissa (Tech Lead), Diego (Plataforma), Bruno (Pedidos)
- **Relacionados:** [ADR-002](ADR-002-worker-separado-em-polling.md), [ADR-006](ADR-006-reuso-dos-padroes-do-projeto.md), [RFC](../RFC.md), [FDD](../FDD.md)

## Contexto

Três clientes B2B querem ser notificados quando o status dos seus pedidos muda ([09:00] Marcos). A mudança de status acontece em `OrderService.changeStatus` (`src/modules/orders/order.service.ts`), dentro de um `prisma.$transaction` que já atualiza `orders`, insere em `order_status_history` e debita/repõe `stock_quantity` ([09:04] Bruno).

Duas forças entram em conflito:

1. A notificação precisa ser **consistente** com a mudança de status: se o status mudou, o evento tem que existir; se a transação deu rollback, o evento não pode existir ([09:06] Diego, [09:40] Bruno).
2. A chamada HTTP para o cliente **não pode** acontecer dentro da transação: um cliente lento travaria a mudança de status de outros pedidos, e um cliente fora do ar não pode provocar rollback ([09:04] Bruno).

A aplicação hoje não tem fila, broker ou mecanismo de eventos. O banco é MySQL 8 via Prisma (`prisma/schema.prisma`, `docker-compose.yml`). O time é pequeno ([09:07] Diego).

## Decisão

Adotar o **padrão Transactional Outbox no MySQL existente**:

- Criar a tabela `webhook_outbox`. Dentro da **mesma transação** de `changeStatus`, inserir uma linha por evento a ser entregue ([09:06] Diego).
- Se a inserção na outbox falhar, a transação inteira faz rollback: não existe caso de status mudar sem evento registrado ([09:40] Bruno, [09:41] Diego).
- A tabela tem índice em `status` (pendente, processando, falhou, entregue) e em `created_at` ([09:08] Diego).
- Um processo separado lê a outbox e faz as chamadas HTTP (detalhado no [ADR-002](ADR-002-worker-separado-em-polling.md)).
- Chave primária UUID, seguindo o padrão do projeto ([09:51] Larissa).

## Alternativas consideradas

| Alternativa | Por que foi descartada |
|---|---|
| **Disparo síncrono no `changeStatus`** | Acopla a latência do cliente à transação de status; cliente fora do ar exigiria rollback da mudança de status, o que é inaceitável ([09:04] Bruno, [09:06] Diego). |
| **Redis Streams / Redis Cluster como fila** | Exige subir e operar infraestrutura nova; para um time pequeno é overengineering, e não resolve sozinho a atomicidade com a transação MySQL ([09:07] Larissa, [09:07] Diego). |

## Consequências

**Positivas**
- Atomicidade garantida pelo próprio banco: commit registra o evento, rollback o descarta ([09:06] Diego).
- Zero infraestrutura nova: mesmo MySQL, mesmo Prisma.
- A outbox também serve de trilha de auditoria do que foi emitido.

**Negativas**
- A transação de `changeStatus` fica um pouco maior (um `INSERT` a mais por webhook interessado).
- A tabela cresce continuamente. O arquivamento de linhas entregues (cerca de 30 dias) foi explicitamente deixado **fora do escopo** desta feature ([09:08] Diego).
- A latência de entrega passa a depender da frequência de leitura do worker (ver ADR-002).

**Trade-off explícito:** troca-se a simplicidade de uma chamada direta por consistência forte entre estado do pedido e evento, aceitando crescimento de tabela e latência de alguns segundos.
