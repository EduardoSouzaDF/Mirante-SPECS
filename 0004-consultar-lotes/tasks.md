# Tarefas — Spec 0004

## Backend (Mock Server)
- [x] Criar `src/lotes.js`: seed em memória (instituições, instituições
      responsáveis, situações, lotes com situações/valores/datas variados)
      + helpers de busca/filtro/paginação.
- [x] Criar `mocks/routes/lotes.js` com `GET /api/filtros/lotes`.
- [x] Adicionar `GET /api/lotes` (filtros + paginação) em `lotes.js`.
- [x] Adicionar `GET /api/lotes/:id` em `lotes.js`.
- [x] Adicionar `POST /api/lotes/:id/excluir` (efeito real no seed) em
      `lotes.js`.
- [x] Adicionar `POST /api/lotes/incluir`, `/api/lotes/:id/alterar`,
      `/api/lotes/justificativa` (placeholder 200) em `lotes.js`.
- [x] Atualizar `mocks/collections.js` (collection `base`) com as novas
      rotas.
- [x] Validar parâmetros inválidos → 400 (situação fora do enum, paginação
      não numérica).
- [x] Migrar `POST /api/lotes/:id/confirmar` e `/enviar` para
      `POST /api/lotes/confirmar` e `/api/lotes/enviar` recebendo
      `{ ids: number[] }` — uma única chamada para todos os lotes
      selecionados (`atualizarSituacaoEmMassa`); 400 se `ids` ausente/
      vazio/não-array.
- [x] `POST /api/lotes/justificativa` também passa a aceitar `{ ids }` em
      massa (placeholder, sem alterar o seed).
- [x] Gerar mais dados de seed: 20 lotes adicionais (total 24, ids 5-24) e
      5 instituições adicionais (total 7) + 2 usuários adicionais, com
      situações mescladas manualmente (não um ciclo Aberto/Confirmado/
      Enviado repetindo em ordem).

## Frontend (Angular 17)
- [x] Criar modelos tipados em `core/models/` (`Lote`, `Instituicao`,
      `Usuario`, `SituacaoLote`, `FiltroLote`, `FiltrosLoteOpcoes`,
      `PaginatedResponse<T>`, `BreadcrumbItem`).
- [x] Refatorar `BreadcrumbComponent` para `@Input() items/homeLabel/
      showHome` + `@Output() home/itemClick`, e ajustar o uso em
      `LayoutComponent` (breadcrumb passa a vir de `data.breadcrumb` da
      rota ativa).
- [x] Criar `RangeFieldComponent` (`shared/range-field/`); ID Lote com
      `type="number"` nativo (min 0); Valor Lote com máscara BRL sem
      caractere solto; Data Entrada abre datepicker ao clicar e trava o
      mínimo do "Até" pelo "De".
- [x] Criar `FilterPanelComponent` (`shared/filter-panel/`) como casca
      genérica: `titulo`/`subtitulo` por `@Input`, corpo via `ng-content`
      (conteúdo específico de lotes migrado para `LotesPageComponent`).
- [x] Criar `GenericTableComponent` (`shared/generic-table/`, generalizado
      a partir de um `LoteTableComponent` inicial) com seleção genérica
      (checkbox linha + "selecionar todos" com indeterminate) e cabeçalho/
      linhas via `<ng-template>`; CSS das células com `:host ::ng-deep`.
- [x] Criar `LoteActionsComponent` (`features/lotes/components/`):
      Confirmar/Enviar/Justificativa sempre habilitados; Alterar/
      Visualizar com exatamente 1 selecionado; Excluir com 1+.
- [x] Criar `LoteDetailDialogComponent` (`features/lotes/components/`).
- [x] Criar `ScopeConfirmDialogComponent` (`features/lotes/components/`):
      modal de escopo (página/todos) para ações em massa sem seleção.
- [x] Criar `LoteFacade` (`core/services/lote.facade.ts`): filtros, busca
      paginada (estado atômico filtro+página), detalhe por id, seleção,
      `selectionMode$`, `confirmarEmMassa`/`enviarEmMassa`/
      `justificativaEmMassa` (uma chamada por ação), `listarTodosLotesFiltroAtual`.
- [x] Criar `LotesPageComponent` (`features/lotes/`) juntando os
      componentes acima; registrar rota `lotes` em `app.routes.ts`; item
      novo no menu lateral (`SidebarComponent`).
- [x] Filtro por Enter: campos dentro de `<form (ngSubmit)>`, botão
      `type="submit"`.
- [x] Filtrar por situação as ações em massa: Confirmar só Abertos, Enviar
      só Confirmados, dentro do conjunto selecionado/escopo.
- [x] Nova coluna "Data/Hora Situação Lote" (`DateHoraBrPipe`,
      `shared/pipes/datetime.pipe.ts`).
- [x] Responsividade: grid de filtros com `minmax(0, 1fr)` (não estoura
      mais no mobile); sidebar com `height:100%` (acompanha a altura da
      página, não corta mais quando a listagem cresce).
- [x] Corrigir bug real: `LotesPageComponent` chamava `pesquisar({})` no
      construtor além da busca automática ao assinar `lotes$`/`total$` —
      causava 2 requisições no carregamento; removida a chamada redundante.
- [x] Testes unitários: `LoteFacade` (inclui ações em massa), `RangeFieldComponent`,
      `FilterPanelComponent` (genérico, projeção de conteúdo),
      `GenericTableComponent` (genérico, seleção/indeterminate),
      `LoteActionsComponent` (regras de habilitação), `BreadcrumbComponent`,
      `LotesPageComponent` (filtro por situação nas ações em massa, filtro
      de faixa, requisição única no carregamento).
- [x] Testes e2e Playwright (`e2e/lotes.spec.ts`): listagem, seleção e
      habilitação da barra de ações.
- [x] Validação visual interativa (Playwright headed, janela aberta) com o
      usuário — layout, colunas, badges, paginação, filtros, ações em
      massa e responsividade mobile, todos ajustados ao vivo.

## Transversais
- [x] Branch `feature/0004-consultar-lotes` criada nos 3 repos.
- [x] Atualizar `state.md` da spec a cada mudança de fase.
- [x] Commits + push + PR (frontend, backend, specs) após validação do
      usuário.
