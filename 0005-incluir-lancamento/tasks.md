# Tarefas — Spec 0005

## Backend (Mock Server)
- [ ] Criar `src/contas-correntes.js`: seed de `ContaCorrente` (`id`,
      `agencia`, `conta`, `instituicaoId`), várias por instituição.
- [ ] Criar `src/lancamentos.js`: seed inicial vazia (ou poucos exemplos),
      helpers para criar `Lancamento` (`id`, `contaCorrenteId`, `valor`,
      `historico`, `estorno`, `documentos: Arquivo[]`, `descricao`,
      `situacao` sempre `"Pendente"` na criação) e `proximoIdLancamento()`.
- [ ] Ajustar `src/lotes.js`: remover `valor`/`quantidadeLancamentos` da
      seed e do `paraApi()`/`paraApiDetalhe()`; sempre incluir
      `lancamentos` (completo) na resposta de listagem e detalhe.
- [ ] Adicionar `calcularValorLote(lote)` em `src/lotes.js`, usada
      internamente por `buscarLotes()` para os filtros `valorDe`/`valorAte`
      (a comparação já não pode usar `lote.valor` direto).
- [ ] Adicionar função `criarLoteComLancamento(lancamento, instituicaoId)`
      em `src/lotes.js`: cria um lote novo (situação `Aberto`,
      `instituicaoId`/`instituicaoRespId` = instituição da conta) com o
      lançamento dentro; devolve o lote criado.
- [ ] Criar `mocks/routes/contas-correntes.js` com
      `GET /api/contas-correntes?numero=...` (localiza a conta; 404 se não
      achar; devolve `{ conta, instituicao }`).
- [ ] Criar `mocks/routes/lancamentos.js` com `POST /api/lancamentos`
      (cria lançamento + lote novo; 400 se `contaCorrenteId` inválido,
      `valor` ausente/≤0, `historico` ausente, `documentos` vazio).
- [ ] Atualizar `mocks/collections.js` (collection `base`) com as rotas
      novas.
- [ ] Validar manualmente com `curl`: busca de conta (sucesso e 404),
      criação de lançamento (sucesso e cada validação 400), e conferir que
      `GET /api/lotes` passou a trazer `lancamentos` em todos os itens.

## Frontend (Angular 17)
- [ ] Criar modelos: `core/models/conta-corrente.model.ts`
      (`ContaCorrente`), `core/models/lancamento.model.ts` (`Lancamento`,
      `ArquivoAnexo`, `EnumHistorico`).
- [ ] Ajustar `core/models/lote.model.ts`: remover `valor`/
      `quantidadeLancamentos` da interface `Lote`; `lancamentos:
      Lancamento[]` passa a ser obrigatório em `Lote` (não só em
      `LoteDetalhe`).
- [ ] Criar funções `calcularValorLote(lote)` /
      `calcularQuantidadeLancamentos(lote)` (utilitário puro, testável
      isoladamente).
- [ ] Ajustar `lotes-page.component.html`: colunas Valor/Quant.
      Lançamentos da tabela usam as funções de cálculo em vez de
      `lote.valor`/`lote.quantidadeLancamentos`.
- [ ] Adicionar ao `LoteFacade` (ou criar `LancamentoFacade`):
      `buscarContaCorrente(numero)` e `incluirLancamento(payload)` —
      após incluir, recarrega a listagem (mesmo padrão de
      `recarregarSubject` já usado).
- [ ] Criar `IncluirLancamentoDialogComponent`
      (`features/lotes/components/incluir-lancamento-dialog.component.ts`):
  - [ ] Formulário reativo com os campos da seção Conta Corrente (busca
        com lupa, Valor, Histórico, Estorno, Documento(s), Descrição,
        Situação somente leitura "Pendente").
  - [ ] Seção Documento CSC simplificada (campo PA).
  - [ ] Validators: `required` em contaCorrente/valor/historico/documentos
        (mínimo 1 arquivo), `min(0.01)` no valor.
  - [ ] Labels com marcador de "obrigatório"; mensagem de erro por campo.
  - [ ] Botão de confirmação desabilitado enquanto `form.invalid`.
  - [ ] Grade de lançamentos do lote sendo criado (reaproveita
        `GenericTableComponent`) com barra Visualizar/Incluir/Alterar/
        Excluir/Duplicar (só Incluir com lógica real nesta spec).
- [ ] Ligar `onAction('incluir')` em `LotesPageComponent` para abrir essa
      modal (troca o placeholder `facade.incluir()`).
- [ ] Testes unitários: `calcularValorLote`/`calcularQuantidadeLancamentos`,
      `IncluirLancamentoDialogComponent` (validações, habilitação do botão
      de confirmação, emissão ao incluir), facade (`buscarContaCorrente`,
      `incluirLancamento`), `LotesPageComponent` (colunas calculadas).
- [ ] Validação visual interativa (Playwright headed) com o usuário:
      abrir modal, buscar conta, preencher formulário, validações,
      incluir, conferir lote novo na listagem.

## Transversais
- [x] Branch `feature/0005-incluir-lancamento` criada nos 3 repos.
- [ ] Atualizar `state.md` da spec a cada mudança de fase.
- [ ] Commits + push + PR (frontend, backend, specs) só após validação do
      usuário.
