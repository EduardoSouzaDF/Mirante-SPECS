# Tarefas — Spec 0005

## Backend (Mock Server)
- [x] Criar `src/instituicoes.js` (instituições extraídas, compartilhadas
      entre lotes/contas-correntes/lançamentos).
- [x] Criar `src/contas-correntes.js`: seed de `ContaCorrente` (`id`,
      `agencia`, `conta`, `instituicaoId`), uma por instituição.
- [x] Criar `src/lancamentos.js`: helpers para criar `Lancamento` (`id`,
      `contaCorrenteId`, `valor`, `historico`, `estorno`,
      `documentos: Arquivo[]`, `descricao`, `situacao` sempre `"Pendente"`
      na criação) com id global (não reinicia por lote).
- [x] Ajustar `src/lotes.js`: remover `valor`/`quantidadeLancamentos` da
      seed e do `paraApi()` (função única agora, sem `paraApiDetalhe`
      separado); sempre incluir `lancamentos` (completo) na resposta de
      listagem e detalhe.
- [x] Adicionar `calcularValorLote(lote)` (e
      `calcularQuantidadeLancamentos`) em `src/lotes.js`, usada
      internamente por `buscarLotes()` para os filtros `valorDe`/`valorAte`.
- [x] Adicionar função `criarLoteComLancamento(lancamento, instituicaoId)`
      em `src/lotes.js`: cria um lote novo (situação `Aberto`,
      `instituicaoId`/`instituicaoRespId` = instituição da conta) com o
      lançamento dentro; devolve o lote criado.
- [x] Criar `mocks/routes/contas-correntes.js` com
      `GET /api/contas-correntes?numero=...` (localiza a conta; 404 se não
      achar; devolve `{ conta, instituicao }`).
- [x] Criar `mocks/routes/lancamentos.js` com `POST /api/lancamentos`
      (cria lançamento + lote novo; 400 se `contaCorrenteId` inválido,
      `valor` ausente/≤0, `historico` ausente, `documentos` vazio).
- [x] Atualizar `mocks/collections.js` (collection `base`) com as rotas
      novas.
- [x] Validar manualmente (script Node, evitando problema de encoding do
      `curl` em bash com acentos): busca de conta (sucesso e 404),
      criação de lançamento (sucesso e cada validação 400), `GET /api/lotes`
      trazendo `lancamentos` sem `valor`/`quantidadeLancamentos`, e filtro
      `valorDe`/`valorAte` calculado batendo certo.

## Frontend (Angular 17)
- [x] Criar modelos: `core/models/conta-corrente.model.ts`
      (`ContaCorrente`, `ContaCorrenteBusca`), `core/models/lancamento.model.ts`
      (`Lancamento`, `ArquivoAnexo`, `EnumHistorico`, `IncluirLancamentoPayload`).
- [x] Ajustar `core/models/lote.model.ts`: remover `valor`/
      `quantidadeLancamentos` da interface `Lote`; `lancamentos:
      Lancamento[]` obrigatório direto em `Lote` (`LoteDetalhe` virou
      alias `@deprecated`).
- [x] Criar funções `calcularValorLote(lote)` /
      `calcularQuantidadeLancamentos(lote)` em `lote.model.ts` (utilitário
      puro, testado isoladamente em `lote.model.spec.ts`).
- [x] Ajustar `lotes-page.component.html` e `lote-detail-dialog.component.html`:
      colunas/campos de Valor e Quant. Lançamentos usam as funções de
      cálculo em vez de `lote.valor`/`lote.quantidadeLancamentos`.
- [x] Adicionar ao `LoteFacade`: `buscarContaCorrente(numero)` e
      `incluirLancamento(payload)` — após incluir, recarrega a listagem
      (mesmo padrão de `recarregarSubject` já usado); remove o antigo
      placeholder `incluir()`.
- [x] Criar `IncluirLancamentoDialogComponent`
      (`features/lotes/components/incluir-lancamento-dialog.component.ts`):
  - [x] Formulário reativo com os campos da seção Conta Corrente (busca
        com lupa, Valor, Histórico, Estorno, Documento(s), Descrição,
        Situação somente leitura "Pendente").
  - [x] Seção Documento CSC simplificada (campo PA).
  - [x] Validators: `required` em contaCorrenteId/valor/historico/documentos
        (mínimo 1 arquivo via validador customizado), `min(0.01)` no valor.
  - [x] Labels com marcador de "obrigatório" (`*` + legenda); mensagem de
        erro por campo.
  - [x] Botão de confirmação desabilitado enquanto `form.invalid`.
  - [x] Grade de lançamentos incluídos na sessão da modal, com barra
        Visualizar/Alterar/Excluir/Duplicar por linha (placeholder — só
        Incluir tem lógica real nesta spec).
- [x] Ligar `onAction('incluir')` em `LotesPageComponent` para abrir essa
      modal (trocou o placeholder `facade.incluir()`).
- [x] Testes unitários: `calcularValorLote`/`calcularQuantidadeLancamentos`
      (`lote.model.spec.ts`), `IncluirLancamentoDialogComponent`
      (validações, busca de conta sucesso/erro, habilitação do botão de
      confirmação, inclusão com sucesso reseta o formulário), facade
      (`buscarContaCorrente`, `incluirLancamento`), `LotesPageComponent`
      (`onAction('incluir')` abre a modal).
- [x] Validação inicial via Playwright headless: coluna Valor calculada
      bate com o protótipo, busca de conta exibe titular, botão Incluir
      trava sem documento e libera com tudo preenchido, inclusão real
      aparece na grade da modal.
- [ ] Validação visual interativa (Playwright headed, janela aberta) com o
      usuário — ajustes finais de UX/layout.

## Transversais
- [x] Branch `feature/0005-incluir-lancamento` criada nos 3 repos.
- [x] Atualizar `state.md` da spec a cada mudança de fase.
- [ ] Commits + push + PR (frontend, backend, specs) só após validação do
      usuário.
