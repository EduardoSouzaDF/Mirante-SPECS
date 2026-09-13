# Estado global do SDD

- Spec ativa: 0004 — Consultar Lotes
- Fase atual: em-testes (backend + frontend implementados e commitados; aguardando validação visual do usuário antes de push/PR)
- Última spec concluída: 0003 — Instalação Mock Server - Login
- Próxima ação sugerida: usuário validar a tela `/lotes` rodando mock + ng serve; depois push + PR nos 3 repos

## Histórico de retomada

- 2026-09-13 — spec 0004 implementada (backend: src/lotes.js + mocks/routes/lotes.js + collections.js; frontend: breadcrumb refatorado, range-field, filter-panel, lote-table, lote-actions, lote-detail-dialog, LoteFacade RxJS, LotesPageComponent + rota); testes unitários (30/30) e e2e Playwright (6/6) verdes; validação visual via screenshot comparada ao protótipo; 2 bugs reais encontrados e corrigidos durante a implementação (breadcrumb quebrando navegação pós-login; facade disparando busca duplicada em pesquisar()) — detalhes em `0004-consultar-lotes/state.md`
- 2026-09-13 — spec 0004 iniciada (Consultar Lotes); spec/plan/tasks/tests preenchidos a partir do plano do usuário; protótipo `docs/consulta.png` copiado para `specs/0004-consultar-lotes/prototipos/`; branch feature/0004-consultar-lotes criada nos 3 repos (frontend/backend a partir de main; specs a partir do estado atual, pois main do repo specs ainda não tem os PRs 0002/0003 mesclados)
- 2026-09-13 — spec 0003 validada pelo usuário e PR já feito; spec encerrada
- 2026-09-13 — spec 0003 em validação visual (Playwright): PrimeFlex + responsividade mobile aplicados (sidebar = drawer no mobile); PRs só após aprovação do usuário
- 2026-09-13 — spec 0003 concluída (Instalação Mock Server - Login); backend com @mocks-server/main (login/reset/me, delay via env) e frontend com login/cookie/interceptors/loader/sidebar/breadcrumb; commits, push e PRs nos 3 repos
- 2026-09-13 — spec 0003 iniciada (Instalação Mock Server - Login); spec/plan/tasks/tests preenchidos; implementação do backend em andamento
- 2026-09-13 — spec 0002 concluída (Instalação FrontEnd); commits locais, push e PRs abertos (frontend e specs; backend sem alterações); fluxo SDD atualizado: conclusão de spec passa a fazer commits + push + PR
