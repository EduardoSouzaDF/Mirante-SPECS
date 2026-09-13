# Estado global do SDD

- Spec ativa: 0004 — Consultar Lotes
- Fase atual: planejamento (spec/plan/tasks/tests preenchidos; iniciando implementação backend)
- Última spec concluída: 0003 — Instalação Mock Server - Login
- Próxima ação sugerida: implementar backend (src/lotes.js, mocks/routes/lotes.js, collections.js)

## Histórico de retomada

- 2026-09-13 — spec 0004 iniciada (Consultar Lotes); spec/plan/tasks/tests preenchidos a partir do plano do usuário; protótipo `docs/consulta.png` copiado para `specs/0004-consultar-lotes/prototipos/`; branch feature/0004-consultar-lotes criada nos 3 repos (frontend/backend a partir de main; specs a partir do estado atual, pois main do repo specs ainda não tem os PRs 0002/0003 mesclados)
- 2026-09-13 — spec 0003 validada pelo usuário e PR já feito; spec encerrada
- 2026-09-13 — spec 0003 em validação visual (Playwright): PrimeFlex + responsividade mobile aplicados (sidebar = drawer no mobile); PRs só após aprovação do usuário
- 2026-09-13 — spec 0003 concluída (Instalação Mock Server - Login); backend com @mocks-server/main (login/reset/me, delay via env) e frontend com login/cookie/interceptors/loader/sidebar/breadcrumb; commits, push e PRs nos 3 repos
- 2026-09-13 — spec 0003 iniciada (Instalação Mock Server - Login); spec/plan/tasks/tests preenchidos; implementação do backend em andamento
- 2026-09-13 — spec 0002 concluída (Instalação FrontEnd); commits locais, push e PRs abertos (frontend e specs; backend sem alterações); fluxo SDD atualizado: conclusão de spec passa a fazer commits + push + PR
