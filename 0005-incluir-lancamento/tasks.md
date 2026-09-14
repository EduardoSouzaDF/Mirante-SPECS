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
- [x] Adicionar função `criarLoteComLancamento(lancamento, instituicaoId,
      usuarioRegistroId)` em `src/lotes.js`: cria um lote novo (situação
      `Aberto`, `instituicaoId`/`instituicaoRespId` = instituição da conta,
      `usuarioRegistro` = usuário autenticado no token) com o lançamento
      dentro; devolve o lote criado.
- [x] `paraApi(lote)`: o objeto `instituicao` inclui `contasCorrentes`
      (via `listarContasPorInstituicao`), tanto na listagem quanto no
      detalhe — não aparece na tela, mas precisa vir no JSON.
- [x] Criar `mocks/routes/contas-correntes.js` com
      `GET /api/contas-correntes?numero=...` (localiza a conta; 404 se não
      achar; devolve `{ conta, instituicao }`) e, sem `numero`, lista todas
      as contas com a instituição (para o select pesquisável do front).
- [x] Criar `mocks/routes/lancamentos.js` com `POST /api/lancamentos`
      (cria lançamento + lote novo; 400 se `contaCorrenteId` inválido,
      `valor` ausente/≤0, `historico` ausente, `documentos` vazio);
      `usuarioRegistro` do lote criado é resolvido a partir do token JWT-like
      do request (`src/auth-token.js`), não um placeholder do seed.
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
  - [x] Conta Corrente é um select pesquisável (`<input list>` +
        `<datalist>`), cada opção mostrando
        `"{Instituicao.nome} - agencia: {agencia} conta: {conta}"`.
  - [x] Documento é uma área de arrastar-e-soltar (dropzone), como sites
        comuns usam, além do clique para escolher arquivo.
  - [x] Valor usa a mesma máscara BRL em tempo real do filtro "Valor Lote"
        (extraída para `shared/utils/mascara-moeda.util.ts`, reaproveitada
        pelo `RangeFieldComponent`).
  - [x] Ao concluir a inclusão com sucesso, a modal fecha imediatamente
        (`incluido` + `closed` emitidos) em vez de ficar aberta para uma
        sessão de múltiplas inclusões — removida a grade "lançamentos
        incluídos nesta sessão" e os botões placeholder por linha.
- [x] Ligar `onAction('incluir')` em `LotesPageComponent` para abrir essa
      modal (trocou o placeholder `facade.incluir()`).
- [x] Criar `AvisoToastComponent` (`shared/aviso-toast/`): aviso não-modal
      reutilizável com `@Input` de `titulo`, `mensagem`, `tipo`
      (`success`/`warning`/`info`) e `tempo` (segundos); barra de
      progresso anima o tempo se esvaindo e fecha sozinho ao zerar.
      `LotesPageComponent` mostra esse aviso quando
      `IncluirLancamentoDialogComponent` emite `incluido`.
- [x] Testes unitários: `calcularValorLote`/`calcularQuantidadeLancamentos`
      (`lote.model.spec.ts`), `IncluirLancamentoDialogComponent`
      (validações, busca de conta sucesso/erro, habilitação do botão de
      confirmação, inclusão com sucesso fecha a modal e emite `incluido`),
      `AvisoToastComponent` (visibilidade, conteúdo, fechamento automático
      por tempo via `fakeAsync`, fechamento manual), facade
      (`buscarContaCorrente`, `listarContasCorrentes`, `incluirLancamento`),
      `LotesPageComponent` (`onAction('incluir')` abre a modal).
- [x] Validação via Playwright: coluna Valor calculada bate com o
      protótipo, busca de conta exibe titular, botão Incluir trava sem
      documento e libera com tudo preenchido; fluxo completo de inclusão
      real (select pesquisável + dropzone + POST /api/lancamentos)
      confirma `usuarioRegistro` do lote como o usuário autenticado, modal
      fecha e o toast de sucesso aparece, anima a barra e some sozinho.
- [x] Validação visual interativa (Playwright headed, janela aberta) com o
      usuário — confirmado, sem ajustes adicionais de UX/layout.

## Transversais
- [x] Branch `feature/0005-incluir-lancamento` criada nos 3 repos.
- [x] Atualizar `state.md` da spec a cada mudança de fase.
- [x] Commits + push + PR (frontend, backend, specs) após validação do
      usuário.
