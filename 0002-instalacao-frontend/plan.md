# Plano — Spec 0002

## Abordagem técnica

Bootstrap do projeto Angular 17 no repo `frontend` usando o Angular CLI
(`ng new`), seguido da configuração de PrimeNG 17 com tema customizado
(design tokens da paleta verde-petróleo), estruturação por camadas
(core/shared/features), criação de pipes pt-BR e do proxy de desenvolvimento
para o backend mock. Nenhuma mudança no repo `backend` nesta spec.

## Frontend (Angular 17)

- **Criação do projeto**: `npx @angular/cli@17 new mirante --standalone
  --routing --style=scss --skip-git --skip-tests=false` (o repo git já
  existe — por isso `--skip-git`), sem SSR.
- **PrimeNG 17**: `npm i primeng@17` + import dos estilos do tema e do
  PrimeNG no `angular.json` (styles); usar os componentes standalone
  conforme necessário na base (Button, InputText, Card, Table, Dialog etc.).
- **Design tokens**: arquivo `src/styles/tokens.scss` com as variáveis da
  paleta (--petroleo-*, --agua-*, --surface-*, --texto-*); sobrescrever as
  variáveis do tema PrimeNG (ex.: `--primary-color`) com a paleta do projeto.
- **Estrutura de pastas** em `src/app/`:
  - `core/` — singletons (services singleton, interceptor HTTP, guards);
    começa com o necessário mínimo (ex.: `services/`, `interceptors/`).
  - `shared/` — componentes reutilizáveis e `pipes/` (pt-BR).
  - `features/` — vazia por enquanto (telas futuras de Lote/Lançamento).
- **Pipes pt-BR** (`shared/pipes/`): pipe `currency` (BRL, pt-BR) e pipe
  `date` (pt-BR), com `LOCALE_ID` registrado (locale pt-BR no
  `main.ts`/providers) e testes unitários.
- **Proxy dev**: `proxy.conf.json` mapeando `/api` →
  `http://localhost:3100`; registrar em `angular.json`
  (serve → options → proxyConfig).
- **Tela inicial**: estilizar o AppComponent com os design tokens como
  exemplo de uso (sem tela demo dedicada).
- **README**: instruções `npm install` / `ng serve`, versão do Angular e do
  PrimeNG, decisões técnicas e exemplos de uso dos design tokens.
- **Testes**: Jasmine/Karma (padrão do CLI); testes do AppComponent e dos
  pipes.

## Backend (Mock Server)

- Nenhuma mudança nesta spec — o repo `backend` permanece vazio.
- Observação: o proxy do frontend já aponta para `http://localhost:3100`
  (porta padrão do `@mocks-server/main`); quando o backend for configurado
  (spec futura), a integração já estará encaminhada.

## Sequenciamento

1. Criar projeto Angular 17 (ng new) no repo `frontend`.
2. Configurar SCSS global e os design tokens da paleta verde-petróleo.
3. Instalar/configurar PrimeNG 17 e aplicar o tema com a paleta.
4. Criar a estrutura core/shared/features.
5. Criar os pipes pt-BR (currency/date) com testes.
6. Configurar o proxy (`/api` → 3100) e o `angular.json`.
7. Estilizar a tela inicial com os tokens (exemplo de uso).
8. Escrever o README (instalação, execução, versões, tokens).
9. Validar: `ng test`, `ng build`, `ng serve`.

## Decisões de arquitetura

- **Angular 17 fixo** (última 17.x): stack oficial registrado em
  `config/project.yaml`.
- **Standalone components**: padrão do Angular 17; evita NgModules.
- **PrimeNG 17** como biblioteca de UI (escolha do time; atende os
  componentes de tabela/filtros/modal das próximas features).
- **Jasmine/Karma**: padrão do CLI, zero configuração extra.
- **npm**: gerenciador de pacotes padrão.
- **Design tokens via variáveis CSS/SCSS**: tema centralizado e sobrescrita
  do PrimeNG com a paleta do projeto.
- **Sem SSR/Docker/CI nesta spec**: escopo mínimo para uma base pronta.
