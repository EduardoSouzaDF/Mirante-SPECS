# Tarefas — Spec 0002

## Frontend (Angular 17)
- [ ] Criar projeto Angular 17 (última 17.x) com `ng new` (standalone,
      routing, SCSS, sem SSR) no repo `frontend` (branch
      `feature/0002-instalacao-frontend`).
- [ ] Configurar os design tokens da paleta verde-petróleo
      (`src/styles/tokens.scss` com --petroleo-*, --agua-*, --surface-*,
      --texto-*).
- [ ] Instalar PrimeNG 17 (`npm i primeng@17`) e registrar os estilos/tema no
      `angular.json`.
- [ ] Sobrescrever as variáveis do tema PrimeNG com a paleta verde-petróleo.
- [ ] Criar a estrutura de pastas `core/`, `shared/` e `features/` em `app/`.
- [ ] Criar os pipes pt-BR `currency` e `date` em `shared/pipes/` (com
      `LOCALE_ID` pt-BR) e exportar para uso nos componentes.
- [ ] Escrever testes unitários (Jasmine/Karma) para os pipes pt-BR e para o
      AppComponent (renderiza com o tema).
- [ ] Criar `proxy.conf.json` (`/api` → `http://localhost:3100`) e registrar
      no `angular.json` (serve → proxyConfig).
- [ ] Estilizar a tela inicial (AppComponent) usando os design tokens como
      exemplo de uso.
- [ ] Escrever o README do frontend (instalação/execução, versões, decisões
      técnicas, exemplos de uso dos tokens).
- [ ] Validar: `npm install`, `ng test`, `ng build`, `ng serve` (tela com o
      tema + proxy apontando para o mock server).

## Backend (Mock Server)
- Nenhuma tarefa nesta spec (repo `backend` permanece vazio). O proxy do
  frontend já aponta para `http://localhost:3100`.

## Transversais
- [x] Criar branch `feature/0002-instalacao-frontend` nos repos frontend,
      backend e specs (feito).
- [x] Corrigir caminhos em `config/repos.env` (`./frontend`, `./backend`).
- [ ] Instalar as skills do Angular no ambiente do agente:
      `npx skills add https://github.com/angular/skills`.
- [ ] Atualizar `specs/INDEX.md` e `specs/STATE.md` conforme o avanço da spec.
