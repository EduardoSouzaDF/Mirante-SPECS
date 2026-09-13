# Spec 0002 — Instalação FrontEnd

- Status: draft
- Repos envolvidos: frontend (+ transversal)
- Criado em: 2026-09-13
- Última atualização: 2026-09-13

## Contexto

O repositório `frontend` (Mirante-Front) está praticamente vazio — contém
apenas o README inicial e o commit "initial". O projeto MVP Mirante ainda não
tem uma aplicação Angular para servir a interface do usuário. Para as próximas
features (Lote, Lançamento, filtros de pesquisa), é preciso uma base sólida com
Angular 17, biblioteca de componentes de UI, tema definido e estrutura de
pastas organizada.

## Objetivo

Criar do zero o projeto Angular 17 (standalone components) no repositório
`frontend`, com a base de bibliotecas (PrimeNG), tema verde-petróleo via design
tokens, estrutura de pastas (core/shared/features), pipes pt-BR e proxy de
desenvolvimento para o backend mock — pronta para as próximas features.

## Escopo

### Dentro do escopo
- Criar projeto Angular 17 (última 17.x) com o Angular CLI: standalone
  components, routing, SCSS e sem SSR.
- Instalar e configurar PrimeNG 17 como biblioteca de componentes de UI.
- Aplicar tema verde-petróleo sobrescrevendo o tema do PrimeNG via design
  tokens (variáveis CSS) — paleta derivada das imagens em `docs/`.
- Estruturar pastas por camadas: `core/` (singletons, guards, interceptor),
  `shared/` (componentes reutilizáveis, pipes) e `features/` (telas — vazia).
- Criar pipes de formatação pt-BR (moeda e data) em `shared/pipes`.
- Configurar proxy de desenvolvimento (`/api` → mock server, porta 3100).
- Testes unitários com Jasmine + Karma (padrão do CLI).
- README com instruções de instalação/execução, versões e decisões técnicas.
- Exemplos de uso dos design tokens documentados no README (sem tela demo).

### Fora do escopo
- Features de domínio (Lote, Lançamento, filtros de pesquisa, tabela, modal)
  — serão specs futuras.
- Configuração do backend mock (rotas/behaviors/collections) — o repo
  `backend` continua vazio nesta spec; o proxy apenas aponta para ele.
- Autenticação/login, SSR, Docker, CI/CD.

## Requisitos funcionais
1. O comando `ng serve` deve subir a aplicação e exibir a tela inicial com o
   tema verde-petróleo aplicado (tela inicial estilizada com os tokens).
2. O tema do PrimeNG deve refletir a paleta definida nos design tokens
   (botões, inputs, tabelas usam as cores do projeto).
3. Os pipes `currency` e `date` devem formatar valores no padrão pt-BR
   (ex.: `R$ 1.234,56` e `13/09/2026`).
4. O proxy de desenvolvimento deve redirecionar chamadas a `/api/**` para o
   mock server (`http://localhost:3100`).
5. A estrutura `core/shared/features` deve existir e estar documentada.

## Requisitos não-funcionais
- Angular 17 (última 17.x), TypeScript com tipagem explícita, standalone
  components.
- Uso de Signals e da nova sintaxe de control flow (`@if`/`@for`) quando
  cabível na base.
- Estilos organizados com design tokens/variáveis CSS; base para
  responsividade.
- Testes unitários executando com `ng test` (Jasmine/Karma).
- Build de produção concluindo com `ng build`.
- Acessibilidade básica nas customizações (contraste da paleta).

## Critérios de aceite
- [ ] `npm install` conclui sem erros.
- [ ] `ng serve` sobe a aplicação e a tela inicial exibe o tema verde-petróleo.
- [ ] `ng test` executa e todos os testes passam.
- [ ] `ng build` (produção) conclui sem erros.
- [ ] Proxy `/api` configurado e apontando para o mock server (porta 3100).
- [ ] Pipes pt-BR (currency/date) presentes em `shared/pipes` com testes.
- [ ] Estrutura `core/`, `shared/`, `features/` criada e documentada.
- [ ] Design tokens da paleta criados e exemplos de uso no README.
- [ ] README com instruções de instalação/execução e decisões técnicas.
- [ ] Skills do Angular instaladas no ambiente (`npx skills add ...`).

## Dependências
- Node.js 18.13+ ou 20.x (requisito do Angular 17).
- Angular CLI (via `npx @angular/cli@17` caso não haja instalado globalmente).
- Backend mock (`@mocks-server/main`) na porta 3100 para validar o proxy
  (configuração do backend fica em spec futura).
- Skills do Angular: `npx skills add https://github.com/angular/skills`
  (instalação no `.cline/skills/` do ambiente do agente).

## Riscos
- Compatibilidade de versões PrimeNG 17 ↔ Angular 17 (usar versões oficiais
  compatíveis).
- Paleta verde-petróleo precisa ser validada visualmente por uma pessoa
  (derivada de análise de pixels das imagens de referência em `docs/`).
- `ng` não instalado globalmente no ambiente (usar `npx @angular/cli@17`).
- Proxy depende do mock server estar rodando; documentar o passo no README.
