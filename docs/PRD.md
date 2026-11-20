# PRD: Sistema de Webhooks de Notificação de Pedidos

| Campo | Valor |
|---|---|
| **Produto** | Order Management System (OMS) |
| **PM responsável** | Marcos |
| **Tech Lead** | Larissa |
| **Status** | Aprovado em reunião técnica; em detalhamento |
| **Documentos relacionados** | [RFC](RFC.md) · [FDD](FDD.md) · [ADRs](adrs/README.md) · [Tracker](TRACKER.md) |

---

## 1. Resumo e contexto da feature

Clientes B2B do OMS vão poder **cadastrar endpoints HTTPS e receber automaticamente uma notificação sempre que o status de um pedido deles mudar** (pago, em processamento, enviado, entregue, cancelado). Cada notificação é assinada, identificada de forma única e retentada se o cliente estiver fora do ar. O cliente escolhe quais status quer receber e consulta o histórico de entregas pela API.

Hoje o OMS não envia nenhuma notificação externa. O cliente só descobre uma mudança consultando a API.

## 2. Problema e motivação

- **Problema:** Atlas Comercial, MaxDistribuição e Nova Cargo consultam `GET /orders` periodicamente para descobrir mudanças de status. A integração fica **lenta e cara** para eles ([09:00] Marcos).
- **Pedido formal:** os três clientes pediram notificação em tempo real na semana anterior à reunião ([09:00] Marcos).
- **Risco comercial:** a Atlas sinalizou que pode migrar para um concorrente se a entrega não sair até o fim do trimestre ([09:00] Marcos). Na reunião, a Atlas pediu a feature até o **fim de novembro** ([09:45] Marcos).
- **Definição de "tempo real" pelos clientes:** menos de 10 segundos, sem precisar atualizar manualmente ([09:02] Marcos).

## 3. Público-alvo e cenários de uso

**Público-alvo**
- Clientes B2B integrados via API, inicialmente Atlas Comercial, MaxDistribuição e Nova Cargo ([09:00] Marcos).
- Usuários do OMS que representam esses clientes e operam a API com JWT ([09:32] Marcos).
- Administradores do OMS, responsáveis por reprocessar entregas que falharam ([09:36] Sofia).

**Cenários**
| ID | Cenário |
|---|---|
| PRD-CEN-01 | A Atlas cadastra um endpoint e escolhe receber só `SHIPPED` e `DELIVERED`. Quando um pedido dela é enviado, o sistema dela é avisado em segundos e ela deixa de fazer polling ([09:33] Marcos). |
| PRD-CEN-02 | O endpoint da MaxDistribuição fica 2 h fora do ar por manutenção planejada. As notificações do período são reenviadas automaticamente quando ele volta ([09:16] Diego). |
| PRD-CEN-03 | Um cliente suspeita que uma notificação não chegou e consulta o histórico das últimas entregas, com status, resposta e tempo de resposta ([09:34] Marcos). |
| PRD-CEN-04 | Um cliente vazou a secret em log. Ele gera uma nova pela API e tem 24 h para atualizar seus sistemas sem perder notificações ([09:21] Sofia, [09:22] Diego). |
| PRD-CEN-05 | Um evento esgotou as tentativas. Um administrador o reprocessa depois que o cliente corrige o endpoint ([09:18] Diego). |

## 4. Objetivos e métricas de sucesso

| ID | Objetivo | Métrica | Meta | Origem |
|---|---|---|---|---|
| PRD-MET-01 | Notificar em "tempo real" | Tempo entre a mudança de status e a entrega ao cliente (p95, clientes saudáveis) | **< 10 s** | [09:02] Marcos |
| PRD-MET-02 | Latência previsível | Atraso máximo até a primeira tentativa de envio | **≤ 2 s** (intervalo de polling) | [09:10] Larissa |
| PRD-MET-03 | Reter os clientes que pediram a feature | Clientes com webhook ativo em produção | **3 de 3** (Atlas, MaxDistribuição, Nova Cargo) | [09:00] Marcos |
| PRD-MET-04 | Não perder notificações | Mudanças de status sem evento registrado para webhooks interessados | **0** | [09:40] Bruno |
| PRD-MET-05 | Tolerar indisponibilidade do cliente | Janela de retentativas antes de falha permanente | **~15 h** | [09:17] Diego |
| PRD-MET-06 | Entregar no prazo | Sprints até produção, com revisão de segurança | **3 sprints** | [09:46] Larissa |

