# Plano — Spec 0005

## Abordagem técnica
Backend ganha 3 entidades novas (`ContaCorrente`, `Arquivo`, `Lancamento`) e
o `Lote` deixa de guardar `valor`/`quantidadeLancamentos` como campos
próprios — essas informações passam a ser sempre calculadas a partir de
`lote.lancamentos` (soma de valor e contagem), com o **frontend** fazendo
esse cálculo para exibição, e o **backend** fazendo o mesmo cálculo
internamente sempre que precisar filtrar/ordenar por valor. Uma modal nova
(`IncluirLancamentoDialogComponent`) abre a partir do botão "Incluir" já
existente na tela de consulta (spec 0004), com formulário reativo para
localizar uma conta corrente e criar o lançamento; ao confirmar, cria-se um
lote novo (Aberto) para a instituição da conta, com o lançamento dentro.

## Modelo de dados (backend, `src/`)
- **`ContaCorrente`** (`src/contas-correntes.js`): `{ id, agencia, conta,
  instituicaoId }` — relação N:1 com `Instituicao` (reaproveita a lista já
  existente em `src/lotes.js`, ou movida para um módulo compartilhado
  `src/instituicoes.js` se fizer sentido para evitar import circular).
- **`Arquivo`** (inline, não precisa de módulo próprio): `{ id, nome,
  pathUrl }` — `pathUrl` é só um valor mock (ex.:
  `/mock-files/<uuid>-<nome>`), sem upload real.
- **`Lancamento`** (`src/lancamentos.js`): `{ id, contaCorrenteId, valor,
  historico, estorno, documentos: Arquivo[], descricao, situacao }`.
  `historico` é um enum (`"Lançamento Manual"` como único valor por
  enquanto — `EnumHistorico`); `situacao` inicia sempre `"Pendente"`.
- **`Lote`** (`src/lotes.js`, ajustado): perde os campos `valor` e
  `quantidadeLancamentos` da seed e do `paraApi()`; `lancamentos:
  Lancamento[]` (array de `Lancamento` completos, não só `{id, descricao,
  valor}` como hoje) passa a ser incluído em **todas** as respostas de
  lote (listagem e detalhe) — antes só vinha no detalhe.
  - Função interna `calcularValorLote(lote)` /
    `calcularQuantidadeLancamentos(lote)` usadas pelo próprio backend para
    filtrar por `valorDe`/`valorAte` em `buscarLotes()` (já que o campo
    `valor` não existe mais para comparar direto).

## Backend (Mock Server)
- **Novas rotas** (`mocks/routes/`):
  - `GET /api/contas-correntes?numero=NNNNNN` (ou `agencia`+`conta`) —
    localiza a conta e devolve `{ conta, instituicao }`; 404 se não achar.
  - `POST /api/lancamentos` — corpo `{ contaCorrenteId, valor, historico,
    estorno, documentos: {nome}[], descricao }`; cria um `Lancamento`
    (situacao `Pendente`), cria um `Lote` novo (situação `Aberto`,
    `instituicaoId` = instituição da conta corrente, `instituicaoRespId`
    = mesma instituição por ora) com esse lançamento dentro, e devolve o
    lote criado (já com `lancamentos`).
- **Rotas existentes ajustadas:**
  - `GET /api/lotes` e `GET /api/lotes/:id`: `paraApi()` deixa de expor
    `valor`/`quantidadeLancamentos`; sempre inclui `lancamentos` (array
    completo de `Lancamento`).
  - `buscarLotes()`: usa `calcularValorLote(lote)` para os filtros
    `valorDe`/`valorAte` (a comparação some dentro da função, o filtro
    continua funcionando igual pra quem consome a API).
- **Validação:** `POST /api/lancamentos` → 400 se `contaCorrenteId` não
  existir, `valor` ausente/≤0, `historico` ausente, ou `documentos` vazio/
  ausente.

