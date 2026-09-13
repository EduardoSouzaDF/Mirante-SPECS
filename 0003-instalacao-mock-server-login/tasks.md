# Tarefas — Spec 0003

## Backend (Mock Server)
- [x] Instalar `@mocks-server/main` (v3.12.0) e criar `package.json` no repo
      backend.
- [x] Criar `mocks.config.js` com porta 3100 e delay lendo
      `MOCK_DELAY_SECONDS` (env) — com mini-loader do `.env` no próprio config.
- [x] Criar fixture da entidade Usuário (admin@mirante.com.br / 123456) —
      nome, email, senha — em `src/usuarios.js`.
- [x] Criar route `POST /api/auth/login`: variant `login-sucesso`
      (middleware valida credenciais e devolve token fake + user) e variants
      `email-nao-encontrado` (401), `senha-incorreta` (401), `erro-servidor`
      (500).
- [x] Criar route `POST /api/auth/reset-password` (variant sempre 200/OK).
- [x] Criar route `GET /api/auth/me` (variant sucesso — valida Bearer token).
- [x] Configurar collections: `base` + `login-email-nao-encontrado`,
      `login-senha-incorreta`, `erro-servidor` (`mocks/collections.js`).
- [x] Criar `.env` / `.env.example` (`MOCK_DELAY_SECONDS=2`) e README do
      backend.
- [x] Subir e validar endpoints com curl: sucesso (200 em ~2,1s), 401 email,
      401 senha, reset 200, /me com e sem token, delay observável.

## Frontend (Angular 17)
- [x] Criar `auth.service` (login, reset, logout, signals) e
      `token-storage.service` (cookie, com "manter conectado" = 30 dias) em
      `core/services`.
- [x] Criar `auth.interceptor` (Authorization Bearer) e `loading.interceptor`
      em `core/interceptors`; providers no `app.config`.
- [x] Criar `loading.service` (contador) e componente `loading-indicator`
      (overlay/spinner minimalista com a paleta) em `shared`.
- [x] Criar tela de login (`features/auth/login`) com Reactive Forms (email,
      senha, manter conectado), mensagens de erro da API e form de reset de
      senha (feedback OK).
- [x] Criar guard (`authGuard`) e rotas `/login` + `/dashboard` (layout) no
      `app.routes`.
- [x] Criar DashboardComponent (boas-vindas + cards com pipes pt-BR).
- [x] Criar `sidebar` (menu lateral com item Dashboard + sair) e `breadcrumb`
      (Início / Dashboard, control flow @for) em `shared`.
- [x] Estilizar tudo com os design tokens da paleta.
- [x] Escrever testes (token-storage, auth.service com HttpTestingController)
      e validar: `ng build` ✅, `ng test` 15/15 ✅, `ng serve` + fluxo via
      proxy (login → token → /me) ✅.

## Transversais
- [x] Criar branch `feature/0003-instalacao-mock-server-login` nos 3 repos
      (via new_spec.sh).
- [x] Atualizar INDEX.md/STATE.md conforme o avanço.
- [x] Ao concluir: commits, push e PRs (frontend, backend e specs).