Indicador de acompanhamento (sem meta definida na reunião): volume de chamadas `GET /orders` dos três clientes antes e depois da adoção, para confirmar a redução do polling ([09:00] Marcos).

## 5. Escopo

### 5.1 Incluso
- Notificação automática de mudança de status de pedido para endpoints HTTPS cadastrados.
- API para cadastrar, listar, editar e remover webhooks, com filtro por status.
- Assinatura de cada notificação e rotação de secret.
- Reenvio automático em caso de falha e fila de falhas permanentes com reprocessamento manual por administrador.
- Histórico de entregas por webhook.
- Documentação de integração no portal do desenvolvedor ([09:26] Marcos, [09:40] Marcos).

### 5.2 Fora de escopo
| ID | Item | Situação | Origem |
|---|---|---|---|
| PRD-OOS-01 | **E-mail ao cliente quando o webhook falha repetidamente** | Adiado para a próxima fase, após medir impacto | [09:37] Larissa |
| PRD-OOS-02 | **Dashboard visual** para o cliente ver seus webhooks | Descartado nesta fase; projeto separado do time de frontend | [09:40] Larissa |
| PRD-OOS-03 | **Rate limiting de envio** por cliente | Não entra; observar e decidir depois | [09:39] Diego, [09:39] Larissa |
| PRD-OOS-04 | Webhooks de entrada (cliente enviando para o OMS) | Descartado: só saída | [09:02] Marcos |
| PRD-OOS-05 | Garantia de ordem global entre pedidos | Não oferecida; só ordem por pedido | [09:13] Larissa, [09:14] Marcos |
| PRD-OOS-06 | Garantia de entrega exatamente uma vez | Descartado; o cliente deduplica | [09:25] Diego |
| PRD-OOS-07 | Arquivamento de notificações antigas | Fora desta feature | [09:08] Diego |
| PRD-OOS-08 | Itens do pedido na notificação | Não enviados; cliente consulta o pedido | [09:43] Diego |

## 6. Requisitos funcionais

| ID | Requisito | Origem |
|---|---|---|
| PRD-FR-01 | O cliente cadastra um webhook informando URL e lista de status de interesse. A plataforma **gera a secret** e a devolve na criação. | [09:31] Marcos |
| PRD-FR-02 | O cliente lista os webhooks de um customer. | [09:33] Bruno |
| PRD-FR-03 | O cliente edita um webhook (URL, status de interesse, ativo/inativo). | [09:33] Bruno |
| PRD-FR-04 | O cliente remove um webhook. | [09:33] Bruno |
| PRD-FR-05 | Cada webhook recebe apenas os status escolhidos; se nenhum webhook do cliente quer aquele status, nada é enviado. | [09:33] Marcos, [09:34] Bruno |
| PRD-FR-06 | Toda mudança de status de pedido gera notificação para os webhooks interessados do cliente dono do pedido. | [09:00] Marcos, [09:40] Bruno |
| PRD-FR-07 | Cada notificação é assinada com uma secret exclusiva daquele webhook, para o cliente verificar origem e integridade. | [09:19] Sofia, [09:21] Sofia |
| PRD-FR-08 | O cliente pode pedir uma nova secret; a anterior continua válida por 24 h. | [09:21] Sofia |
| PRD-FR-09 | O cliente consulta as últimas 100 entregas de um webhook, com sucesso/falha, conteúdo enviado, resposta recebida e tempo de resposta. | [09:34] Marcos |
| PRD-FR-10 | Notificações que falham são reenviadas automaticamente em intervalos crescentes (1 min, 5 min, 30 min, 2 h, 12 h). | [09:15] Diego, [09:17] Larissa |
| PRD-FR-11 | Notificações que esgotam as tentativas ficam guardadas com o motivo da falha, e um administrador pode reprocessá-las. O reprocessamento é registrado para auditoria. | [09:18] Diego, [09:36] Sofia |
| PRD-FR-12 | Cada notificação tem um identificador único e estável, para o cliente descartar duplicatas. | [09:25] Diego |
| PRD-FR-13 | A notificação identifica qual webhook cadastrado a gerou e o momento do envio. | [09:44] Sofia, [09:44] Diego |
| PRD-FR-14 | A notificação contém o essencial do pedido: identificador, número, status anterior e novo, cliente, valor total e data da mudança. Sem itens. | [09:43] Diego |

