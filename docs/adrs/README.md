# Architectural Decision Records

Decisões arquiteturais da feature **Sistema de Webhooks de Notificação de Pedidos**, no formato MADR simplificado (Status, Contexto, Decisão, Alternativas Consideradas, Consequências). Cada ADR cita a origem na `TRANSCRICAO.md` no formato `[hh:mm] Nome`.

| ADR | Decisão | Status |
|---|---|---|
| [ADR-001](ADR-001-outbox-no-mysql.md) | Padrão Outbox no MySQL existente | Aceito |
| [ADR-002](ADR-002-worker-separado-em-polling.md) | Worker em processo separado, polling de 2 s, single-worker | Aceito |
| [ADR-003](ADR-003-retry-com-backoff-e-dlq.md) | Retry 1m/5m/30m/2h/12h e DLQ em tabela separada | Aceito |
| [ADR-004](ADR-004-autenticacao-hmac-sha256-secret-por-endpoint.md) | HMAC-SHA256, secret por endpoint, rotação com 24 h de grace | Aceito (revisão de segurança pendente) |
| [ADR-005](ADR-005-entrega-at-least-once-com-x-event-id.md) | At-least-once com `X-Event-Id` | Aceito |
| [ADR-006](ADR-006-reuso-dos-padroes-do-projeto.md) | Reuso dos padrões do projeto (módulo, AppError, Pino, `WEBHOOK_*`) | Aceito |
| [ADR-007](ADR-007-payload-snapshot-e-filtro-na-insercao.md) | Payload snapshot e filtro de eventos na inserção | Aceito |
