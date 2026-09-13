# Plano — Spec 0004

## Abordagem técnica
Tela nova "Outros Créditos/Débitos" (rota `lotes`), composta por componentes
standalone reutilizáveis (breadcrumb, faixa De/Até, painel de filtros
genérico, tabela genérica, barra de ações, modal de detalhe, modal de
escopo), orquestrados por um facade RxJS (`LoteFacade`). O backend mockado
ganha modelos/seed em memória e rotas de filtros/listagem/detalhe/ações
(inclusive em massa) no `@mocks-server/main`.

## Frontend (Angular 17)
- **Rota:** `lotes` (dentro do `LayoutComponent`, ao lado de `dashboard`),
  componente `LotesPageComponent`; item novo no menu lateral
  (`SidebarComponent`) apontando para `/lotes`.
- **Componentes/telas afetados:**
  - `BreadcrumbComponent` (`shared/breadcrumb/`): `@Input() items:
    BreadcrumbItem[]`, `@Input() homeLabel`, `@Input() showHome`,
    `@Output() home`, `@Output() itemClick`. `LayoutComponent` monta os
    itens a partir de `data.breadcrumb` da rota ativa (genérico, serve
    para qualquer rota nova).
  - `RangeFieldComponent` (`shared/range-field/`, `app-range-field`):
    `@Input() label/placeholderDe/placeholderAte`, `@Input() type: 'number'
    | 'date' | 'currency'`, `@Output() rangeChange` com `{ de, ate }` (nome
    diferente de "change" de propósito — evita colisão com o evento nativo
    do DOM que borbulha dos `<input>` internos). `type="number"` usa input
    nativo `type="number"` (min 0); `type="currency"` mascara BRL em tempo
    real, escrevendo o valor sanitizado direto no elemento (não só via
    binding) para não deixar caractere inválido "colado"; `type="date"`
    abre o datepicker ao clicar (`showPicker()`) e trava o mínimo do "Até"
    no valor do "De".
  - `FilterPanelComponent` (`shared/filter-panel/`, `app-filter-panel`):
    **casca genérica** — `@Input() titulo`, `@Input() subtitulo`, signal
    `expandido`, corpo do painel inteiramente projetado via `<ng-content>`
    por quem usa. Não sabe mais nada sobre lotes/filtros específicos.
  - `GenericTableComponent` (`shared/generic-table/`, `app-generic-table`):
    tabela genérica reutilizável — `@Input() items/total/page/size/
    selectedIds/trackBy/emptyMessage`, `@Output() selectionChange/
    pageChange`. Cabeçalho e linhas fornecidos por quem usa via
    `<ng-template #header>`/`<ng-template #row let-item>` e renderizados
    com `NgTemplateOutlet`; seleção (com "selecionar todos"/indeterminate)
    e paginação continuam genéricas dentro do componente. CSS das células
    de header/linha usa `:host ::ng-deep`, porque `ngTemplateOutlet`
    projeta conteúdo do template de quem usa (atributo de encapsulamento
    diferente do host).
  - `LoteActionsComponent` (`features/lotes/components/`,
    `app-lote-actions`): recebe `@Input() selectionMode`, emite
    `@Output() action`. Confirmar/Enviar/Justificativa sempre habilitados;
    Alterar/Visualizar só com exatamente 1 selecionado; Excluir com 1+.
  - `LoteDetailDialogComponent` (`features/lotes/components/`,
    `app-lote-detail-dialog`): `@Input() visible/loteId`,
    `@Output() closed/action`; busca o lote via facade ao abrir.
  - `ScopeConfirmDialogComponent` (`features/lotes/components/`,
    `app-scope-confirm-dialog`): `@Input() visible/acaoLabel/mostrarTodos`,
    `@Output() escolha ('pagina' | 'todos') / cancelado`. Só aparece
    quando Confirmar/Enviar/Justificativa são clicados sem seleção.
  - `LotesPageComponent` (`features/lotes/`): junta os componentes acima;
    dono do estado dos campos de filtro (antes vivia no
    `FilterPanelComponent`) e da lógica de escopo/filtro por situação das
    ações em massa.
- **Modelos** (`core/models/lote.model.ts` e afins): `Lote`, `Instituicao`,
  `Usuario`, `SituacaoLote` (union type), `FiltroLote`,
  `FiltrosLoteOpcoes`, `PaginatedResponse<T>`, `BreadcrumbItem`.
