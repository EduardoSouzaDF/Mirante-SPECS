# Tarefas — Spec 0002

## Frontend (Angular 17)
- [x] Criar projeto Angular 17 (última 17.x = 17.3.0) com `ng new` (standalone,
      routing, SCSS, sem SSR) no repo `frontend` (branch
      `feature/0002-instalacao-frontend`).
- [x] Configurar os design tokens da paleta verde-petróleo
      (`src/styles/tokens.scss` com --petroleo-*, --agua-*, --surface-*,
      --texto-*).
- [x] Instalar PrimeNG 17 (17.18.15) e primeicons; registrar os estilos/tema no
      `angular.json`.
- [x] Sobrescrever as variáveis do tema PrimeNG com a paleta verde-petróleo
      (`src/styles.scss`).
- [x] Criar a estrutura de pastas `core/`, `shared/` e `features/` em `app/`
      (core/services, core/interceptors, shared/pipes, features).
- [x] Criar os pipes pt-BR `currency` (brl) e `date` (dataBr) em
      `shared/pipes/` (com `LOCALE_ID` pt-BR) e exportar para uso nos
      componentes.
- [x] Escrever testes unitários (Jasmine/Karma) para os pipes pt-BR e para o
      AppComponent — `ng test`: 9 testes passando.
- [x] Criar `proxy.conf.json` (`/api` → `http://localhost:3100`) e registrar
      no `angular.json` (serve → proxyConfig) + `karma.conf.js` (ChromeHeadless).
- [x] Estilizar a tela inicial (AppComponent) usando os design tokens como
      exemplo de uso.
- [x] Escrever o README do frontend (instalação/execução, versões, decisões
      técnicas, exemplos de uso dos tokens).
- [x] Validar: `npm install` ✔, `ng test` ✔ (9/9), `ng build` ✔, `ng serve` ✔
      (HTTP 200 em http://localhost:4200). Proxy validado apenas na
      configuração — chamada real depende de rota no mock server (spec futura).

## Backend (Mock Server)
- Nenhuma tarefa nesta spec (repo `backend` permanece vazio). O proxy do
  frontend já aponta para `http://localhost:3100`.

## Transversais
- [x] Criar branch `feature/0002-instalacao-frontend` nos repos frontend,
      backend e specs (feito).
- [x] Corrigir caminhos em `config/repos.env` (`./frontend`, `./backend`).
- [x] Instalar as skills do Angular no ambiente do agente:
      `npx skills add https://github.com/angular/skills`.
- [x] Atualizar `specs/INDEX.md` e `specs/STATE.md` conforme o avanço da spec.
