# Spec 0003 — Instalação Mock Server - Login

- Status: draft
- Repos envolvidos: backend | frontend
- Criado em: 2026-09-13
- Última atualização: 2026-09-13

## Contexto

O repositório `backend` (Mirant-mockServer) está vazio — o projeto ainda não
tem uma API mockada. O frontend já está instalado (spec 0002) com proxy
`/api` → `localhost:3100`. Para abrir a primeira tela (dashboard) com
autenticação, precisamos do mock de autenticação (login/reset de senha) e da
tela de login no frontend.

## Objetivo

Instalar e configurar o `@mocks-server/main` no repo backend com a entidade
**Usuário** e o serviço de **login** (token fake "encriptografado" + reset de
senha fake), e no frontend criar a **tela de login** (guarda token em cookie,
interceptor com token, loading indicator, menu lateral e breadcrumbs) com
redirecionamento para o dashboard.

## Escopo

### Dentro do escopo
- **Backend**: instalar `@mocks-server/main` (v3, porta 3100) no repo backend.
- **Backend**: entidade Usuário como fixture (nome, email, senha) — usuário
  `admin@mirante.com.br` / `123456`.
- **Backend**: rota `POST /api/auth/login` — 200 com token fake (payload
  codificado/"encriptografado") + dados do usuário; 401 com mensagens de erro
  para email não cadastrado e senha incorreta.
- **Backend**: rota `POST /api/auth/reset-password` — sempre 200 OK (fake).
- **Backend**: delay de resposta configurável em segundos no env do backend
  (aplica-se a toda requisição).
- **Frontend**: tela de login (email/senha, manter conectado, mensagens de erro).
- **Frontend**: form de reset de senha (sempre OK com feedback).
- **Frontend**: após login redirecionar para `/dashboard`; token guardado em
  **cookie** (mantendo o usuário conectado quando marcado).
- **Frontend**: interceptor que sempre envia o token (Authorization).
- **Frontend**: indicador de carregamento (loader minimalista na paleta).
- **Frontend**: menu lateral à esquerda com item Dashboard + breadcrumbs.

### Fora do escopo
- Cadastro de novos usuários (só login).
- Redefinição real de senha (envio de email etc.) — sempre fake/OK.
- Refresh token real, autorização por perfil (admin/comum).
- Persistência real de usuários (o mock é em memória/fixture).
- Outras telas além do dashboard (ex.: Lote/Lançamento) — specs futuras.

## Requisitos funcionais
1. O backend mock sobe com `@mocks-server/main` na porta 3100.
2. Existe usuário fixture: nome, email, senha — admin@mirante.com.br / 123456.
3. `POST /api/auth/login` com credenciais válidas retorna 200 com `token` e
   `user` (nome, email); credenciais inválidas retornam 401 com mensagem de
   erro (ex.: "Email não encontrado" / "Senha incorreta").
4. `POST /api/auth/reset-password` retorna sempre 200/OK (fake).
5. Token é uma string "encriptografada" gerada pelo mock (payload + assinatura
   fake) e o frontend o guarda em cookie.
6. O tempo de resposta de toda requisição é um valor pré-setado (em segundos)
   configurado no env do backend.
7. Tela de login com Reactive Forms (email, senha, manter conectado),
   validação e mensagens de erro da API.
8. Login com sucesso redireciona para `/dashboard`; sem autenticação, o usuário
   é redirecionado para `/login`.
9. Formulário de reset de senha exibe feedback "OK" (fake).
10. Interceptor envia o token no header `Authorization` em toda requisição.
11. Indicador de carregamento minimalista aparece durante as requisições
    (adaptado à paleta verde-petróleo).
12. Menu lateral (esquerda) com item "Dashboard" e breadcrumbs.

## Requisitos não-funcionais
- Backend: `@mocks-server/main` versão 3 (última estável; projeto
  descontinuado, sem novas releases).
- Frontend: Angular 17 standalone, Reactive Forms, Signals e/ou control flow
  (`@if`/`@for`), design tokens da paleta.
- Caminhos HTTP: `/api/**` via proxy já configurado (spec 0002).
- Loading visual leve (sem lib externa; estilo próprio com as cores).
- Testes: Jasmine/Karma no frontend; validação de rotas via curl no backend.

## Critérios de aceite
- [ ] Backend instalado e subido na porta 3100 com `@mocks-server/main`.
- [ ] `POST /api/auth/login` responde e os cenários (sucesso/erro) funcionam.
- [ ] `POST /api/auth/reset-password` sempre OK.
- [ ] Delay de resposta configurado via env (segundos) e aplicado.
- [ ] Frontend: tela /login com form, erro, manter conectado e reset.
- [ ] Login redireciona para /dashboard; logout/redirecionamento por guard ok.
- [ ] Token em cookie + interceptor Authorization funcionais.
- [ ] Loader minimalista visível nas requisições (paleta).
- [ ] Menu lateral com Dashboard e breadcrumbs visíveis.
- [ ] `ng build` e `ng test` passando.

## Dependências
- Spec 0002 (base frontend com proxy `/api` → 3100) — concluída.
- `@mocks-server/main` v3 (npm).
- Node.js no ambiente.

## Riscos
- Mocks Server descontinuado — fixar versão 3.12.x e documentar.
- Token "encriptografado" é fake (codificação base64 do payload), não é
  segurança real.
- Delay global pode tornar a suíte de testes lenta — separar env de teste.
- Mensagens/UX do login a validar com o time.
