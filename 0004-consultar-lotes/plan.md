# Plano — Spec 0004

## Abordagem técnica
Tela nova "Outros Créditos/Débitos" (rota `lotes`), composta por componentes
standalone reutilizáveis (breadcrumb, faixa De/Até, painel de filtros,
tabela com seleção, barra de ações, modal de detalhe), orquestrados por um
facade RxJS (`LoteFacade`). O backend mockado ganha modelos/seed em memória
e rotas de filtros/listagem/detalhe/ações no `@mocks-server/main`.

## Frontend (Angular 17)
- **Rota:** `lotes` (dentro do `LayoutComponent`, ao lado de `dashboard`),
  componente `LotesPageComponent`.
- **Componentes/telas afetados:**
  - `BreadcrumbComponent` (`shared/breadcrumb/`): refatorar de "derivado da
    URL" para `@Input() items: BreadcrumbItem[]`, `@Input() homeLabel`,
    `@Input() showHome`, `@Output() home`, `@Output() itemClick`. Ajustar o
    único uso atual (`LayoutComponent`) para continuar funcionando.
  - `RangeFieldComponent` (`shared/range-field/`, seletor `app-range-field`):
    `@Input() label/min/max/type/placeholderDe/placeholderAte`,
    `@Output() change` com `{ de, ate }`. Suporta `type: 'number' | 'date' |
    'currency'` (currency com máscara BRL simples via `(input)`).
  - `FilterPanelComponent` (`shared/filter-panel/`, `app-filter-panel`):
    painel recolhível (signal `expandido`), campos ligados às opções de
    filtro recebidas por `@Input() opcoes: FiltrosLoteOpcoes`, emite
    `@Output() pesquisar: FiltroLote` no clique do botão "Pesquisar".
  - `LoteTableComponent` (`shared/lote-table/`, `app-lote-table`): recebe
    `@Input() lotes/total/page/size/selectedIds`, emite
    `@Output() selectionChange/rowAction/pageChange`.
  - `LoteActionsComponent` (`features/lotes/components/`,
    `app-lote-actions`): recebe `@Input() selectionMode`, emite
    `@Output() action` com o nome da ação clicada.
  - `LoteDetailDialogComponent` (`features/lotes/components/`,
    `app-lote-detail-dialog`): `@Input() visible/loteId`,
    `@Output() closed/action`; busca o lote via facade ao abrir.
  - `LotesPageComponent` (`features/lotes/`): junta os componentes acima.
- **Modelos** (`core/models/lote.model.ts` e afins): `Lote`, `Instituicao`,
  `Usuario`, `SituacaoLote` (enum/union type), `FiltroLote`,
  `FiltrosLoteOpcoes`, `PaginatedResponse<T>`, `BreadcrumbItem`.
- **Serviços:** `LoteFacade` (`core/services/lote.facade.ts`) concentra
  chamadas HTTP + estado observável; `LotesPageComponent` e filhos só
  consomem via `async` pipe.
- **Estado (RxJS):** `BehaviorSubject` para filtro ativo, página, tamanho de
  página e seleção (`Set<number>`); `switchMap` na busca para cancelar
  requisições obsoletas; `shareReplay(1)` no resultado da página atual;
  `selectionMode$` derivado de `selectedIds$` (`none | one | many`).

## Backend (Mock Server)
- **Modelos/seed** (`src/lotes.js`, seguindo o padrão de `src/usuarios.js`):
  array em memória de lotes com situações e datas variadas, mais listas
  fixas de instituições e instituições responsáveis.
- **Routes necessárias** (`mocks/routes/lotes.js`, `type: middleware`, como
  em `mocks/routes/auth.js`):
  - `GET /api/filtros/lotes`
  - `GET /api/lotes` (query params: instituições, situação, faixas de id/
    valor/data, `page`, `size`)
  - `GET /api/lotes/:id`
  - `POST /api/lotes/:id/confirmar` | `/enviar` | `/excluir` (efeito real no
    seed)
  - `POST /api/lotes/:id/incluir` | `/alterar` | `/justificativa`
    (placeholder 200, sem alterar o seed — fora do escopo desta spec)
- **Collections a atualizar** (`mocks/collections.js`): incluir as novas
  rotas na collection `base` com as variants de sucesso.
- **Validação:** parâmetros de filtro inválidos (ex. situação fora do enum,
  paginação não numérica) → 400.

## Sequenciamento
1. Backend: modelos/seed → rotas de filtros/listagem/detalhe → rotas de
   ação → collection `base` atualizada.
2. Frontend: modelos tipados → `LoteFacade` → `BreadcrumbComponent`
   (refactor) → `RangeFieldComponent` → `FilterPanelComponent` →
   `LoteTableComponent` → `LoteActionsComponent` → `LoteDetailDialogComponent`
   → `LotesPageComponent` + rota.
3. Testes (unitários dos componentes/facade) → validação visual manual
   contra `prototipos/consulta.png` → commits/PR.

## Decisões de arquitetura
- Estado via RxJS/facade (não signals) para manter consistência com o único
  precedente de estado assíncrono do projeto (`AuthService`).
- Seleção de linhas não persiste entre páginas nesta versão (ver Riscos em
  `spec.md`).
- Ações de Incluir/Alterar/Justificativa ficam com endpoint placeholder
  (200) propositalmente — regra de negócio real é spec futura.
