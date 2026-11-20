# Tracker de Rastreabilidade

Cada linha liga um item dos documentos à sua origem: a fala na `TRANSCRICAO.md` (formato `[hh:mm] Nome`) ou um arquivo real do código. Os IDs são os mesmos usados dentro dos documentos.

**Fora do tracker de propósito:** as hipóteses de implementação `H-01` a `H-14` do [FDD, seção 13](FDD.md#13-hipóteses-e-pontos-a-confirmar). Elas não têm origem na reunião nem no código. Por isso estão marcadas como hipóteses e aguardam validação na revisão técnica.

| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |
|---|---|---|---|---|---|
| PRD-CTX-01 | docs/PRD.md | Contexto | Três clientes B2B (Atlas, MaxDistribuição, Nova Cargo) pediram notificação em tempo real | TRANSCRICAO | [09:00] Marcos |
| PRD-CTX-02 | docs/PRD.md | Problema | Clientes fazem polling em GET /orders; integração lenta e cara | TRANSCRICAO | [09:00] Marcos |
| PRD-CTX-03 | docs/PRD.md | Restrição | Atlas pode migrar para concorrente se não houver entrega até o fim do trimestre | TRANSCRICAO | [09:00] Marcos |
| PRD-CTX-04 | docs/PRD.md | Restrição | Atlas quer a feature até o fim de novembro | TRANSCRICAO | [09:45] Marcos |
| PRD-CEN-01 | docs/PRD.md | Cenário | Cliente assina apenas SHIPPED e DELIVERED | TRANSCRICAO | [09:33] Marcos |
| PRD-CEN-02 | docs/PRD.md | Cenário | Endpoint do cliente fora do ar por 2 h em manutenção planejada | TRANSCRICAO | [09:16] Diego |
| PRD-CEN-03 | docs/PRD.md | Cenário | Cliente consulta histórico de entregas | TRANSCRICAO | [09:34] Marcos |
| PRD-CEN-04 | docs/PRD.md | Cenário | Cliente vazou secret e rotaciona com 24 h de convivência | TRANSCRICAO | [09:22] Diego |
| PRD-CEN-05 | docs/PRD.md | Cenário | Admin reprocessa evento que esgotou tentativas | TRANSCRICAO | [09:18] Diego |
| PRD-MET-01 | docs/PRD.md | Métrica | Entrega em menos de 10 s (definição de tempo real dos clientes) | TRANSCRICAO | [09:02] Marcos |
| PRD-MET-02 | docs/PRD.md | Métrica | Atraso máximo de 2 s até a primeira tentativa | TRANSCRICAO | [09:10] Larissa |
| PRD-MET-03 | docs/PRD.md | Métrica | 3 de 3 clientes solicitantes com webhook ativo | TRANSCRICAO | [09:00] Marcos |
| PRD-MET-04 | docs/PRD.md | Métrica | 0 mudanças de status sem evento registrado | TRANSCRICAO | [09:40] Bruno |
| PRD-MET-05 | docs/PRD.md | Métrica | Janela de retentativas de ~15 h | TRANSCRICAO | [09:17] Diego |
| PRD-MET-06 | docs/PRD.md | Métrica | Entrega em 3 sprints incluindo revisão de segurança | TRANSCRICAO | [09:46] Larissa |
| PRD-OOS-01 | docs/PRD.md | Fora de escopo | E-mail ao cliente em falhas repetidas adiado para próxima fase | TRANSCRICAO | [09:37] Larissa |
| PRD-OOS-02 | docs/PRD.md | Fora de escopo | Dashboard visual para o cliente; projeto do time de frontend | TRANSCRICAO | [09:40] Larissa |
| PRD-OOS-03 | docs/PRD.md | Fora de escopo | Rate limiting de envio: observar e decidir depois | TRANSCRICAO | [09:39] Larissa |
| PRD-OOS-04 | docs/PRD.md | Fora de escopo | Webhooks inbound; fluxo é só outbound | TRANSCRICAO | [09:02] Marcos |
| PRD-OOS-05 | docs/PRD.md | Fora de escopo | Sem garantia de ordem global | TRANSCRICAO | [09:13] Larissa |
| PRD-OOS-06 | docs/PRD.md | Fora de escopo | Exactly-once descartado | TRANSCRICAO | [09:25] Diego |
| PRD-OOS-07 | docs/PRD.md | Fora de escopo | Arquivamento de linhas entregues após ~30 dias | TRANSCRICAO | [09:08] Diego |
| PRD-OOS-08 | docs/PRD.md | Fora de escopo | Itens do pedido não vão no payload | TRANSCRICAO | [09:43] Diego |
| PRD-FR-01 | docs/PRD.md | Requisito Funcional | Cadastrar webhook com URL e status; secret gerada pela plataforma e devolvida | TRANSCRICAO | [09:31] Marcos |
| PRD-FR-02 | docs/PRD.md | Requisito Funcional | Listar webhooks de um customer | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-03 | docs/PRD.md | Requisito Funcional | Editar webhook (PATCH) | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-04 | docs/PRD.md | Requisito Funcional | Remover webhook (DELETE) | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-05 | docs/PRD.md | Requisito Funcional | Filtro de eventos por lista de status | TRANSCRICAO | [09:33] Marcos |
| PRD-FR-06 | docs/PRD.md | Requisito Funcional | Notificar toda mudança de status para webhooks interessados | TRANSCRICAO | [09:40] Bruno |
| PRD-FR-07 | docs/PRD.md | Requisito Funcional | Notificação assinada com secret exclusiva do webhook | TRANSCRICAO | [09:21] Sofia |
| PRD-FR-08 | docs/PRD.md | Requisito Funcional | Rotação de secret com validade paralela de 24 h | TRANSCRICAO | [09:21] Sofia |
| PRD-FR-09 | docs/PRD.md | Requisito Funcional | Histórico das últimas 100 entregas com payload, resposta e tempo | TRANSCRICAO | [09:34] Marcos |
| PRD-FR-10 | docs/PRD.md | Requisito Funcional | Reenvio automático em 1m/5m/30m/2h/12h | TRANSCRICAO | [09:17] Larissa |
| PRD-FR-11 | docs/PRD.md | Requisito Funcional | Falhas permanentes guardadas e reprocessáveis por admin | TRANSCRICAO | [09:18] Diego |
| PRD-FR-12 | docs/PRD.md | Requisito Funcional | Identificador único e estável por notificação | TRANSCRICAO | [09:25] Diego |
| PRD-FR-13 | docs/PRD.md | Requisito Funcional | Notificação identifica o webhook e o momento do envio | TRANSCRICAO | [09:44] Sofia |
| PRD-FR-14 | docs/PRD.md | Requisito Funcional | Payload com dados essenciais do pedido, sem itens | TRANSCRICAO | [09:43] Diego |
| PRD-NFR-01 | docs/PRD.md | Requisito Não Funcional | Entrega < 10 s; até 2 s de espera | TRANSCRICAO | [09:02] Marcos |
| PRD-NFR-02 | docs/PRD.md | Requisito Não Funcional | Consistência entre mudança de status e notificação | TRANSCRICAO | [09:06] Diego |
| PRD-NFR-03 | docs/PRD.md | Requisito Não Funcional | Cliente lento não trava mudança de status | TRANSCRICAO | [09:04] Bruno |
| PRD-NFR-04 | docs/PRD.md | Requisito Não Funcional | Apenas URLs https | TRANSCRICAO | [09:23] Sofia |
| PRD-NFR-05 | docs/PRD.md | Requisito Não Funcional | HMAC-SHA256 com secret por webhook | TRANSCRICAO | [09:20] Sofia |
| PRD-NFR-06 | docs/PRD.md | Requisito Não Funcional | Limite de 64 KB por notificação, com erro | TRANSCRICAO | [09:24] Larissa |
| PRD-NFR-07 | docs/PRD.md | Requisito Não Funcional | Timeout de 10 s conta como falha | TRANSCRICAO | [09:42] Diego |
| PRD-NFR-08 | docs/PRD.md | Requisito Não Funcional | Entrega at-least-once | TRANSCRICAO | [09:24] Diego |
| PRD-NFR-09 | docs/PRD.md | Requisito Não Funcional | Ordem apenas por pedido | TRANSCRICAO | [09:13] Larissa |
| PRD-NFR-10 | docs/PRD.md | Requisito Não Funcional | Replay só ADMIN; CRUD qualquer role autenticada | TRANSCRICAO | [09:37] Sofia |
| PRD-NFR-11 | docs/PRD.md | Requisito Não Funcional | Envio independente do processo da API | TRANSCRICAO | [09:11] Diego |
| PRD-NFR-12 | docs/PRD.md | Requisito Não Funcional | Seguir padrões existentes do projeto | TRANSCRICAO | [09:30] Larissa |
| PRD-DEP-01 | docs/PRD.md | Dependência | Revisão de segurança de pelo menos 2 dias úteis antes do deploy | TRANSCRICAO | [09:46] Sofia |
| PRD-DEP-02 | docs/PRD.md | Dependência | Documentação no portal do desenvolvedor | TRANSCRICAO | [09:26] Marcos |
| PRD-DEP-03 | docs/PRD.md | Dependência | PM confirma prazo com a Atlas | TRANSCRICAO | [09:47] Marcos |
| PRD-DEP-04 | docs/PRD.md | Dependência | Mudança de status em OrderService.changeStatus | CODIGO | src/modules/orders/order.service.ts |
| PRD-RISK-01 | docs/PRD.md | Risco | Perda da Atlas por atraso | TRANSCRICAO | [09:00] Marcos |
| PRD-RISK-02 | docs/PRD.md | Risco | Clientes processando duplicatas | TRANSCRICAO | [09:25] Sofia |
| PRD-RISK-03 | docs/PRD.md | Risco | Vazamento de secret de cliente | TRANSCRICAO | [09:22] Diego |
| PRD-RISK-04 | docs/PRD.md | Risco | Volume alto de notificações para um cliente | TRANSCRICAO | [09:38] Diego |
| PRD-RISK-05 | docs/PRD.md | Risco | Cliente fora do ar por mais de ~15 h | TRANSCRICAO | [09:17] Marcos |
| PRD-RISK-06 | docs/PRD.md | Risco | Notificações do mesmo pedido fora de ordem após retry | TRANSCRICAO | [09:12] Diego |
| PRD-TEST-01 | docs/PRD.md | Estratégia de teste | Testes de integração no padrão Vitest + Supertest existente | CODIGO | tests/orders.test.ts |
| RFC-META-01 | docs/RFC.md | Metadado | Revisores: participantes da reunião; próximo passo é sessão de revisão com Bruno e Diego | TRANSCRICAO | [09:50] Larissa |
| RFC-PROP-01 | docs/RFC.md | Proposta | Evento gravado na outbox na mesma transação de changeStatus | TRANSCRICAO | [09:06] Diego |
| RFC-PROP-02 | docs/RFC.md | Proposta | Worker separado, polling de 2 s, npm run worker | TRANSCRICAO | [09:11] Larissa |
| RFC-PROP-03 | docs/RFC.md | Proposta | Headers X-Event-Id, X-Signature, X-Timestamp, Content-Type | TRANSCRICAO | [09:44] Diego |
| RFC-PROP-04 | docs/RFC.md | Proposta | Header X-Webhook-Id | TRANSCRICAO | [09:44] Sofia |
| RFC-PROP-05 | docs/RFC.md | Proposta | customer_id não vem do JWT | TRANSCRICAO | [09:32] Larissa |
| RFC-PROP-06 | docs/RFC.md | Proposta | Usuários com JWT representam o cliente na API | TRANSCRICAO | [09:32] Marcos |
| RFC-ALT-01 | docs/RFC.md | Alternativa descartada | Disparo HTTP síncrono em changeStatus | TRANSCRICAO | [09:04] Bruno |
| RFC-ALT-02 | docs/RFC.md | Alternativa descartada | Redis Streams / Redis Cluster | TRANSCRICAO | [09:07] Diego |
| RFC-ALT-03 | docs/RFC.md | Alternativa descartada | Trigger de banco para acordar o worker | TRANSCRICAO | [09:09] Diego |
| RFC-ALT-04 | docs/RFC.md | Alternativa descartada | Exactly-once | TRANSCRICAO | [09:25] Diego |
| RFC-ALT-05 | docs/RFC.md | Alternativa descartada | Retry indefinido ou apenas 3 tentativas | TRANSCRICAO | [09:16] Diego |
| RFC-ALT-06 | docs/RFC.md | Alternativa descartada | Secret global da plataforma | TRANSCRICAO | [09:21] Sofia |
| RFC-Q-01 | docs/RFC.md | Questão em aberto | Rate limiting de saída | TRANSCRICAO | [09:39] Diego |
| RFC-Q-02 | docs/RFC.md | Questão em aberto | Escalar para múltiplos workers (particionar por order_id ou lock) | TRANSCRICAO | [09:13] Diego |
| RFC-Q-03 | docs/RFC.md | Questão em aberto | E-mail em falhas repetidas | TRANSCRICAO | [09:37] Marcos |
| RFC-Q-04 | docs/RFC.md | Questão em aberto | customer_id no body ou no path | TRANSCRICAO | [09:32] Larissa |
| RFC-Q-05 | docs/RFC.md | Questão em aberto | Endurecer autorização do CRUD mais tarde | TRANSCRICAO | [09:37] Sofia |
| RFC-Q-06 | docs/RFC.md | Questão em aberto | "5 tentativas" no resumo vs. 5 intervalos somando ~15 h | TRANSCRICAO | [09:48] Larissa |
| RFC-Q-07 | docs/RFC.md | Questão em aberto | Armazenamento da secret e assinatura no grace period | TRANSCRICAO | [09:46] Sofia |
| RFC-Q-08 | docs/RFC.md | Questão em aberto | Arquivamento de linhas entregues | TRANSCRICAO | [09:08] Diego |
| RFC-IMP-01 | docs/RFC.md | Impacto | Estimativa por etapa: outbox/DLQ 1 sprint, worker/retry 1 sprint, CRUD ½, integração ½ | TRANSCRICAO | [09:46] Larissa |
| RFC-IMP-02 | docs/RFC.md | Impacto | Transação de changeStatus já atualiza orders, history e estoque | CODIGO | src/modules/orders/order.service.ts |
| ADR-001 | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | Padrão Outbox no MySQL existente | TRANSCRICAO | [09:08] Larissa |
| ADR-001-ALT-01 | docs/adrs/ADR-001-outbox-no-mysql.md | Alternativa descartada | Redis Streams exigiria mais infraestrutura | TRANSCRICAO | [09:07] Larissa |
| ADR-001-CON-01 | docs/adrs/ADR-001-outbox-no-mysql.md | Trade-off | Rollback da transação descarta o evento; commit o garante | TRANSCRICAO | [09:06] Diego |
| ADR-001-CTX-01 | docs/adrs/ADR-001-outbox-no-mysql.md | Contexto | Infra MySQL 8 via Prisma | CODIGO | prisma/schema.prisma |
| ADR-002 | docs/adrs/ADR-002-worker-separado-em-polling.md | Decisão | Worker separado em polling de 2 s | TRANSCRICAO | [09:10] Larissa |
| ADR-002-DEC-01 | docs/adrs/ADR-002-worker-separado-em-polling.md | Decisão | Processo separado da API | TRANSCRICAO | [09:11] Diego |
| ADR-002-DEC-02 | docs/adrs/ADR-002-worker-separado-em-polling.md | Decisão | Novo PrismaClient por processo, mesma DATABASE_URL | TRANSCRICAO | [09:30] Bruno |
| ADR-002-DEC-03 | docs/adrs/ADR-002-worker-separado-em-polling.md | Restrição | Single-worker; ordem por order_id | TRANSCRICAO | [09:12] Diego |
| ADR-002-CTX-01 | docs/adrs/ADR-002-worker-separado-em-polling.md | Contexto | Entry-point atual da API usado como modelo | CODIGO | src/server.ts |
| ADR-002-CTX-02 | docs/adrs/ADR-002-worker-separado-em-polling.md | Contexto | PrismaClient criado por processo | CODIGO | src/config/database.ts |
| ADR-003 | docs/adrs/ADR-003-retry-com-backoff-e-dlq.md | Decisão | 5 tentativas com backoff 1m/5m/30m/2h/12h | TRANSCRICAO | [09:17] Larissa |
| ADR-003-DEC-01 | docs/adrs/ADR-003-retry-com-backoff-e-dlq.md | Decisão | DLQ em tabela separada webhook_dead_letter | TRANSCRICAO | [09:18] Diego |
| ADR-003-DEC-02 | docs/adrs/ADR-003-retry-com-backoff-e-dlq.md | Decisão | Replay manual via POST /admin/webhooks/dead-letter/:id/replay | TRANSCRICAO | [09:35] Diego |
| ADR-003-ALT-01 | docs/adrs/ADR-003-retry-com-backoff-e-dlq.md | Alternativa descartada | Retry indefinido | TRANSCRICAO | [09:15] Diego |
| ADR-003-ALT-02 | docs/adrs/ADR-003-retry-com-backoff-e-dlq.md | Alternativa descartada | 3 tentativas | TRANSCRICAO | [09:16] Bruno |
| ADR-003-ALT-03 | docs/adrs/ADR-003-retry-com-backoff-e-dlq.md | Alternativa descartada | Marcar failed na própria outbox | TRANSCRICAO | [09:17] Larissa |
| ADR-004 | docs/adrs/ADR-004-autenticacao-hmac-sha256-secret-por-endpoint.md | Decisão | HMAC-SHA256 sobre o corpo, secret por endpoint, rotação 24 h | TRANSCRICAO | [09:22] Sofia |
| ADR-004-DEC-01 | docs/adrs/ADR-004-autenticacao-hmac-sha256-secret-por-endpoint.md | Decisão | Algoritmo SHA-256 por ser padrão de mercado | TRANSCRICAO | [09:20] Sofia |
| ADR-004-DEC-02 | docs/adrs/ADR-004-autenticacao-hmac-sha256-secret-por-endpoint.md | Restrição | TLS obrigatório validado no schema Zod | TRANSCRICAO | [09:23] Sofia |
| ADR-004-CTX-01 | docs/adrs/ADR-004-autenticacao-hmac-sha256-secret-por-endpoint.md | Contexto | Fluxo apenas outbound | TRANSCRICAO | [09:03] Sofia |
| ADR-005 | docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md | Decisão | At-least-once com X-Event-Id | TRANSCRICAO | [09:26] Larissa |
| ADR-005-DEC-01 | docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md | Decisão | event_id UUID gerado na entrada da outbox | TRANSCRICAO | [09:25] Diego |
| ADR-005-CON-01 | docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md | Trade-off | Responsabilidade de deduplicação vai para o cliente | TRANSCRICAO | [09:25] Sofia |
| ADR-006 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | Reuso máximo dos padrões existentes | TRANSCRICAO | [09:30] Larissa |
| ADR-006-DEC-01 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | Módulo src/modules/webhooks com controller/service/repository/routes/schemas | TRANSCRICAO | [09:27] Bruno |
| ADR-006-DEC-02 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | Lógica do worker em webhook.processor.ts; entry src/worker.ts | TRANSCRICAO | [09:28] Bruno |
| ADR-006-DEC-03 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | Códigos de erro com prefixo WEBHOOK_ | TRANSCRICAO | [09:29] Larissa |
| ADR-006-DEC-04 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | Pino e error middleware reaproveitados sem mudança | TRANSCRICAO | [09:29] Bruno |
| ADR-006-DEC-05 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | requireRole existente no replay | TRANSCRICAO | [09:36] Larissa |
| ADR-006-DEC-06 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | IDs UUID | TRANSCRICAO | [09:51] Larissa |
| ADR-006-CTX-01 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Contexto | Hierarquia AppError com errorCode | CODIGO | src/shared/errors/app-error.ts |
| ADR-006-CTX-02 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Contexto | InsufficientStockError e InvalidStatusTransitionError como modelo | CODIGO | src/shared/errors/http-errors.ts |
| ADR-006-CTX-03 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Contexto | Error middleware trata AppError, Zod e Prisma | CODIGO | src/middlewares/error.middleware.ts |
| ADR-006-CTX-04 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Contexto | Logger Pino com redaction | CODIGO | src/shared/logger/index.ts |
| ADR-006-CTX-05 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Contexto | authenticate e requireRole | CODIGO | src/middlewares/auth.middleware.ts |
| ADR-006-ALT-01 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Alternativa descartada | Injetar repository inteiro no OrderService | TRANSCRICAO | [09:41] Diego |
| ADR-007 | docs/adrs/ADR-007-payload-snapshot-e-filtro-na-insercao.md | Decisão | Payload renderizado (snapshot) na inserção | TRANSCRICAO | [09:52] Larissa |
| ADR-007-DEC-01 | docs/adrs/ADR-007-payload-snapshot-e-filtro-na-insercao.md | Decisão | Filtro de eventos aplicado na inserção da outbox | TRANSCRICAO | [09:34] Bruno |
| ADR-007-DEC-02 | docs/adrs/ADR-007-payload-snapshot-e-filtro-na-insercao.md | Decisão | Campos do payload e exclusão de itens | TRANSCRICAO | [09:43] Diego |
| ADR-007-ALT-01 | docs/adrs/ADR-007-payload-snapshot-e-filtro-na-insercao.md | Alternativa descartada | Guardar só order_id e renderizar no envio | TRANSCRICAO | [09:51] Bruno |
| ADR-007-ALT-02 | docs/adrs/ADR-007-payload-snapshot-e-filtro-na-insercao.md | Alternativa descartada | Filtrar no momento do envio | TRANSCRICAO | [09:34] Diego |
| FDD-OBJ-01 | docs/FDD.md | Objetivo técnico | Atomicidade: falha na outbox faz rollback do status | TRANSCRICAO | [09:40] Bruno |
| FDD-OBJ-02 | docs/FDD.md | Objetivo técnico | Primeira tentativa em até 2 s; fim a fim < 10 s | TRANSCRICAO | [09:09] Diego |
| FDD-OBJ-03 | docs/FDD.md | Objetivo técnico | Envio inicial + 5 retentativas, ~14,6 h | TRANSCRICAO | [09:17] Diego |
| FDD-OBJ-04 | docs/FDD.md | Objetivo técnico | 100% das entregas assinadas | TRANSCRICAO | [09:22] Sofia |
| FDD-OBJ-05 | docs/FDD.md | Objetivo técnico | X-Event-Id estável entre tentativas | TRANSCRICAO | [09:25] Diego |
| FDD-OBJ-06 | docs/FDD.md | Objetivo técnico | Sem regressão nos testes de pedidos | CODIGO | tests/orders.test.ts |
| FDD-ESC-01 | docs/FDD.md | Exclusão | Evento na criação do pedido fora do escopo (só changeStatus) | TRANSCRICAO | [09:40] Bruno |
| FDD-ESC-02 | docs/FDD.md | Restrição | Nenhuma transição leva a PENDING; PENDING recusado em events | CODIGO | src/modules/orders/order.status.ts |
| FDD-DADOS-01 | docs/FDD.md | Modelo de dados | Tabela webhook_outbox com índices em status e created_at | TRANSCRICAO | [09:08] Diego |
| FDD-DADOS-02 | docs/FDD.md | Modelo de dados | Tabela webhooks com url, secret, customer_id, ativo | TRANSCRICAO | [09:21] Bruno |
| FDD-DADOS-03 | docs/FDD.md | Modelo de dados | Tabela webhook_dead_letter com payload, motivo e timestamp | TRANSCRICAO | [09:18] Diego |
| FDD-DADOS-04 | docs/FDD.md | Modelo de dados | Tabela webhook_deliveries para o histórico | TRANSCRICAO | [09:34] Marcos |
| FDD-DADOS-05 | docs/FDD.md | Modelo de dados | Convenções UUID CHAR(36) e @@map snake_case | CODIGO | prisma/schema.prisma |
| FDD-FLUXO-01 | docs/FDD.md | Fluxo | publishWebhookEvent dentro da transação de changeStatus | TRANSCRICAO | [09:41] Bruno |
| FDD-FLUXO-02 | docs/FDD.md | Fluxo | Worker busca pendentes mais antigos em lote e marca resultado | TRANSCRICAO | [09:09] Diego |
| FDD-FLUXO-03 | docs/FDD.md | Fluxo | Retry com tabela fixa de delays | TRANSCRICAO | [09:17] Diego |
| FDD-FLUXO-04 | docs/FDD.md | Fluxo | DLQ e replay que recoloca como pendente | TRANSCRICAO | [09:18] Diego |
| FDD-FLUXO-04b | docs/FDD.md | Fluxo | Replay registrado em log com o admin que executou | TRANSCRICAO | [09:36] Sofia |
| FDD-FLUXO-05 | docs/FDD.md | Fluxo | Rotação de secret com 24 h de grace | TRANSCRICAO | [09:21] Sofia |
| FDD-CONTRATO-01 | docs/FDD.md | Contrato | POST /api/v1/webhooks | TRANSCRICAO | [09:31] Marcos |
| FDD-CONTRATO-02 | docs/FDD.md | Contrato | GET /api/v1/webhooks?customerId= | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-03 | docs/FDD.md | Contrato | PATCH /api/v1/webhooks/:id | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-04 | docs/FDD.md | Contrato | DELETE /api/v1/webhooks/:id | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-05 | docs/FDD.md | Contrato | Endpoint de rotação de secret | TRANSCRICAO | [09:21] Sofia |
| FDD-CONTRATO-06 | docs/FDD.md | Contrato | GET /api/v1/webhooks/:id/deliveries (últimas 100) | TRANSCRICAO | [09:34] Marcos |
| FDD-CONTRATO-07 | docs/FDD.md | Contrato | POST /api/v1/admin/webhooks/dead-letter/:id/replay com ADMIN | TRANSCRICAO | [09:35] Diego |
| FDD-CONTRATO-08 | docs/FDD.md | Contrato | Requisição outbound: headers e corpo JSON | TRANSCRICAO | [09:44] Diego |
| FDD-CONTRATO-08b | docs/FDD.md | Contrato | event_type order.status_changed e campos do payload | TRANSCRICAO | [09:43] Diego |
| FDD-CONTRATO-09 | docs/FDD.md | Contrato | Assinatura publishWebhookEvent(tx, order, fromStatus, toStatus) | TRANSCRICAO | [09:41] Bruno |
| FDD-CONTRATO-10 | docs/FDD.md | Contrato | Prefixo /api/v1 e envelope de erro existentes | CODIGO | src/app.ts |
| FDD-CONTRATO-11 | docs/FDD.md | Contrato | Formato paginado reutilizado na listagem | CODIGO | src/shared/http/response.ts |
| FDD-CONTRATO-12 | docs/FDD.md | Contrato | order_number no formato ORD-000000 | CODIGO | src/modules/orders/order.service.ts |
| FDD-ERR-01 | docs/FDD.md | Erro | WEBHOOK_NOT_FOUND | TRANSCRICAO | [09:28] Bruno |
| FDD-ERR-02 | docs/FDD.md | Erro | WEBHOOK_INVALID_URL para URL não https | TRANSCRICAO | [09:23] Sofia |
| FDD-ERR-02b | docs/FDD.md | Restrição | validate middleware mapeia ZodError para VALIDATION_ERROR | CODIGO | src/middlewares/validate.middleware.ts |
| FDD-ERR-03 | docs/FDD.md | Erro | WEBHOOK_INVALID_EVENTS (prefixo WEBHOOK_ para todo o módulo) | TRANSCRICAO | [09:29] Larissa |
| FDD-ERR-08 | docs/FDD.md | Erro | WEBHOOK_DELIVERY_TIMEOUT após 10 s | TRANSCRICAO | [09:42] Diego |
| FDD-ERR-11 | docs/FDD.md | Erro | WEBHOOK_MAX_ATTEMPTS_EXCEEDED leva à DLQ | TRANSCRICAO | [09:15] Diego |
| FDD-ERR-12 | docs/FDD.md | Erro | WEBHOOK_PAYLOAD_TOO_LARGE, erro em vez de truncar | TRANSCRICAO | [09:23] Sofia |
| FDD-ERR-13 | docs/FDD.md | Erro | WEBHOOK_SECRET_REQUIRED | TRANSCRICAO | [09:28] Bruno |
| FDD-RES-01 | docs/FDD.md | Parâmetro | Polling 2000 ms | TRANSCRICAO | [09:09] Diego |
| FDD-RES-02 | docs/FDD.md | Parâmetro | Lote pequeno de eventos | TRANSCRICAO | [09:08] Diego |
| FDD-RES-03 | docs/FDD.md | Parâmetro | Timeout HTTP 10 000 ms | TRANSCRICAO | [09:42] Diego |
| FDD-RES-04 | docs/FDD.md | Parâmetro | Limite de payload 64 KB | TRANSCRICAO | [09:24] Diego |
| FDD-RES-05 | docs/FDD.md | Restrição | Sem fallback por e-mail nesta fase | TRANSCRICAO | [09:37] Larissa |
| FDD-RES-06 | docs/FDD.md | Parâmetro | Novas variáveis no schema de env com defaults | CODIGO | src/config/env.ts |
| FDD-OBS-01 | docs/FDD.md | Observabilidade | Logs via Pino existente, sem nova biblioteca | TRANSCRICAO | [09:29] Bruno |
| FDD-OBS-02 | docs/FDD.md | Observabilidade | Correlação por X-Request-Id existente | CODIGO | src/middlewares/request-logger.middleware.ts |
| FDD-OBS-03 | docs/FDD.md | Observabilidade | Sem stack de métricas/tracing nas dependências atuais | CODIGO | package.json |
| FDD-OBS-04 | docs/FDD.md | Observabilidade | Log de auditoria do replay | TRANSCRICAO | [09:36] Sofia |
| FDD-DEP-01 | docs/FDD.md | Dependência | Node >=20 com fetch global; versões de Prisma e uuid | CODIGO | package.json |
| FDD-DEP-02 | docs/FDD.md | Dependência | MySQL 8.0 | CODIGO | docker-compose.yml |
| FDD-DEP-03 | docs/FDD.md | Dependência | Worker usa mesma DATABASE_URL | TRANSCRICAO | [09:11] Diego |
| FDD-AC-15 | docs/FDD.md | Critério de aceite | Revisão de segurança antes do deploy | TRANSCRICAO | [09:46] Sofia |
| FDD-RISK-R01 | docs/FDD.md | Risco | Falha na outbox bloqueia mudança de status por desenho | TRANSCRICAO | [09:40] Bruno |
| FDD-RISK-R02 | docs/FDD.md | Risco | Inversão de ordem por retry com single-worker | TRANSCRICAO | [09:12] Diego |
| FDD-RISK-R03 | docs/FDD.md | Risco | Múltiplos workers perdem garantia de ordem | TRANSCRICAO | [09:12] Diego |
| FDD-RISK-R05 | docs/FDD.md | Risco | Vazamento de secret | TRANSCRICAO | [09:22] Diego |
| FDD-RISK-R06 | docs/FDD.md | Risco | Crescimento das tabelas; arquivamento fora de escopo | TRANSCRICAO | [09:08] Diego |
| FDD-RISK-R08 | docs/FDD.md | Risco | Prazo de 3 sprints com revisão no fim | TRANSCRICAO | [09:47] Larissa |
| FDD-INT-01 | docs/FDD.md | Integração | changeStatus chama publishWebhookEvent após order_status_history | CODIGO | src/modules/orders/order.service.ts |
| FDD-INT-02 | docs/FDD.md | Integração | Novos erros estendem AppError/ConflictError | CODIGO | src/shared/errors/http-errors.ts |
| FDD-INT-03 | docs/FDD.md | Integração | Error middleware sem alteração | CODIGO | src/middlewares/error.middleware.ts |
| FDD-INT-04 | docs/FDD.md | Integração | requireRole(ADMIN) como em user.routes | CODIGO | src/modules/users/user.routes.ts |
| FDD-INT-05 | docs/FDD.md | Integração | Montagem de rotas /webhooks e /admin/webhooks | CODIGO | src/routes/index.ts |
| FDD-INT-06 | docs/FDD.md | Integração | Wiring em buildControllers | CODIGO | src/app.ts |
| FDD-INT-07 | docs/FDD.md | Integração | src/worker.ts espelha bootstrap e shutdown da API | CODIGO | src/server.ts |
| FDD-INT-08 | docs/FDD.md | Integração | Worker usa createPrismaClient() | CODIGO | src/config/database.ts |
| FDD-INT-09 | docs/FDD.md | Integração | redactPaths ampliado para secret e assinatura | CODIGO | src/shared/logger/index.ts |
| FDD-INT-10 | docs/FDD.md | Integração | Relação webhooks em Customer e nova migração | CODIGO | prisma/schema.prisma |
| FDD-INT-11 | docs/FDD.md | Integração | Script npm run worker | TRANSCRICAO | [09:11] Larissa |
| FDD-INT-12 | docs/FDD.md | Integração | Limpeza das novas tabelas no beforeEach | CODIGO | tests/setup.ts |
| FDD-INT-13 | docs/FDD.md | Integração | Factories para webhooks e usuário ADMIN | CODIGO | tests/helpers/factories.ts |
| PRD-AC-01 | docs/PRD.md | Critério de aceitação | Webhook que assina SHIPPED recebe em < 10 s | TRANSCRICAO | [09:33] Marcos |
| PRD-AC-02 | docs/PRD.md | Critério de aceitação | Status não assinado não gera envio | TRANSCRICAO | [09:34] Bruno |
| PRD-AC-03 | docs/PRD.md | Critério de aceitação | URL http recusada | TRANSCRICAO | [09:23] Sofia |
| PRD-AC-04 | docs/PRD.md | Critério de aceitação | Secret devolvida na criação e assinatura verificável | TRANSCRICAO | [09:31] Marcos |
| PRD-AC-05 | docs/PRD.md | Critério de aceitação | Rotação: nova e anterior válidas por 24 h | TRANSCRICAO | [09:21] Sofia |
| PRD-AC-06 | docs/PRD.md | Critério de aceitação | Retentativas 1m/5m/30m/2h/12h e depois DLQ | TRANSCRICAO | [09:17] Larissa |
| PRD-AC-07 | docs/PRD.md | Critério de aceitação | Replay só por ADMIN e com mesmo identificador | TRANSCRICAO | [09:36] Larissa |
| PRD-AC-08 | docs/PRD.md | Critério de aceitação | Histórico com até 100 tentativas | TRANSCRICAO | [09:34] Marcos |
| PRD-AC-09 | docs/PRD.md | Critério de aceitação | Falha no registro do evento faz a mudança de status falhar | TRANSCRICAO | [09:40] Bruno |
| PRD-AC-10 | docs/PRD.md | Critério de aceitação | Itens fora de escopo não implementados | TRANSCRICAO | [09:48] Larissa |
| FDD-AC-01 | docs/FDD.md | Critério de aceite técnico | 1 linha PENDING por webhook interessado na mesma transação | TRANSCRICAO | [09:40] Bruno |
| FDD-AC-02 | docs/FDD.md | Critério de aceite técnico | Erro na outbox mantém status, histórico e estoque | TRANSCRICAO | [09:41] Diego |
| FDD-AC-03 | docs/FDD.md | Critério de aceite técnico | Status não assinado não cria linha | TRANSCRICAO | [09:34] Bruno |
| FDD-AC-04 | docs/FDD.md | Critério de aceite técnico | Entrega < 10 s em p95 | TRANSCRICAO | [09:02] Marcos |
| FDD-AC-05 | docs/FDD.md | Critério de aceite técnico | Agenda de retentativas e DLQ | TRANSCRICAO | [09:17] Diego |
| FDD-AC-06 | docs/FDD.md | Critério de aceite técnico | Timeout de 10 s gera retry | TRANSCRICAO | [09:42] Diego |
| FDD-AC-07 | docs/FDD.md | Critério de aceite técnico | X-Signature HMAC-SHA256 e rotação | TRANSCRICAO | [09:21] Sofia |
| FDD-AC-08 | docs/FDD.md | Critério de aceite técnico | X-Event-Id idêntico entre tentativas e replay | TRANSCRICAO | [09:25] Diego |
| FDD-AC-09 | docs/FDD.md | Critério de aceite técnico | URL http retorna 400 | TRANSCRICAO | [09:23] Sofia |
| FDD-AC-10 | docs/FDD.md | Critério de aceite técnico | Replay 403 para OPERATOR, 202 para ADMIN com log | TRANSCRICAO | [09:36] Sofia |
| FDD-AC-11 | docs/FDD.md | Critério de aceite técnico | Payload > 64 KB vai para DLQ sem envio | TRANSCRICAO | [09:24] Larissa |
| FDD-AC-12 | docs/FDD.md | Critério de aceite técnico | Deliveries limitado a 100 itens | TRANSCRICAO | [09:34] Marcos |
| FDD-AC-13 | docs/FDD.md | Critério de aceite técnico | Linhas PROCESSING reenviadas após restart (at-least-once) | TRANSCRICAO | [09:24] Diego |
| FDD-AC-14 | docs/FDD.md | Critério de aceite técnico | Suíte existente passa sem mudança de asserções | CODIGO | tests/orders.test.ts |
| FDD-ERR-04 | docs/FDD.md | Erro | WEBHOOK_CUSTOMER_NOT_FOUND (mesma checagem de customer usada em create) | CODIGO | src/modules/orders/order.service.ts |
| FDD-ERR-05 | docs/FDD.md | Erro | WEBHOOK_SECRET_ROTATION_IN_PROGRESS durante o grace period | TRANSCRICAO | [09:21] Sofia |
| FDD-ERR-06 | docs/FDD.md | Erro | WEBHOOK_DEAD_LETTER_NOT_FOUND no replay | TRANSCRICAO | [09:18] Diego |
| FDD-ERR-07 | docs/FDD.md | Erro | WEBHOOK_DEAD_LETTER_ALREADY_REPLAYED | TRANSCRICAO | [09:18] Diego |
| FDD-ERR-09 | docs/FDD.md | Erro | WEBHOOK_DELIVERY_HTTP_ERROR para resposta não-2xx | TRANSCRICAO | [09:15] Diego |
| FDD-ERR-10 | docs/FDD.md | Erro | WEBHOOK_DELIVERY_NETWORK_ERROR para cliente offline | TRANSCRICAO | [09:14] Larissa |
| FDD-ERR-14 | docs/FDD.md | Erro | WEBHOOK_INACTIVE para webhook desativado | TRANSCRICAO | [09:21] Bruno |
| FDD-ERR-15 | docs/FDD.md | Erro | WEBHOOK_NOT_FOUND no worker para webhook removido | TRANSCRICAO | [09:33] Bruno |
| FDD-PLAN-01 | docs/FDD.md | Plano | Ordem de implementação em 3 sprints | TRANSCRICAO | [09:46] Larissa |

## Resumo

| Indicador | Valor |
|---|---|
| Total de linhas | 230 |
| Fonte TRANSCRICAO | 193 (83%) |
| Fonte CODIGO | 37 (16%) |
