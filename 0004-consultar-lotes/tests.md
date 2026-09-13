# Testes — Spec 0004

## Casos de teste — Frontend
| ID | Cenário | Passos | Resultado esperado | Status |
|----|---------|--------|---------------------|--------|
| T1 | Facade carrega filtros | `carregarFiltros()` | `filtrosOptions$` emite instituições/situações do backend (ou fallback) | OK |
| T2 | Facade pesquisa lotes | `pesquisar(filtro)` | `lotes$`/`total$` atualizados; requisição anterior cancelada (switchMap); estado atômico (filtro+página juntos) | OK |
| T3 | RangeFieldComponent emite faixa | Preencher De e Até | `rangeChange` emite `{ de, ate }` | OK |
| T4 | RangeFieldComponent moeda sem caractere solto (regressão) | Digitar letra após valor mascarado | Campo permanece só com o valor mascarado, sem a letra grudada | OK |
| T5 | FilterPanelComponent projeta conteúdo genérico | `<app-filter-panel>` com `titulo`/`subtitulo` e conteúdo projetado | Título/subtítulo aparecem; conteúdo projetado só visível quando expandido | OK |
| T6 | GenericTableComponent projeta cabeçalho/linhas | `<ng-template #header>`/`<ng-template #row>` | Conteúdo do host aparece nas células corretas | OK |
| T7 | GenericTableComponent seleciona todos | Marcar checkbox do cabeçalho | Todos os ids da página entram em `selectionChange`; checkbox = checked | OK |
| T8 | GenericTableComponent seleção parcial | Marcar 1 de N linhas | Checkbox do cabeçalho fica indeterminate | OK |
| T9 | LoteActionsComponent — Confirmar/Enviar/Justificativa sempre habilitados | `selectionMode = 'none' \| 'one' \| 'many'` | Os três sempre `habilitado() === true`, independente da seleção | OK |
| T10 | LoteActionsComponent — Alterar/Visualizar exigem 1 | `selectionMode = 'one'` vs `'many'`/`'none'` | Habilitados só com exatamente 1 selecionado | OK |
| T11 | LoteActionsComponent — Excluir exige 1+ | `selectionMode = 'none'` vs `'one'`/`'many'` | Desabilitado sem seleção, habilitado com 1+ | OK |
| T12 | BreadcrumbComponent por Input | `[items]="[...]"` | Renderiza os itens recebidos, sem depender da URL | OK |
| T13 | Fallback in-memory | Simular erro no `GET /api/filtros/lotes` | Facade usa dados locais de fallback sem travar a tela | OK |
| T14 | `LotesPageComponent` filtro de faixa (regressão do bug de rangeChange) | `onIdChange({de:3,ate:4})` + `onPesquisar()` | Requisição inclui `idDe=3&idAte=4` | OK |
| T15 | `LotesPageComponent` sem filtro manda situacao=Todas | `onPesquisar()` sem preencher nada | `situacao=Todas` na requisição | OK |
| T16 | `LotesPageComponent` Confirmar filtra por situação Aberto | Selecionar lotes Aberto+Confirmado+Enviado, `onAction('confirmar')` | `POST /api/lotes/confirmar` com `ids` só do(s) lote(s) Aberto(s) | OK |
| T17 | `LotesPageComponent` Enviar filtra por situação Confirmado | Selecionar lotes Aberto+Confirmado+Enviado, `onAction('enviar')` | `POST /api/lotes/enviar` com `ids` só do(s) lote(s) Confirmado(s) | OK |
| T18 | `LotesPageComponent` uma única requisição no carregamento (regressão) | Criar o componente | Só 1 `GET /api/lotes` (não 2) — `pesquisar({})` redundante removida do construtor | OK |

Rodados via `ng test --watch=false` (41/41 suites verdes) e
`playwright test` (6/6 — `e2e/login.spec.ts` + `e2e/lotes.spec.ts`).

## Casos de teste — Backend (mock scenarios)
| ID | Cenário | Mock/behavior utilizado | Resultado esperado | Status |
|----|---------|--------------------------|---------------------|--------|
| T1 | Buscar filtros | `GET /api/filtros/lotes` | 200 com `instituicoes` (7), `instituicoesResponsaveis` (3), `situacoes` | OK |
| T2 | Listar lotes sem filtro | `GET /api/lotes?page=1&size=10` | 200 com `data/total/page/size/hasNext/hasPrevious`; `total` = 24 | OK |
| T3 | Listar lotes com filtro por situação | `GET /api/lotes?situacao=Enviado` | 200 só com lotes na situação informada | OK |
| T4 | Listar lotes com faixa de valor | `GET /api/lotes?valorDe=200&valorAte=1500` | 200 só com lotes no intervalo | OK |
| T5 | Situação inválida | `GET /api/lotes?situacao=Inexistente` | 400 | OK |
| T6 | Detalhe do lote | `GET /api/lotes/:id` | 200 com lote completo (resp/usuários resolvidos, lançamentos) | OK |
| T7 | Confirmar em massa | `POST /api/lotes/confirmar` `{ids:[7,8]}` | 200 `{ lotes: [...] }`; situação dos dois lotes atualizada no seed numa única chamada | OK |
| T8 | Enviar em massa | `POST /api/lotes/enviar` `{ids:[...]}` | 200; situação atualizada no seed (mesmo helper `atualizarSituacaoEmMassa`) | OK |
| T9 | Ação em massa sem `ids` | `POST /api/lotes/confirmar` `{}` | 400 | OK |
| T10 | Excluir lote | `POST /api/lotes/:id/excluir` | 204; lote removido do seed | OK |
| T11 | Placeholder incluir/alterar/justificativa | `POST /api/lotes/incluir`, `/api/lotes/:id/alterar`, `/api/lotes/justificativa` `{ids}` | 200 com corpo básico, sem alterar o seed | OK |

Validado com `npm run mocks` + `curl` direto nas rotas; nenhum teste
automatizado de backend (projeto não usa framework de testes no
mock-server, mesmo padrão da spec 0003).

## Testes de integração (frontend + backend mockado)
- [x] Pesquisar com filtros combinados (select, faixa De/Até, Enter no
      campo) → tabela reflete exatamente os lotes filtrados no mock.
- [x] Selecionar 1 lote → Alterar/Visualizar abrem o modal com os dados de
      `GET /api/lotes/:id`.
- [x] Confirmar/Enviar com seleção mista de situações → só os lotes na
      situação certa são afetados, numa única chamada HTTP; lista
      recarrega e reflete o novo estado do seed.
- [x] Confirmar/Enviar/Justificativa sem seleção → modal de escopo
      aparece; "nesta página" aplica aos lotes visíveis, "todos" busca
      todo o filtro atual antes de aplicar.
- [x] Responsividade mobile (390px): tela não estoura horizontalmente
      (`document.scrollWidth === innerWidth`); tabela rola horizontalmente
      dentro do próprio container.
- [x] Menu lateral acompanha a altura da página quando a listagem cresce
      além de uma tela (sidebar e `.layout__main` com a mesma altura).
- [x] Validação visual interativa (Playwright headed, janela aberta) com o
      usuário, ajuste por ajuste, contra `../prototipos/consulta.png` e
      contra os pedidos feitos durante a sessão.
