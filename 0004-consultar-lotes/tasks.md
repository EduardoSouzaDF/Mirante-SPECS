# Tarefas — Spec 0004

## Backend (Mock Server)
- [ ] Criar `src/lotes.js`: seed em memória (instituições, instituições
      responsáveis, situações, lotes com situações/valores/datas variados)
      + helpers de busca/filtro/paginação.
- [ ] Criar `mocks/routes/lotes.js` com `GET /api/filtros/lotes`.
- [ ] Adicionar `GET /api/lotes` (filtros + paginação) em `lotes.js`.
- [ ] Adicionar `GET /api/lotes/:id` em `lotes.js`.
- [ ] Adicionar `POST /api/lotes/:id/confirmar`, `/enviar`, `/excluir`
      (efeito real no seed) em `lotes.js`.
- [ ] Adicionar `POST /api/lotes/:id/incluir`, `/alterar`, `/justificativa`
      (placeholder 200) em `lotes.js`.
- [ ] Atualizar `mocks/collections.js` (collection `base`) com as novas
      rotas.
- [ ] Validar parâmetros inválidos → 400 (situação fora do enum, paginação
      não numérica).

## Frontend (Angular 17)
- [ ] Criar modelos tipados em `core/models/` (`Lote`, `Instituicao`,
      `Usuario`, `SituacaoLote`, `FiltroLote`, `FiltrosLoteOpcoes`,
      `PaginatedResponse<T>`, `BreadcrumbItem`).
- [ ] Refatorar `BreadcrumbComponent` para `@Input() items/homeLabel/
      showHome` + `@Output() home/itemClick`, e ajustar o uso em
      `LayoutComponent`.
- [ ] Criar `RangeFieldComponent` (`shared/range-field/`).
- [ ] Criar `FilterPanelComponent` (`shared/filter-panel/`).
- [ ] Criar `LoteTableComponent` (`shared/lote-table/`) com seleção
      (checkbox linha + "selecionar todos" com indeterminate).
- [ ] Criar `LoteActionsComponent` (`features/lotes/components/`) com as
      regras de habilitação por `selectionMode`.
- [ ] Criar `LoteDetailDialogComponent` (`features/lotes/components/`).
- [ ] Criar `LoteFacade` (`core/services/lote.facade.ts`): filtros, busca
      paginada, detalhe por id, seleção, `selectionMode$`.
- [ ] Criar `LotesPageComponent` (`features/lotes/`) juntando os
      componentes acima; registrar rota `lotes` em `app.routes.ts`.
- [ ] Testes unitários: `LoteFacade`, `RangeFieldComponent`,
      `FilterPanelComponent`, `LoteTableComponent` (seleção/indeterminate),
      `LoteActionsComponent` (regras de habilitação).
- [ ] Validação visual manual contra `../prototipos/consulta.png`.

## Transversais
- [x] Branch `feature/0004-consultar-lotes` criada nos 3 repos.
- [ ] Atualizar `state.md` da spec a cada mudança de fase.
- [ ] Commits + push + PR (frontend, backend, specs) só após validação do
      usuário.