- **Pipes novos:** `DateHoraBrPipe` (`shared/pipes/datetime.pipe.ts`,
  `dataHoraBr`) — formata data+hora `dd/mm/aaaa HH:MM`, usado na coluna
  "Data/Hora Situação Lote".
- **Serviços:** `LoteFacade` (`core/services/lote.facade.ts`) concentra
  chamadas HTTP + estado observável; `LotesPageComponent` e filhos só
  consomem via `toSignal`/`async`. Estado de consulta (filtro+página+
  tamanho) vive num único `BehaviorSubject` (`ConsultaState`), trocado
  atomicamente por `pesquisar()`/`irParaPagina()` — evita uma busca
  intermediária com filtro novo e página antiga. A busca inicial da tela
  dispara sozinha ao assinar `lotes$`/`total$` (o `BehaviorSubject` reemite
  o valor padrão); `LotesPageComponent` não precisa (e não deve) chamar
  `pesquisar({})` de novo no construtor.
- **Ações em massa:** `confirmarEmMassa(ids)`/`enviarEmMassa(ids)`/
  `justificativaEmMassa(ids)` fazem uma única chamada `POST` com
  `{ ids: number[] }`, em vez de uma chamada por id. `LotesPageComponent`
  filtra o conjunto de ids por situação exigida (`Aberto` para confirmar,
  `Confirmado` para enviar) antes de chamar o facade. `listarTodosLotesFiltroAtual()`
  busca todos os lotes do filtro atual (ignorando paginação) para o
  escopo "todos" do modal.

## Backend (Mock Server)
- **Modelos/seed** (`src/lotes.js`): 24 lotes (4 originais + 20 gerados
  programaticamente com instituição/situação/valor/data variados —
  situações mescladas manualmente, não um ciclo simples), 7 instituições,
  3 instituições responsáveis, 4 usuários.
- **Routes** (`mocks/routes/lotes.js`, `type: middleware`):
  - `GET /api/filtros/lotes`
  - `GET /api/lotes` (query params: instituições, situação, faixas de id/
    valor/data, `page`, `size`)
  - `GET /api/lotes/:id`
  - `POST /api/lotes/confirmar` | `/enviar` — corpo `{ ids: number[] }`,
    aplica a mesma situação a todos os ids numa única chamada
    (`atualizarSituacaoEmMassa`); `POST /api/lotes/:id/excluir` continua
    por id.
  - `POST /api/lotes/incluir` | `/api/lotes/:id/alterar` |
    `/api/lotes/justificativa` (placeholder 200, `justificativa` também
    aceita `{ ids }` em massa, sem alterar o seed — fora do escopo desta
    spec)
- **Validação:** parâmetros de filtro inválidos (situação fora do enum,
  paginação não numérica) → 400; `ids` ausente/vazio/não-array nas ações
  em massa → 400.

## Sequenciamento
1. Backend: modelos/seed → rotas de filtros/listagem/detalhe → rotas de
   ação (unitárias, depois migradas para em massa) → collection `base`.
2. Frontend: modelos tipados → `LoteFacade` → `BreadcrumbComponent`
   (refactor) → `RangeFieldComponent` → `FilterPanelComponent` →
   `GenericTableComponent` (generalizado a partir de um `LoteTableComponent`
   inicial) → `LoteActionsComponent` → `LoteDetailDialogComponent` →
   `ScopeConfirmDialogComponent` → `LotesPageComponent` + rota + menu.
3. Testes (unitários dos componentes/facade/página) → validação visual
   interativa (Playwright headed, janela aberta) com o usuário passando
   ajustes ao vivo → correções → commits/PR.

## Decisões de arquitetura
- Estado via RxJS/facade (não signals) para manter consistência com o único
  precedente de estado assíncrono do projeto (`AuthService`); os signals do
  componente (`toSignal`) só espelham os observables do facade.
- Componentes de UI genéricos (`FilterPanelComponent`, `GenericTableComponent`)
  não conhecem `Lote` — a tela de lotes é quem projeta conteúdo/templates
  específicos. Facilita reuso em telas futuras.
- Seleção de linhas não persiste entre páginas nesta versão (ver Riscos em
  `spec.md`).
- Ações de Incluir/Alterar/Justificativa ficam com endpoint placeholder
  (200) propositalmente — regra de negócio real é spec futura.
- Confirmar/Enviar filtram por situação (Aberto/Confirmado) dentro do
  conjunto escolhido pelo usuário, em vez de rejeitar a ação inteira ou
  aplicar indiscriminadamente — decisão tomada junto com o usuário durante
  a validação.
