# Testes — Spec 0004

## Casos de teste — Frontend
| ID | Cenário | Passos | Resultado esperado | Status |
|----|---------|--------|---------------------|--------|
| T1 | Facade carrega filtros | `loadFiltros()` | `filtrosOptions$` emite instituições/situações do backend (ou fallback) | pendente |
| T2 | Facade pesquisa lotes | `pesquisarLotes(filtro, page, size)` | `lotes$`/`total$` atualizados; requisição anterior cancelada (switchMap) | pendente |
| T3 | RangeFieldComponent emite faixa | Preencher De e Até | `change` emite `{ de, ate }` | pendente |
| T4 | FilterPanelComponent emite filtro | Preencher campos + clicar Pesquisar | `pesquisar` emite `FiltroLote` com os valores preenchidos | pendente |
| T5 | LoteTableComponent seleciona todos | Marcar checkbox do cabeçalho | Todos os ids da página entram em `selectionChange`; estado do checkbox = checked | pendente |
| T6 | LoteTableComponent seleção parcial | Marcar 1 de N linhas | Checkbox do cabeçalho fica indeterminate | pendente |
| T7 | LoteActionsComponent habilitação (1 selecionado) | `selectionMode = 'one'` | Todos os botões habilitados, incluindo Alterar/Visualizar | pendente |
| T8 | LoteActionsComponent habilitação (vários selecionados) | `selectionMode = 'many'` | Alterar/Visualizar desabilitados; demais habilitados | pendente |
| T9 | LoteActionsComponent habilitação (nenhum selecionado) | `selectionMode = 'none'` | Só Incluir habilitado | pendente |
| T10 | BreadcrumbComponent por Input | `[items]="[...]"` | Renderiza os itens recebidos, sem depender da URL | pendente |
| T11 | Fallback in-memory | Simular erro no `GET /api/filtros/lotes` | Facade usa dados locais de fallback sem travar a tela | pendente |

## Casos de teste — Backend (mock scenarios)
| ID | Cenário | Mock/behavior utilizado | Resultado esperado | Status |
|----|---------|--------------------------|---------------------|--------|
| T1 | Buscar filtros | `GET /api/filtros/lotes` | 200 com `instituicoes`, `instituicoesResponsaveis`, `situacoes` | pendente |
| T2 | Listar lotes sem filtro | `GET /api/lotes?page=1&size=10` | 200 com `data/total/page/size/hasNext/hasPrevious` | pendente |
| T3 | Listar lotes com filtro por situação | `GET /api/lotes?situacao=Aberto` | 200 só com lotes na situação informada | pendente |
| T4 | Listar lotes com faixa de valor | `GET /api/lotes?valorDe=100&valorAte=500` | 200 só com lotes no intervalo | pendente |
| T5 | Situação inválida | `GET /api/lotes?situacao=Inexistente` | 400 | pendente |
| T6 | Detalhe do lote | `GET /api/lotes/:id` | 200 com lote completo (resp/usuários resolvidos, lançamentos) | pendente |
| T7 | Confirmar lote | `POST /api/lotes/:id/confirmar` | 200; situação do lote atualizada no seed | pendente |
| T8 | Enviar lote | `POST /api/lotes/:id/enviar` | 200; situação do lote atualizada no seed | pendente |
| T9 | Excluir lote | `POST /api/lotes/:id/excluir` | 204; lote removido do seed | pendente |
| T10 | Placeholder incluir/alterar/justificativa | `POST /api/lotes/:id/incluir` (e afins) | 200 com corpo básico, sem alterar o seed | pendente |

## Testes de integração (frontend + backend mockado)
- Pesquisar com filtros combinados → tabela reflete exatamente os lotes
  filtrados no mock.
- Selecionar 1 lote → Visualizar abre o modal com os dados de
  `GET /api/lotes/:id`.
- Confirmar/Enviar/Excluir com sucesso → lista recarrega e reflete o novo
  estado do seed.
- Validação visual manual da tela contra `../prototipos/consulta.png`
  (layout dos filtros, faixa De/Até, barra de ações, paginação).