## Frontend (Angular 17)
- **Modelos novos** (`core/models/lancamento.model.ts` e ajustes em
  `lote.model.ts`):
  - `ContaCorrente { id, agencia, conta, instituicao: Instituicao }`.
  - `ArquivoAnexo { id, nome, pathUrl }`.
  - `EnumHistorico` (union type, hoje só `'Lançamento Manual'`).
  - `Lancamento { id, contaCorrenteId, valor, historico, estorno,
    documentos: ArquivoAnexo[], descricao, situacao: 'Pendente' | ... }`.
  - `Lote`: remove `valor`/`quantidadeLancamentos` da interface — passam a
    ser funções puras/getters (`calcularValorLote(lote)`,
    `calcularQuantidadeLancamentos(lote)`) num arquivo utilitário
    (`core/models/lote.util.ts` ou dentro do próprio `lote.model.ts`),
    usadas onde hoje se lia `lote.valor`/`lote.quantidadeLancamentos`.
- **Tela de consulta (spec 0004) ajustada:** o `<ng-template #row>` do
  `GenericTableComponent` em `lotes-page.component.html` passa a chamar
  essas funções (`calcularValorLote(lote)` / `calcularQuantidadeLancamentos(lote)`)
  em vez de `lote.valor`/`lote.quantidadeLancamentos`.
- **Serviço:** `LancamentoFacade` (ou métodos adicionados ao `LoteFacade`)
  com `buscarContaCorrente(numero)` e `incluirLancamento(payload)`.
- **Componente novo:** `IncluirLancamentoDialogComponent`
  (`features/lotes/components/`), formulário reativo
  (`FormGroup`/`FormBuilder`):
  - Seção Conta Corrente: campo de busca (input + botão lupa) ligado a
    `buscarContaCorrente()`; ao achar, mostra nome da instituição titular;
    Valor (`RangeFieldComponent` não serve aqui — precisa de um campo de
    moeda único, não faixa; reaproveitar a lógica de máscara BRL do
    `RangeFieldComponent` extraída para um pipe/diretiva reutilizável, ou
    um novo `CurrencyInputComponent` simples); Histórico (select);
    Estorno (checkbox); Documento (input file, múltiplos arquivos,
    lista dos anexados com opção de remover); Descrição (textarea);
    Situação (input readonly, sempre "Pendente").
  - Seção Documento CSC: PA (select com poucas opções mock); demais campos
    da seção ficam simplificados/sem validação forte.
  - Grade de lançamentos do lote sendo criado (usa `GenericTableComponent`
    de novo, com colunas simples); barra de ações Visualizar/Incluir/
    Alterar/Excluir/Duplicar (só "Incluir" plugado nesta spec).
  - Validação: `Validators.required` em contaCorrente/valor/historico;
    validador customizado `min(0.01)` pro valor; validador customizado
    "pelo menos 1 arquivo" pra documentos; labels com marcador de
    obrigatório; mensagens de erro por campo abaixo de cada input.
  - Botão de confirmação (`[disabled]="form.invalid"`).
- **Integração com `LotesPageComponent`:** `onAction('incluir')` abre essa
  modal (hoje chama `facade.incluir()` placeholder); ao confirmar, chama
  `incluirLancamento()`, fecha ou atualiza a modal, e recarrega a listagem
  (o lote novo criado deve aparecer na tela de consulta).

## Sequenciamento
1. Backend: entidades (`ContaCorrente`, `Lancamento`) → ajustar `Lote`
   (remover valor/quantidadeLancamentos, sempre expor `lancamentos`) →
   rota de busca de conta → rota de criação de lançamento → validações.
2. Frontend: modelos + funções de cálculo → ajustar tabela da tela de
   consulta pros valores calculados → `LancamentoFacade`/métodos no
   `LoteFacade` → `IncluirLancamentoDialogComponent` (form reativo,
   validações, grade) → ligar no `onAction('incluir')`.
3. Testes (unitários do form/validação/cálculo, facade, backend via curl)
   → validação visual interativa com o usuário → commits/PR.

## Decisões de arquitetura
- `valor`/`quantidadeLancamentos` do lote nunca são persistidos como
  campos — sempre derivados de `lancamentos`, eliminando risco de
  divergência entre o que a UI mostra e a soma real dos lançamentos.
- Cada inclusão de lançamento sempre cria um lote novo (não reaproveita um
  lote "Aberto" já existente da mesma instituição) — decisão explícita do
  usuário, mais simples de implementar e entender nesta spec.
- Upload de arquivo é só simulado (sem persistência/binário real).
- Seção "Documento CSC" fica com validação mínima (só o campo PA é
  obrigatório-mock, sem regra de negócio real) — os demais campos, se
  existirem, são decorativos nesta spec.
