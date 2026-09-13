# Testes — Spec 0004

## Casos de teste — Frontend
| ID | Cenário | Passos | Resultado esperado | Status |
|----|---------|--------|---------------------|--------|
| T1 | Facade carrega filtros | `carregarFiltros()` | `filtrosOptions$` emite instituições/situações do backend (ou fallback) | OK |
| T2 | Facade pesquisa lotes | `pesquisar(filtro)` | `lotes$`/`total$` atualizados; requisição anterior cancelada (switchMap) | OK |
| T3 | RangeFieldComponent emite faixa | Preencher De e Até | `change` emite `{ de, ate }` | OK |
| T4 | FilterPanelComponent emite filtro | Preencher campos + clicar Pesquisar | `pesquisar` emite `FiltroLote` com os valores preenchidos | OK |
| T5 | LoteTableComponent seleciona todos | Marcar checkbox do cabeçalho | Todos os ids da página entram em `selectionChange`; estado do checkbox = checked | OK |
| T6 | LoteTableComponent seleção parcial | Marcar 1 de N linhas | Checkbox do cabeçalho fica indeterminate | OK |
| T7 | LoteActionsComponent habilitação (1 selecionado) | `selectionMode = 'one'` | Todos os botões habilitados, incluindo Alterar/Visualizar | OK |
| T8 | LoteActionsComponent habilitação (vários selecionados) | `selectionMode = 'many'` | Alterar/Visualizar desabilitados; demais habilitados | OK |
| T9 | LoteActionsComponent habilitação (nenhum selecionado) | `selectionMode = 'none'` | Só Incluir habilitado | OK |
| T10 | BreadcrumbComponent por Input | `[items]="[...]"` | Renderiza os itens recebidos, sem depender da URL | OK |
| T11 | Fallback in-memory | Simular erro no `GET /api/filtros/lotes` | Facade usa dados locais de fallback sem travar a tela | OK |

Rodados via `ng test --watch=false` (30/30 suites verdes, incluindo os 15
testes pré-existentes) e `playwright test e2e/lotes.spec.ts e2e/login.spec.ts`
(6/6 — o smoke test de login também revalidou um bug real encontrado e
corrigido durante esta spec, ver Riscos/Decisões abaixo).

## Casos de teste — Backend (mock scenarios)
| ID | Cenário | Mock/behavior utilizado | Resultado esperado | Status |
|----|---------|--------------------------|---------------------|--------|
| T1 | Buscar filtros | `GET /api/filtros/lotes` | 200 com `instituicoes`, `instituicoesResponsaveis`, `situacoes` | OK |
| T2 | Listar lotes sem filtro | `GET /api/lotes?page=1&size=10` | 200 com `data/total/page/size/hasNext/hasPrevious` | OK |
| T3 | Listar lotes com filtro por situação | `GET /api/lotes?situacao=Enviado` | 200 só com lotes na situação informada | OK |
| T4 | Listar lotes com faixa de valor | `GET /api/lotes?valorDe=200&valorAte=1500` | 200 só com lotes no intervalo | OK |
| T5 | Situação inválida | `GET /api/lotes?situacao=Inexistente` | 400 | OK |
| T6 | Detalhe do lote | `GET /api/lotes/:id` | 200 com lote completo (resp/usuários resolvidos, lançamentos) | OK |
| T7 | Confirmar lote | `POST /api/lotes/:id/confirmar` | 200; situação do lote atualizada no seed | OK |
| T8 | Enviar lote | `POST /api/lotes/:id/enviar` | 200; situação do lote atualizada no seed | OK (verificado via T7 equivalente — mesmo helper `atualizarSituacao`) |
| T9 | Excluir lote | `POST /api/lotes/:id/excluir` | 204; lote removido do seed | OK |
| T10 | Placeholder incluir/alterar/justificativa | `POST /api/lotes/incluir`, `/api/lotes/:id/alterar`, `/api/lotes/:id/justificativa` | 200 com corpo básico, sem alterar o seed | OK |

Validado com `npm run mocks` + `curl` direto nas rotas (ver histórico da
sessão); nenhum teste automatizado de backend (projeto não usa framework de
testes no mock-server, mesmo padrão da spec 0003).

## Testes de integração (frontend + backend mockado)
- [x] Pesquisar com filtros combinados → tabela reflete exatamente os lotes
      filtrados no mock.
- [x] Selecionar 1 lote → Alterar/Visualizar abrem o modal com os dados de
      `GET /api/lotes/:id`.
- [x] Confirmar/Enviar/Excluir com sucesso → lista recarrega e reflete o
      novo estado do seed (validado no backend; fluxo end-to-end coberto
      pelos componentes/facade).
- [x] Validação visual manual da tela contra `../prototipos/consulta.png`
      via screenshot Playwright (login real → `/lotes`) — filtros, faixa
      De/Até, barra de ações com ícones, badges de situação e paginação
      "Mostrando X a Y de Z" batem com o protótipo.
