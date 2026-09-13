# Estado global do SDD

- Spec ativa: 0003 — Instalação Mock Server - Login
- Fase atual: em-testes (validação visual com Playwright)
- Última spec concluída: 0002 — Instalação FrontEnd
- Próxima ação sugerida: validar telas pelo Playwright (npm run e2e:open), aplicar correções informadas pelo usuário e só então atualizar os PRs da 0003

## Histórico de retomada

- 2026-09-13 — spec 0003 em validação visual (Playwright): PrimeFlex + responsividade mobile aplicados (sidebar = drawer no mobile); PRs só após aprovação do usuário
- 2026-09-13 — spec 0003 concluída (Instalação Mock Server - Login); backend com @mocks-server/main (login/reset/me, delay via env) e frontend com login/cookie/interceptors/loader/sidebar/breadcrumb; commits, push e PRs nos 3 repos
- 2026-09-13 — spec 0003 iniciada (Instalação Mock Server - Login); spec/plan/tasks/tests preenchidos; implementação do backend em andamento
- 2026-09-13 — spec 0002 concluída (Instalação FrontEnd); commits locais, push e PRs abertos (frontend e specs; backend sem alterações); fluxo SDD atualizado: conclusão de spec passa a fazer commits + push + PR
