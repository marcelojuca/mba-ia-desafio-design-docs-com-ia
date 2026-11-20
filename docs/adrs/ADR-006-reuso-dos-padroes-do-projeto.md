# ADR-006: Webhooks como módulo padrão do projeto, reaproveitando AppError, Pino, middlewares e Zod

- **Status:** Aceito
- **Data:** dia da reunião técnica (ver `TRANSCRICAO.md`, 09:27 a 09:30 e 09:36)
- **Decisores:** Larissa, Bruno, Diego, Sofia
- **Relacionados:** [ADR-002](ADR-002-worker-separado-em-polling.md), [FDD, seção 12](../FDD.md#12-integração-com-o-sistema-existente)

## Contexto

O OMS já tem convenções claras, verificáveis no código:

| Convenção | Onde está |
|---|---|
| Um módulo por domínio com `controller`, `service`, `repository`, `routes`, `schemas` | `src/modules/orders/`, `src/modules/customers/`, `src/modules/products/` |
| Hierarquia de erros com `statusCode` + `errorCode` em SCREAMING_SNAKE_CASE | `src/shared/errors/app-error.ts`, `src/shared/errors/http-errors.ts` (`InsufficientStockError`, `InvalidStatusTransitionError`) |
| Tratamento centralizado de `AppError`, `ZodError` e erros conhecidos do Prisma | `src/middlewares/error.middleware.ts` |
| Logger Pino com redaction de campos sensíveis | `src/shared/logger/index.ts` |
| Validação de body/query/params com Zod | `src/middlewares/validate.middleware.ts` |
| Autenticação JWT e autorização por role | `src/middlewares/auth.middleware.ts` (`authenticate`, `requireRole`) |
| IDs UUID em `CHAR(36)` | `prisma/schema.prisma` |
| Composição de dependências e montagem de rotas | `src/app.ts`, `src/routes/index.ts` |

Introduzir convenções novas (outro logger, outro formato de erro) aumentaria a carga cognitiva de um time pequeno sem benefício.

## Decisão

**Reuso máximo do que já existe** ([09:30] Larissa):

1. Novo módulo `src/modules/webhooks/` com a mesma estrutura dos demais ([09:27] Bruno); lógica do worker em `src/modules/webhooks/webhook.processor.ts` e entry-point em `src/worker.ts` ([09:28] Bruno).
2. Erros do módulo estendem `AppError` (ou as subclasses de `http-errors.ts`) com **códigos prefixados `WEBHOOK_`**, como `WEBHOOK_NOT_FOUND`, `WEBHOOK_INVALID_URL`, `WEBHOOK_SECRET_REQUIRED` ([09:28] Bruno, [09:29] Larissa).
3. Logs via o `logger` Pino existente; nenhuma biblioteca nova de log ([09:29] Bruno).
4. O `errorMiddleware` existente trata os novos erros **sem alteração** ([09:29] Bruno).
5. Schemas Zod no padrão de `*.schemas.ts`, aplicados com `validate()`.
6. Endpoint de replay da DLQ protegido pelo `requireRole('ADMIN')` existente ([09:36] Larissa).
7. Chaves primárias UUID ([09:51] Larissa).

## Alternativas consideradas

| Alternativa | Por que foi descartada |
|---|---|
| **Serviço/repositório separado para webhooks** (microsserviço) | Não discutido como opção viável: o time quer o worker na "mesma stack, mesmo banco" ([09:11] Diego) e o módulo "igual aos outros" ([09:30] Larissa). Exigiria deploy, banco e convenções próprias. |
| **Injetar `WebhookRepository` inteiro no `OrderService`** | Preterido em favor de uma função pura `publishWebhookEvent(tx, ...)` que recebe o client da transação ([09:41] Bruno, [09:41] Diego). |

## Consequências

**Positivas**
- Desenvolvedores do time de Pedidos reconhecem a estrutura de imediato.
- Respostas de erro com o mesmo envelope `{ error: { code, message, details } }` já gerado pelo `errorMiddleware`.
- Nenhuma dependência nova de log ou validação.

**Negativas**
- O worker não passa pelo `errorMiddleware` (não é HTTP); precisa de tratamento próprio de erro e log no loop.
- O módulo herda limitações atuais, por exemplo a ausência de stack de métricas/tracing no projeto (ver Observabilidade no FDD).

**Trade-off explícito:** consistência e velocidade de entrega acima de liberdade de desenho; o módulo fica acoplado às convenções do monólito.
