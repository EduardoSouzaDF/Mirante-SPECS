# Spec 0005 — Incluir Lançamento

- Status: em-planejamento
- Repos envolvidos: frontend | backend | ambos
- Criado em: 2026-09-13
- Última atualização: 2026-09-13

## Contexto
Hoje o botão "Incluir" na tela "Outros Créditos/Débitos" (spec 0004) é um
placeholder. Esta spec implementa de verdade a inclusão de um lançamento:
o usuário busca uma conta corrente, preenche os dados do lançamento, e o
sistema cria um lote novo (com esse lançamento) para a instituição dona da
conta encontrada.

## Objetivo
Permitir incluir um lançamento via modal acionada pelo botão "Incluir" da
tela de consulta de lotes, criando um lote novo para a instituição da conta
corrente escolhida, com o lançamento já dentro dele.

## Escopo

### Dentro do escopo
- Novas entidades no backend mockado: `ContaCorrente` (com relação 1:N a
  partir de `Instituicao`), `Arquivo`, `Lancamento` (com relação N:1 a
  `ContaCorrente`); `Lote` ganha `lancamentos: Lancamento[]`.
- `GET /api/lotes` (listagem) passa a incluir `lancamentos` no JSON de cada
  lote (não exibido na grade da tela de consulta, mas presente na resposta).
- **Mudança no modelo do Lote (evolui a spec 0004):** `valor` e
  `quantidadeLancamentos` deixam de ser campos próprios/persistidos do
  lote — passam a ser **calculados no frontend** a partir do array
  `lancamentos` de cada lote (`valor` = somatório de `lancamentos[].valor`;
  `quantidadeLancamentos` = `lancamentos.length`). O backend só precisa
  expor `lancamentos`; não envia mais `valor`/`quantidadeLancamentos`
  prontos.
- Busca de conta corrente por número (ícone de lupa): ao encontrar, mostra
  o nome da instituição titular ao lado do campo.
- Modal "Incluir Lançamento" (aberta pelo botão "Incluir" da tela de
  consulta), com formulário reativo:
  - Seção **Conta Corrente**: Conta Corrente (busca), Valor (monetário),
    Histórico (select), Estorno (checkbox), Documento (upload de
    arquivo(s)), Descrição (textarea), Situação (somente leitura,
    "Pendente").
  - Seção **Documento CSC**: PA (select) — demais campos desta seção são
    simplificados/mock (sem validação forte), por decisão do usuário.
  - Grade de lançamentos do lote sendo criado, com ações Visualizar,
    Incluir, Alterar, Excluir, Duplicar.
- Validações reativas (Angular `ReactiveFormsModule`): obrigatórios ao
  concluir são Conta Corrente, Valor, Histórico e pelo menos 1 Documento;
  mensagens de erro por campo; aviso de "campo obrigatório" na label desses
  campos; botão de confirmação (Incluir) só habilita com o formulário
  válido.
- Ao confirmar a inclusão: cria um lote novo (situação `Aberto`) para a
  instituição da conta corrente encontrada, com o lançamento dentro; o
  lançamento aparece na grade da modal (dados em memória no mock).

### Fora do escopo
- Editar/excluir/duplicar lançamentos de verdade (os botões existem na UI,
  mas a lógica de negócio de Alterar/Excluir/Duplicar um lançamento já
  criado é spec futura — nesta spec só "Incluir" tem lógica real).
- Upload de arquivo real (persistência de arquivo) — mock simula o
  `Arquivo` (nome + pathUrl fake), sem envio de binário de verdade.
- Regra de negócio real da seção "Documento CSC" além do campo PA.
- Reaproveitar um lote "Aberto" já existente da mesma instituição em vez de
  criar um novo — cada inclusão cria um lote novo, por decisão do usuário.

## Requisitos funcionais
1. Botão "Incluir" (já existente, sempre habilitado) abre a modal "Incluir
   Lançamento".
2. Campo Conta Corrente tem um ícone de lupa que dispara a busca da conta
   no mock; ao localizar, mostra o nome da instituição titular ao lado do
   campo; se não encontrar, mostra mensagem de erro.
