# Testes — Spec 0005

## Casos de teste — Frontend
| ID | Cenário | Passos | Resultado esperado | Status |
|----|---------|--------|---------------------|--------|
| T1 | `calcularValorLote` soma os lançamentos | Lote com lançamentos `[100, 250.5]` | Retorna `350.5` | pendente |
| T2 | `calcularQuantidadeLancamentos` conta os lançamentos | Lote com 3 lançamentos | Retorna `3` | pendente |
| T3 | `calcularValorLote`/`calcularQuantidadeLancamentos` com lote vazio | Lote com `lancamentos: []` | Retorna `0` para ambos | pendente |
| T4 | Tabela de consulta usa valores calculados (regressão do modelo) | Lote sem campos `valor`/`quantidadeLancamentos` no objeto | Colunas Valor/Quant. Lançamentos exibem o somatório/contagem corretos | pendente |
| T5 | Modal abre ao clicar Incluir | `onAction('incluir')` | `IncluirLancamentoDialogComponent` fica visível | pendente |
| T6 | Busca de conta corrente com sucesso | Digitar número + clicar lupa (conta existe) | Nome da instituição aparece ao lado do campo | pendente |
| T7 | Busca de conta corrente sem sucesso | Digitar número + clicar lupa (conta não existe) | Mensagem de erro "conta não encontrada" | pendente |
| T8 | Validação: Conta Corrente obrigatória | Tentar confirmar sem localizar conta | Erro no campo; botão de confirmação desabilitado | pendente |
| T9 | Validação: Valor obrigatório e > 0 | Deixar vazio, ou digitar 0 | Erro "obrigatório" / "deve ser maior que zero" | pendente |
| T10 | Validação: Histórico obrigatório | Não selecionar | Erro no campo | pendente |
| T11 | Validação: ao menos 1 Documento | Não anexar nenhum arquivo | Erro "anexe ao menos um documento" | pendente |
| T12 | Situação somente leitura | Abrir a modal | Campo Situação mostra "Pendente", não editável | pendente |
| T13 | Botão de confirmação habilita só com formulário válido | Preencher todos os obrigatórios corretamente | Botão Incluir passa a `enabled` | pendente |
| T14 | Incluir com sucesso | Formulário válido → clicar Incluir | Lançamento aparece na grade da modal; facade recarrega a listagem | pendente |
| T15 | Labels marcam campos obrigatórios | Renderizar o formulário | Conta Corrente/Valor/Histórico/Documento têm indicador visual de obrigatório | pendente |

## Casos de teste — Backend (mock scenarios)
| ID | Cenário | Mock/behavior utilizado | Resultado esperado | Status |
|----|---------|--------------------------|---------------------|--------|
| T1 | Buscar conta corrente existente | `GET /api/contas-correntes?numero=...` | 200 com `{ conta, instituicao }` | pendente |
| T2 | Buscar conta corrente inexistente | `GET /api/contas-correntes?numero=...` | 404 | pendente |
| T3 | Criar lançamento com sucesso | `POST /api/lancamentos` (payload completo) | 200/201; cria lote novo (Aberto) com o lançamento (Pendente) dentro | pendente |
| T4 | Lote criado pertence à instituição da conta | Idem T3 | `lote.instituicaoId` == instituição da `contaCorrenteId` enviada | pendente |
| T5 | Validação: contaCorrenteId inválido | `POST /api/lancamentos` com id inexistente | 400 | pendente |
| T6 | Validação: valor ausente/≤0 | `POST /api/lancamentos` sem `valor` ou `valor: 0` | 400 | pendente |
| T7 | Validação: historico ausente | `POST /api/lancamentos` sem `historico` | 400 | pendente |
| T8 | Validação: documentos vazio | `POST /api/lancamentos` com `documentos: []` | 400 | pendente |
| T9 | `GET /api/lotes` traz lançamentos na listagem (regressão) | `GET /api/lotes?page=1&size=10` | Cada item de `data[]` tem `lancamentos: Lancamento[]`; não tem mais `valor`/`quantidadeLancamentos` | pendente |
| T10 | Filtro por valor continua funcionando sem o campo `valor` | `GET /api/lotes?valorDe=200&valorAte=1500` | 200 só com lotes cujo somatório de lançamentos cai no intervalo (cálculo interno) | pendente |

Validado com `npm run mocks` + `curl` direto nas rotas; nenhum teste
automatizado de backend (mesmo padrão das specs 0003/0004).

## Testes de integração (frontend + backend mockado)
- [ ] Clicar Incluir → buscar uma conta corrente real do seed → preencher
      Valor/Histórico/Documento → confirmar → lote novo aparece na tela de
      consulta (Valor e Quant. Lançamentos calculados batem com o
      lançamento incluído).
- [ ] Tentar confirmar com formulário incompleto → botão permanece
      desabilitado, mensagens de erro aparecem nos campos certos.
- [ ] Buscar conta corrente inexistente → mensagem de erro, sem travar o
      formulário.
- [ ] Validação visual interativa (Playwright headed, janela aberta) com o
      usuário — layout da modal, seções Conta Corrente/Documento CSC,
      grade de lançamentos, mensagens de obrigatório.
