# Tarefas — Spec 0004

## Backend (Mock Server)
- [x] Criar `src/lotes.js`: seed em memória (instituições, instituições
      responsáveis, situações, lotes com situações/valores/datas variados)
      + helpers de busca/filtro/paginação.
- [x] Criar `mocks/routes/lotes.js` com `GET /api/filtros/lotes`.
- [x] Adicionar `GET /api/lotes` (filtros + paginação) em `lotes.js`.
- [x] Adicionar `GET /api/lotes/:id` em `lotes.js`.
- [x] Adicionar `POST /api/lotes/:id/confirmar`, `/enviar`, `/excluir`
      (efeito real no seed) em `lotes.js`.
- [x] Adicionar `POST /api/lotes/:id/incluir`, `/alterar`, `/justificativa`
      (placeholder 200) em `lotes.js`.
- [x] Atualizar `mocks/collections.js` (collection `base`) com as novas
      rotas.
- [x] Validar parâmetros inválidos → 400 (situação fora do enum, paginação
      não numérica).

## Frontend (Angular 17)
- [x] Criar modelos tipados em `core/models/` (`Lote`, `Instituicao`,
      `Usuario`, `SituacaoLote`, `FiltroLote`, `FiltrosLoteOpcoes`,
      `PaginatedResponse<T>`, `BreadcrumbItem`).
- [x] Refatorar `BreadcrumbComponent` para `@Input() items/homeLabel/
      showHome` + `@Output() home/itemClick`, e ajustar o uso em
      `LayoutComponent` (breadcrumb passa a vir de `data.breadcrumb` da
      rota ativa).
- [x] Criar `RangeFieldComponent` (`shared/range-field/`).
- [x] Criar `FilterPanelComponent` (`shared/filter-panel/`).
- [x] Criar `LoteTableComponent` (`shared/lote-table/`) com seleção
      (checkbox linha + "selecionar todos" com indeterminate).
- [x] Criar `LoteActionsComponent` (`features/lotes/components/`) com as
      regras de habilitação por `selectionMode`.
- [x] Criar `LoteDetailDialogComponent` (`features/lotes/components/`).
- [x] Criar `LoteFacade` (`core/services/lote.facade.ts`): filtros, busca
      paginada, detalhe por id, seleção, `selectionMode$`.
- [x] Criar `LotesPageComponent` (`features/lotes/`) juntando os
      componentes acima; registrar rota `lotes` em `app.routes.ts`.
- [x] Testes unitários: `LoteFacade`, `RangeFieldComponent`,
      `FilterPanelComponent`, `LoteTableComponent` (seleção/indeterminate),
      `LoteActionsComponent` (regras de habilitação), `BreadcrumbComponent`.
- [x] Testes e2e Playwright (`e2e/lotes.spec.ts`): listagem, seleção e
      habilitação da barra de ações.
- [x] Validação visual manual contra `../prototipos/consulta.png` (via
      screenshot Playwright) — layout, colunas, badges e paginação batem
      com o protótipo.

## Transversais
- [x] Branch `feature/0004-consultar-lotes` criada nos 3 repos.
- [x] Atualizar `state.md` da spec a cada mudança de fase.
- [ ] Commits + push + PR (frontend, backend, specs) só após validação do
      usuário.
