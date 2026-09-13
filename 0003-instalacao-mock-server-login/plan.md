# Plano — Spec 0003

## Abordagem técnica

No repo **backend**, instalar `@mocks-server/main` v3 com a estrutura de
mocks (routes/variants/collections): rotas de autenticação (login e reset de
senha) sobre a entidade Usuário (fixture), token fake codificado e delay de
resposta vindo do env (segundos). No repo **frontend**, implementar a tela de
login com Reactive Forms, guard de rota, cookie de token, interceptor de
Authorization, loader minimalista, menu lateral e breadcrumbs.

## Frontend (Angular 17)

- **Telas**: `features/auth/login` (LoginComponent), `features/dashboard`
  (DashboardComponent).
- **Rotas**: `/login` (pública), `/dashboard` (protegida, dentro do layout com
  sidebar e breadcrumb); redirect de `''` para `/dashboard` (ou `/login` se
  não autenticado via guard).
- **Serviços** (`core/services`): `auth.service` (login, reset, logout,
  sinal de autenticação), `token-storage.service` (cookie),
  `loading.service` (contador de requisições).
- **Interceptors** (`core/interceptors`): `auth.interceptor` (injeta
  `Authorization: Bearer <token>`), `loading.interceptor` (ativa/desativa o
  loader).
- **Comonentes de UI** (`hared`): `loading-indicator` (overlay/spinner
  minimalista com as cores da paleta), `breadcrumb` (item atual da rota),
  `sidebar` (menu lateral com item Dashboard).
- **Login**: Reactive Forms com validadores (email, senha), mensagens de erro
  da API (401), checkbox "manter conectado" (cookie com expiração maior),
  botão "Esqueci minha senha" que abre form de reset (sempre OK).
- **Estado**: signals (`isAuthenticated`, `user`).

## Backend (Mock Server)

- **Estrutura**: `mocks/routes/` (auth.js), `mocks/collections/` (base),
  dataset/fixture de usuário (admin@mirante.com.br / 123456), `mocks.config.js`.
- **Routes**:
  - `POST /api/auth/login` — variants: `login-sucesso` (200 com
    `{ token, user }`), `email-nao-encontrado` (401 com mensagem),
    `senha-incorreta` (401 com mensagem), `erro-servidor` (500).
  - `POST /api/auth/reset-password` — variant `sucesso` (sempre 200/OK).
  - `GET /api/auth/me` — variant `sucesso` (valida token e retorna
    usuário) para uso futuro/validação.
- **Token fake**: string no formato `base64url(header).base64url(payload).
  base64url(signature)`, com payload `{ sub, name, email, exp }` — o mock
  "encriptografa" (codifica) o payload.
- **Delay**: `mocks.config.js` lê `MOCK_DELAY_SECONDS` do env (default 0) e
  aplica delay em milissegundos a todas as rotas.
- **Porta**: 3100 (padrão).

## Sequenciamento

1. Backend: instalar `@mocks-server/main` e validar que sobe.
2. Backend: criar entidade/fixture de usuário e routes/variants de auth.
3. Backend: delay via env + validação com curl (sucesso/erro/reset).
4. Frontend: AuthService + token (cookie) + AuthGuard + rotas.
5. Frontend: tela de login (form, erros, manter conectado, reset).
6. Frontend: interceptors (auth + loading) e loading indicator.
7. Frontend: layout com sidebar + breadcrumbs + Dashboard.
8. Validações: ng build, ng test, ng serve + fluxo login via proxy.

## Decisões de arquitetura

- Token fake no formato JWT-like (base64url) — suficiente para o MVP; o
  frontend apenas guarda e reenvia.
- Cookie gerenciado por serviço próprio (sem lib externa) para manter a base
  leve; expiração distinta para "manter conectado".
- Loading via interceptor com contador (evita flash quando há requisições
  concorrentes).
- Layout (sidebar + breadcrumb) apenas na área autenticada (`/dashboard`),
  enquanto `/login` é tela cheia sem menu.
- Mensagens de erro de `/api/auth/login` vêm do mock no `body.message`.