## 7. Requisitos não funcionais

| ID | Categoria | Requisito | Origem |
|---|---|---|---|
| PRD-NFR-01 | Desempenho | Entrega em menos de 10 s; até 2 s de espera antes do primeiro envio | [09:02] Marcos, [09:10] Larissa |
| PRD-NFR-02 | Consistência | Se o status mudou, a notificação existe; se a mudança falhou, nenhuma notificação é gerada | [09:06] Diego, [09:40] Bruno |
| PRD-NFR-03 | Isolamento | Um cliente lento ou fora do ar não pode atrasar nem impedir mudanças de status | [09:04] Bruno |
| PRD-NFR-04 | Segurança | Apenas URLs `https`; outras são recusadas | [09:23] Sofia |
| PRD-NFR-05 | Segurança | Assinatura HMAC-SHA256; secret por webhook, nunca global | [09:20] Sofia, [09:21] Sofia |
| PRD-NFR-06 | Limites | Notificação com mais de 64 KB não é enviada (erro, sem truncar) | [09:23] Sofia, [09:24] Larissa |
| PRD-NFR-07 | Resiliência | Cliente que não responde em 10 s conta como falha e entra em retry | [09:42] Diego |
| PRD-NFR-08 | Entrega | Pelo menos uma vez (at-least-once); duplicatas possíveis | [09:24] Diego |
| PRD-NFR-09 | Ordem | Ordem garantida apenas por pedido, não global | [09:13] Larissa |
| PRD-NFR-10 | Autorização | Reprocessamento só por role ADMIN; gestão de webhooks por qualquer usuário autenticado | [09:36] Sofia, [09:37] Sofia |
| PRD-NFR-11 | Operação | O processo de envio é independente da API: reiniciar a API não interrompe envios | [09:11] Diego |
| PRD-NFR-12 | Manutenibilidade | Seguir os padrões existentes do projeto (módulos, erros, logs) | [09:30] Larissa |

## 8. Decisões e trade-offs principais

Detalhes de cada decisão nos ADRs; aqui fica apenas o impacto para o produto.

| Decisão | Impacto para o produto / cliente | ADR |
|---|---|---|
| Registrar o evento junto com a mudança de status e enviar depois, por um processo separado | Nenhuma notificação perdida, ao custo de até 2 s de atraso | [ADR-001](adrs/ADR-001-outbox-no-mysql.md), [ADR-002](adrs/ADR-002-worker-separado-em-polling.md) |
| 5 retentativas ao longo de ~15 h, depois fila de falhas | Cobre manutenções longas; quem ficar fora mais tempo depende de reprocessamento manual | [ADR-003](adrs/ADR-003-retry-com-backoff-e-dlq.md) |
| Assinatura com secret por webhook e rotação com 24 h | Segurança padrão de mercado; o cliente precisa implementar a verificação | [ADR-004](adrs/ADR-004-autenticacao-hmac-sha256-secret-por-endpoint.md) |
| Entrega pelo menos uma vez | O cliente precisa descartar duplicatas pelo `X-Event-Id`; documentado com destaque no portal | [ADR-005](adrs/ADR-005-entrega-at-least-once-com-x-event-id.md) |
| Notificação com retrato do pedido no momento da mudança, sem itens | Conteúdo fiel ao momento; detalhes via consulta ao pedido | [ADR-007](adrs/ADR-007-payload-snapshot-e-filtro-na-insercao.md) |
| Construir dentro do OMS com os padrões atuais | Entrega mais rápida, sem nova infraestrutura | [ADR-006](adrs/ADR-006-reuso-dos-padroes-do-projeto.md) |

## 9. Dependências

| Tipo | Dependência | Origem |
|---|---|---|
| Organizacional | Revisão de segurança da Sofia, com pelo menos 2 dias úteis, antes do deploy | [09:46] Sofia, [09:49] Sofia |
| Organizacional | Documentação no portal do desenvolvedor (deduplicação, verificação da assinatura, integração via API) feita pelo PM | [09:26] Marcos, [09:40] Marcos |
| Organizacional | Confirmação do prazo com a Atlas | [09:47] Marcos |
| Técnica | Mudança de status de pedido (`src/modules/orders/order.service.ts`) e banco MySQL existente | [09:40] Bruno |
| Técnica | Novo processo de envio implantado junto da API | [09:11] Larissa |
| Clientes | Cada cliente precisa expor um endpoint HTTPS, validar a assinatura e deduplicar eventos | [09:23] Sofia, [09:25] Diego |

