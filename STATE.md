# Estado global do SDD

- Spec ativa: nenhuma (aguardando nova demanda)
- Fase atual: -
- Última spec concluída: 0004 — Consultar Lotes
- Próxima ação sugerida: aguardar detalhes da próxima spec com o usuário

## Histórico de retomada

- 2026-09-13 — spec 0004 concluída após validação interativa completa com o usuário (Playwright headed, ajustes passados ao vivo): filtros (Enter, faixas, seleção real corrigida), ações em massa com filtro por situação e chamada única, modal de escopo (página/todos), FilterPanelComponent e GenericTableComponent generalizados (conteúdo/colunas projetados por quem usa), coluna Data/Hora Situação, menu lateral, responsividade mobile corrigida, seed com 24 lotes/7 instituições; 8 bugs reais encontrados e corrigidos (breadcrumb pós-login, facade double-fetch em 2 níveis, colisão de nome de evento no rangeChange, caractere solto no campo de moeda, CSS perdido no generic-table, grid blowout mobile, sidebar cortando); build + 41 testes unitários + 6 e2e verdes; docs (spec/plan/tasks/tests/state) atualizadas; commits + push + PR nos 3 repos
- 2026-09-13 — spec 0004 implementada (backend: src/lotes.js + mocks/routes/lotes.js + collections.js; frontend: breadcrumb refatorado, range-field, filter-panel, lote-table, lote-actions, lote-detail-dialog, LoteFacade RxJS, LotesPageComponent + rota); testes unitários (30/30) e e2e Playwright (6/6) verdes; validação visual via screenshot comparada ao protótipo; 2 bugs reais encontrados e corrigidos durante a implementação (breadcrumb quebrando navegação pós-login; facade disparando busca duplicada em pesquisar()) — detalhes em `0004-consultar-lotes/state.md`
- 2026-09-13 — spec 0004 iniciada (Consultar Lotes); spec/plan/tasks/tests preenchidos a partir do plano do usuário; protótipo `docs/consulta.png` copiado para `specs/0004-consultar-lotes/prototipos/`; branch feature/0004-consultar-lotes criada nos 3 repos (frontend/backend a partir de main; specs a partir do estado atual, pois main do repo specs ainda não tem os PRs 0002/0003 mesclados)
- 2026-09-13 — spec 0003 validada pelo usuário e PR já feito; spec encerrada
- 2026-09-13 — spec 0003 em validação visual (Playwright): PrimeFlex + responsividade mobile aplicados (sidebar = drawer no mobile); PRs só após aprovação do usuário
- 2026-09-13 — spec 0003 concluída (Instalação Mock Server - Login); backend com @mocks-server/main (login/reset/me, delay via env) e frontend com login/cookie/interceptors/loader/sidebar/breadcrumb; commits, push e PRs nos 3 repos
- 2026-09-13 — spec 0003 iniciada (Instalação Mock Server - Login); spec/plan/tasks/tests preenchidos; implementação do backend em andamento
- 2026-09-13 — spec 0002 concluída (Instalação FrontEnd); commits locais, push e PRs abertos (frontend e specs; backend sem alterações); fluxo SDD atualizado: conclusão de spec passa a fazer commits + push + PR