3. Campos obrigatórios (Conta Corrente, Valor, Histórico, Documento — pelo
   menos 1 arquivo): rótulo com indicação visual de obrigatório; mensagem
   de erro específica quando inválido/vazio ao tentar submeter ou ao
   perder o foco.
4. Valor: numérico, monetário (máscara BRL), obrigatoriamente maior que
   zero.
5. Histórico: select com opções do enum (`Lançamento Manual` é a opção
   inicial/exemplo).
6. Situação: campo somente leitura, valor inicial "Pendente" — não
   editável pelo usuário.
7. Botão de confirmação (Incluir) só fica habilitado com o formulário
   inteiro válido.
8. Ao confirmar: cria um lote novo (situação Aberto) para a instituição da
   conta corrente encontrada, com um lançamento (situação Pendente) dentro
   dele; o lançamento passa a aparecer na grade de lançamentos da modal.
9. Grade de lançamentos (dentro da modal, do lote recém-criado) com botões
   Visualizar, Incluir (abre o formulário limpo de novo), Alterar, Excluir,
   Duplicar — só "Incluir" tem lógica de negócio real nesta spec; os
   demais ficam com UI pronta e handler placeholder.
10. `GET /api/lotes` (listagem paginada) passa a incluir `lancamentos` no
    JSON de cada item, mesmo sem exibir essa informação na tabela da tela
    de consulta.
11. As colunas "Valor" e "Quant. Lançamentos" da tabela de consulta (spec
    0004) passam a ser calculadas no frontend a partir de
    `lote.lancamentos` (somatório de valor e contagem), em vez de ler
    campos prontos do backend — atualiza `GenericTableComponent`/template
    de linha da tela de consulta para usar esse cálculo.

## Requisitos não-funcionais
- Formulário reativo (`FormGroup`/`FormControl`), tipagem explícita para
  as novas entidades (`ContaCorrente`, `Arquivo`, `Lancamento`,
  `EnumHistorico`).
- Componentização: modal própria (`app-incluir-lancamento-dialog` ou
  nome equivalente), reaproveitando padrões já usados (ex.:
  `LoteDetailDialogComponent`, `ScopeConfirmDialogComponent`).
- Mensagens de erro em pt-BR, claras e por campo.
- Segue os tokens visuais e convenções já usadas no projeto (PrimeIcons,
  paleta verde-petróleo).

## Critérios de aceite
- [ ] `GET /api/contas-correntes?numero=...` (ou rota equivalente) localiza
      a conta e devolve `{ conta, instituicao }`; 404 se não encontrar.
- [ ] `POST /api/lancamentos` (ou rota equivalente) recebe os dados do
      formulário + conta corrente, cria um lote novo (Aberto) para a
      instituição da conta, com o lançamento (Pendente) dentro; devolve o
      lote criado (incluindo o lançamento).
- [ ] `GET /api/lotes` inclui `lancamentos` no JSON de cada lote da
      listagem (não exibido na tabela da UI).
- [ ] Formulário não deixa confirmar sem Conta Corrente válida, Valor > 0,
      Histórico selecionado e ao menos 1 Documento anexado — cada campo
      mostra sua própria mensagem de erro; labels desses campos indicam
      "obrigatório".
- [ ] Situação sempre aparece como "Pendente", somente leitura, ao abrir a
      modal para incluir.
- [ ] Ao incluir com sucesso, o lançamento aparece na grade da modal, e a
      modal (ou o fluxo) permite ver esse lançamento criado.
- [ ] Botão Incluir (confirmação) desabilitado enquanto o formulário for
      inválido; habilita assim que os obrigatórios forem preenchidos
      corretamente.

## Dependências
- Spec 0004 (Consultar Lotes): tela, `LoteActionsComponent` (evento
  `incluir`), `LoteFacade`, backend de lotes.

## Riscos
- Upload de arquivo é só simulado (sem persistência real) — pode divergir
  do comportamento esperado se uma spec futura exigir upload de verdade.
- Seção "Documento CSC" fica com escopo reduzido por decisão explícita do
  usuário; pode precisar de mais campos/validação em spec futura.
- "Nome do titular" exibido ao localizar a conta é o nome da Instituicao
  vinculada à ContaCorrente (não existe campo "titular" separado na
  entidade ContaCorrente definida pelo usuário) — assunção a confirmar.
