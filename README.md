# Da Reunião ao Documento: Design Docs do Sistema de Webhooks

> Entrega do desafio **"Da Reunião ao Documento: Design Docs Gerados por IA"** do MBA em Engenharia de Software com IA (Full Cycle). Enunciado original: [devfullcycle/mba-ia-desafio-design-docs-com-ia](https://github.com/devfullcycle/mba-ia-desafio-design-docs-com-ia).

## Sobre o desafio

Uma empresa que opera um Order Management System (Node.js + TypeScript + Prisma/MySQL) decidiu, numa reunião de ~55 minutos, construir um **sistema de webhooks outbound** para avisar clientes B2B quando o status dos pedidos muda. A única fonte do que foi decidido era a transcrição da call (`TRANSCRICAO.md`). A tarefa foi transformar essa conversa, junto com o código existente, num pacote de design docs acionável: PRD, RFC, FDD, ADRs e um tracker de rastreabilidade.

O desafio não era só escrever. Era **filtrar**: separar decisão fechada de sugestão, requisito de ideia descartada, e ancorar cada afirmação numa fala com timestamp ou num arquivo real do repositório, sem deixar a IA inventar nada. Nenhum arquivo de código da aplicação foi alterado.

## Ferramentas de IA utilizadas

| Ferramenta | Papel |
|---|---|
| **Claude Code (Claude Opus 5.5)** | Ferramenta principal. Leu o repositório e a transcrição, extraiu decisões e itens descartados, gerou todos os documentos, fez a revisão cruzada com o código e rodou scripts de validação. |
| **Material do curso** (`design-docs/`: prompt de FDD, exemplo de PRD; plugin `adrs-management`) | Base de formato: o esqueleto de seções do FDD e do PRD e a estrutura dos ADRs seguem esses modelos, adaptados ao contexto. |
| **Scripts Python gerados pela IA** | Validação anti-alucinação: conferem cada `[hh:mm] Nome` contra a transcrição, cada caminho de arquivo contra o repositório e a cobertura de IDs no tracker. |

## Workflow adotado

1. **Contextualização.** Leitura integral da transcrição e dos arquivos centrais do código: `order.service.ts`, `order.status.ts`, `shared/errors/*`, middlewares, `app.ts`, `server.ts`, `schema.prisma`, `package.json` e os testes. O objetivo era saber o que existe de fato antes de citar qualquer arquivo.
2. **Extração dirigida.** Um prompt específico classificou cada trecho da reunião em decisão fechada, requisito, restrição, alternativa descartada, item adiado ou dúvida não resolvida, sempre com `[hh:mm] Nome`.
3. **ADRs primeiro** (7 arquivos). As seis decisões principais da reunião, mais uma secundária com trade-off próprio (payload snapshot + filtro na inserção).
4. **RFC.** Consolidação em nível de arquitetura, linkando os ADRs, com 6 alternativas descartadas e 8 questões em aberto. O detalhe de implementação ficou de fora de propósito.
5. **FDD.** Modelo de dados, fluxos, 9 contratos, matriz `WEBHOOK_*`, resiliência, observabilidade e a seção de integração com 17 arquivos reais. Tudo o que não foi decidido na reunião aparece como hipótese `H-nn`.
6. **PRD por último.** Consolidação em linguagem de produto, sem repetir detalhe técnico.
7. **Tracker.** Gerado a partir de uma lista estruturada e validado por script.
8. **Revisão final** contra a checklist de critérios de aceite.

Regra usada em todas as etapas: **cada documento opera numa altura**. O PRD não fala de tabelas, a RFC não traz payloads e o FDD não re-explica o porquê das decisões (ele linka os ADRs).

## Prompts customizados

**1. Extração dirigida da transcrição** (adaptado do prompt de entrevista de PRD do curso, trocando entrevista por extração com evidência):

```text
Você é um analista técnico. Leia TRANSCRICAO.md inteira e classifique CADA trecho relevante
em exatamente uma categoria:
  DECISAO_FECHADA | REQUISITO_FUNCIONAL | REQUISITO_NAO_FUNCIONAL | RESTRICAO |
  ALTERNATIVA_DESCARTADA (com o motivo do descarte) | ADIADO_FORA_DE_ESCOPO |
  PONTO_NAO_DECIDIDO
Regras:
- Cite sempre a origem no formato [hh:mm] Nome. Sem origem, o item não existe.
- Se uma fala posterior contradiz ou corrige uma anterior, registre as duas e marque qual vale
  (ex.: customer_id "implícito do JWT" em 09:31 foi revertido em 09:32).
- Se o resumo final (09:48) diverge do que foi discutido antes, sinalize como PONTO_NAO_DECIDIDO.
- Não complete lacunas com "boas práticas". Lacuna vira PONTO_NAO_DECIDIDO.
Saída: tabela markdown | categoria | resumo de 1 linha | [hh:mm] Nome |.
```

**2. Geração do FDD ancorado no código** (adaptado do "Prompt para geração de um FDD" do curso, sem o modo entrevista):

```text
Gere docs/FDD.md seguindo o esqueleto de FDD do curso (contexto, objetivos, escopo, fluxos,
contratos, erros, observabilidade, dependências, critérios de aceite, riscos) MAIS a seção
"Integração com o sistema existente".
Fontes permitidas: TRANSCRICAO.md, docs/adrs/*.md e o código em src/, prisma/, tests/.
Regras:
- Não repita o "porquê" das decisões: linke o ADR correspondente.
- Todo caminho de arquivo citado deve existir. Arquivos NOVOS só sob src/modules/webhooks/,
  src/worker.ts e tests/webhooks.test.ts, e devem estar marcados como novos.
- Antes de propor um código de erro, leia src/middlewares/validate.middleware.ts e
  src/middlewares/error.middleware.ts e diga qual código o cliente REALMENTE recebe.
- Para cada endpoint: método, caminho real (o app monta em /api/v1), request e response
  de exemplo, status codes e semântica.
- Qualquer valor não dito na reunião (tamanho de lote, formato de header na rotação,
  caminho da rotação etc.) deve sair marcado como hipótese H-nn e listado numa seção final.
- Observabilidade: não invente stack. Verifique package.json e diga o que existe hoje.
```

**3. Auditoria anti-alucinação** (prompt de revisão + script que a IA gerou e rodou):

```text
Audite docs/ contra as fontes. Para cada documento:
1. Liste toda citação [hh:mm] Nome que não corresponde a uma fala real desse falante nesse minuto.
2. Liste todo caminho de arquivo citado que não existe no repositório (ignorando os
   arquivos novos planejados).
3. Liste todo ID (PRD-*, RFC-*, FDD-*) presente nos documentos e ausente do TRACKER.md.
4. Procure itens descartados na reunião (e-mail, dashboard, rate limiting, exactly-once,
   ordem global, Redis) aparecendo como requisito ou como coisa a implementar.
Responda só com as violações. Não corrija nada ainda.
```

```python
spk = set(re.findall(r'^\[(\d\d:\d\d)\] (\w+):', transcricao, re.M))
bad = [c for c in re.findall(r'\[(\d\d:\d\d)\] (\w+)', doc) if c not in spk]
```

## Iterações e ajustes

Foram **4 ciclos principais**: extração, geração, revisão crítica contra código e transcrição, e auditoria automatizada. Os ajustes concretos:

1. **Contagem de tentativas ambígua.** A primeira leitura gerava "5 tentativas no total". Somando os intervalos 1m+5m+30m+2h+12h dá ~14,6 h, que bate com "quase 15 horas entre primeira falha e última tentativa" ([09:17] Diego). Isso só fecha com **envio inicial + 5 retentativas**, mas o resumo ([09:48] Larissa) diz "total 5 tentativas". Em vez de escolher em silêncio, o FDD adota a leitura que fecha a conta, e a divergência virou a questão em aberto **RFC-Q-06**.
2. **Limite de 64 KB no lugar errado.** A versão inicial validava o tamanho em `publishWebhookEvent`, dentro da transação. Na revisão ficou claro que isso faria uma mudança de status sofrer rollback por causa de um webhook, o que contradiz [09:04] Bruno ("dá rollback na mudança de status? Não dá."). A checagem foi movida para o worker, que manda direto para a DLQ com `WEBHOOK_PAYLOAD_TOO_LARGE`.
3. **`customer_id` do JWT.** A extração inicial pegou "customer_id implícito do JWT" ([09:31] Marcos), mas a fala foi revertida um minuto depois ([09:32] Larissa). A posição final (body ou path) não foi decidida, então virou **RFC-Q-04**, e o FDD marca sua escolha como proposta.
4. **Código de erro que o cliente nunca veria.** O primeiro FDD dizia que URL `http` retornaria `400 WEBHOOK_INVALID_URL`. Lendo `src/middlewares/validate.middleware.ts`, todo `ZodError` vira `VALIDATION_ERROR`. Como a Sofia pediu a validação no schema Zod ([09:23] Sofia), a matriz de erros passou a descrever o comportamento real: o código `WEBHOOK_*` vai em `details[].message`. A alternativa ficou registrada.
5. **Observabilidade inventada.** O rascunho citava Prometheus e OpenTelemetry. `package.json` só tem Pino. A seção foi reescrita: métricas derivadas de logs e de queries nas tabelas, tracing por correlação de `eventId`, e nenhuma dependência nova, coerente com [09:29] Bruno.
6. **Evento na criação do pedido.** A IA incluiu `OrderService.create` como gerador de evento. A reunião só fala de `changeStatus` ([09:40] Bruno), e `order.status.ts` mostra que nenhuma transição leva a `PENDING`. O item virou exclusão explícita, e `PENDING` passou a ser recusado no filtro de eventos.
7. **Inconsistência entre ADR-007 e FDD.** O ADR dizia que desativar um webhook não afetava eventos enfileirados, e o FDD mandava esses eventos para a DLQ. O ADR foi corrigido para delegar o tratamento ao FDD.
8. **Cobertura do tracker abaixo da meta.** A auditoria automática mostrou **75%** de cobertura de IDs: faltavam critérios de aceite e 8 erros da matriz. Foram adicionadas 32 linhas e a cobertura chegou a **100%** (130/130 IDs). Também confirmou 0 citações inválidas e 0 caminhos inexistentes.
9. **Detalhe não decidido tratado como fato.** Lote de 10, caminho `/rotate-secret`, duas assinaturas no `X-Signature` durante a rotação, recuperação de linhas `PROCESSING` e outros detalhes não foram decididos na reunião. Esses 14 itens foram rotulados `H-01` a `H-14` no FDD e ficaram fora do tracker de propósito.

## Como navegar a entrega

```
.
├── README.md                  ← este arquivo (processo)
├── TRANSCRICAO.md             ← fonte (inalterada)
└── docs/
    ├── PRD.md                 ← por que e o quê (produto)
    ├── RFC.md                 ← proposta técnica, alternativas, questões em aberto
    ├── adrs/
    │   ├── README.md          ← índice
    │   ├── ADR-001-outbox-no-mysql.md
    │   ├── ADR-002-worker-separado-em-polling.md
    │   ├── ADR-003-retry-com-backoff-e-dlq.md
    │   ├── ADR-004-autenticacao-hmac-sha256-secret-por-endpoint.md
    │   ├── ADR-005-entrega-at-least-once-com-x-event-id.md
    │   ├── ADR-006-reuso-dos-padroes-do-projeto.md
    │   └── ADR-007-payload-snapshot-e-filtro-na-insercao.md
    ├── FDD.md                 ← como construir (contratos, fluxos, erros, integração)
    └── TRACKER.md             ← origem de cada item (230 linhas)
```

**Ordem de leitura sugerida:**
1. [PRD](docs/PRD.md): entender o problema e o escopo.
2. [RFC](docs/RFC.md): visão da solução e o que ainda está em aberto.
3. [ADRs](docs/adrs/README.md): o porquê de cada decisão.
4. [FDD](docs/FDD.md): para quem vai implementar. Comece pela seção 12 (integração) e pela 13 (hipóteses).
5. [TRACKER](docs/TRACKER.md): para auditar a origem de qualquer item.