## 10. Riscos e mitigação

| ID | Risco | Probabilidade | Impacto | Mitigação |
|---|---|---|---|---|
| PRD-RISK-01 | **Perda da Atlas por atraso** na entrega | Média | Alto | Estimativa de 3 sprints confirmada em reunião; o PM alinha o prazo com a Atlas ([09:46] Larissa, [09:47] Marcos) |
| PRD-RISK-02 | **Clientes processando notificações duplicadas** por não deduplicarem | Média | Médio | `X-Event-Id` estável; destaque no portal do desenvolvedor ([09:26] Marcos) |
| PRD-RISK-03 | **Vazamento de secret** de um cliente | Baixa | Alto | Secret por webhook e rotação com 24 h ([09:21] Sofia); caso real já ocorreu ([09:22] Diego) |
| PRD-RISK-04 | **Volume alto de notificações** para um cliente (ex.: 50 em 1 min) | Média | Médio | Monitorar volume por cliente; decidir rate limiting com dados ([09:38] Diego, [09:39] Larissa) |
| PRD-RISK-05 | **Cliente fora do ar por mais de ~15 h** perde notificações automáticas | Baixa | Médio | Fila de falhas com reprocessamento por admin ([09:18] Diego); PM considera aceitável ([09:17] Marcos) |
| PRD-RISK-06 | **Notificações de um mesmo pedido fora de ordem** após retentativas | Média | Baixo | Notificação traz status anterior, novo e data; orientar no portal (ver R-02 no [FDD](FDD.md#11-riscos-e-mitigação)) |

## 11. Critérios de aceitação

| ID | Critério |
|---|---|
| PRD-AC-01 | Dado um webhook que assina `SHIPPED`, quando um pedido do cliente muda para `SHIPPED`, o endpoint recebe a notificação em menos de 10 s. |
| PRD-AC-02 | Dado um webhook que **não** assina `PAID`, quando um pedido muda para `PAID`, nenhuma notificação é enviada a ele. |
| PRD-AC-03 | Cadastrar webhook com URL `http://` é recusado com erro de validação. |
| PRD-AC-04 | A resposta de criação traz a secret, e a assinatura de cada notificação é verificável com ela. |
| PRD-AC-05 | Depois de rotacionar a secret, notificações são verificáveis com a nova e com a anterior por 24 h, e só com a nova depois disso. |
| PRD-AC-06 | Com o endpoint indisponível, a notificação é reenviada em 1 min, 5 min, 30 min, 2 h e 12 h, e depois fica na fila de falhas com o motivo. |
| PRD-AC-07 | Um administrador reprocessa uma notificação da fila de falhas, e ela é reenviada com o mesmo identificador. Um usuário sem role ADMIN recebe acesso negado. |
| PRD-AC-08 | O histórico de entregas mostra até 100 tentativas mais recentes com sucesso/falha, conteúdo, resposta e tempo. |
| PRD-AC-09 | Se o registro da notificação falhar, a mudança de status também falha e o pedido permanece no status anterior. |
| PRD-AC-10 | Nenhum item em "Fora de escopo" está implementado. |

## 12. Estratégia de testes e validação

- **Testes automatizados de integração** no padrão de `tests/orders.test.ts` (Vitest + Supertest + MySQL), cobrindo CRUD, filtro de status, atomicidade com a mudança de status e autorização do reprocessamento.
- **Testes do processo de envio** contra um servidor HTTP local que simula cliente saudável, lento (> 10 s), com erro 5xx e fora do ar, com relógio simulado para validar os intervalos de retentativa.
- **Testes de assinatura** com vetores fixos de secret e payload, incluindo o período de rotação.
- **Regressão:** a suíte atual de pedidos continua passando sem alteração.
- **Revisão de segurança** da Sofia antes do deploy ([09:46] Sofia).
- **Validação com clientes:** homologação com Atlas, MaxDistribuição e Nova Cargo usando o portal do desenvolvedor; acompanhar PRD-MET-01 e PRD-MET-03 após o go-live.
- Critérios técnicos detalhados no [FDD, seção 10](FDD.md#10-critérios-de-aceite-técnicos).
