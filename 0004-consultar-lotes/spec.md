# Spec 0004 — Consultar Lotes

- Status: concluida
- Repos envolvidos: frontend | backend | ambos
- Criado em: 2026-09-13
- Última atualização: 2026-09-13

## Contexto
O módulo "Outros Créditos/Débitos" precisa de uma tela de pesquisa de lotes,
onde o usuário filtra, lista e age sobre lotes (confirmar, enviar, incluir,
alterar, excluir, visualizar, ver justificativa). Protótipo de referência:
`prototipos/consulta.png`.

## Objetivo
Permitir consultar lotes por filtros (instituição, situação, faixas de ID,
valor e data), listar os resultados paginados numa tabela com seleção, e
disparar ações sobre o(s) lote(s) selecionado(s) — inclusive em massa,
sobre todos os lotes de uma página ou de todo o filtro, sem precisar
selecionar linha por linha.

## Escopo

### Dentro do escopo
- Tela "Outros Créditos/Débitos" com breadcrumb, painel de filtros
  recolhível, barra de ações e tabela paginada de lotes; acessível pelo
  menu lateral ("Outros Créditos/Débitos").
- Backend mockado: rotas de filtros disponíveis, listagem paginada de lotes,
  detalhe de um lote, e ações em massa (confirmar/enviar/justificativa
  recebem uma lista de ids numa única chamada).
- Ações que alteram estado real no mock: Confirmar (só lotes Abertos),
  Enviar (só lotes Confirmados), Excluir.
- Ações que só abrem o modal de detalhe ou não têm regra de negócio real
  nesta spec: Visualizar, Visualizar Justificativa, Incluir, Alterar —
  endpoints respondem 200 com corpo básico (placeholder), tratados a fundo
  em spec futura.
- Modal de escopo: ao clicar Confirmar/Enviar/Visualizar Justificativa sem
  nenhum lote selecionado, pergunta se a ação é só para os lotes desta
  página ou para todos os lotes do filtro atual (todas as páginas).
- Componentização reutilizável e genérica: breadcrumb (por `@Input`), campo
  de faixa "De/Até", painel de filtros genérico (`FilterPanelComponent`,
  título/subtítulo por Input, corpo do painel projetado por quem usa via
  `ng-content`), tabela genérica (`GenericTableComponent`, cabeçalho e
  linhas fornecidos por quem usa via `<ng-template #header>`/`<ng-template
  #row let-item>`, com seleção e paginação genéricas).
- Estado da tela via RxJS (facade/service), sem uso de signals para os dados
  remotos (segue o padrão já usado no `AuthService`).
- Responsivo: painel de filtros colapsa para 1 coluna e a tela não estoura
  horizontalmente em telas móveis; menu lateral acompanha a altura real do
  conteúdo (não corta mais quando a listagem cresce além de uma tela).

### Fora do escopo
- Regra de negócio real de Incluir/Alterar/Justificativa (formulários,
  validações específicas) — spec futura.
- Backend real (fora do mock-server).
- Autorização por perfil/permissão nas ações.

## Requisitos funcionais
1. Buscar opções de filtro (`instituicoes`, `instituicoesResponsaveis`,
   `situacoes`) no carregamento da tela.
2. Filtrar lotes por Instituição Resp., Instituição, Situação do Lote, ID
   Lote (De/Até), Valor Lote (De/Até) e Data Entrada (De/Até); botão
   "Pesquisar" (`type="submit"` dentro de um `<form>`) dispara a busca, e
   também a tecla Enter em qualquer campo do filtro.
3. Listar lotes em tabela paginada com colunas: checkbox, ID, Data Entrada,
   Valor, Quant. Lançamentos, Usuário Registro, Usuário Aprovação, Situação,
   Data/Hora Situação (formato pt-BR `dd/mm/aaaa HH:MM`).
4. Selecionar um ou vários lotes via checkbox (linha e "selecionar todos" no
   cabeçalho, com estado indeterminado quando a seleção é parcial).
5. Exibir barra de ações acima da tabela: Confirmar, Enviar, Visualizar
   Justificativa, Incluir, Alterar, Excluir, Visualizar.
   - Confirmar, Enviar, Visualizar Justificativa: **sempre habilitados**,
     independente de seleção.
   - Excluir: habilitado com pelo menos 1 lote selecionado.
   - Incluir: sempre habilitado (não depende de seleção).
   - Alterar, Visualizar: habilitados apenas com exatamente 1 lote
     selecionado.
6. Ao clicar Confirmar/Enviar/Visualizar Justificativa **com** lotes
   selecionados, aplica direto nos selecionados. **Sem** seleção, abre um
   modal perguntando o escopo: "somente os lotes desta página" ou "todos os
   lotes do filtro atual" (a segunda opção só aparece se houver mais de uma
   página de resultados).
7. Confirmar só considera, dentro do conjunto escolhido (seleção ou
   escopo), os lotes com situação **Aberto**; os demais são ignorados.
   Enviar só considera os com situação **Confirmado**. Visualizar
   Justificativa não filtra por situação.
8. Cada uma dessas três ações dispara **uma única chamada HTTP**, com todos
   os ids do conjunto final (já filtrado por situação quando aplicável) —
   nunca uma chamada por lote.
9. Abrir modal de detalhe do lote (Visualizar/Alterar) buscando
   `GET /api/lotes/:id`, mostrando todos os campos do lote.
10. Excluir continua agindo por id, um a um (fora do escopo desta mudança
    de "chamada única").

## Requisitos não-funcionais
- Tipagem explícita para os modelos (`Lote`, `Instituicao`, `Usuario`,
  `SituacaoLote`, `FiltroLote`, respostas paginadas).
- Componentes standalone (Angular 17), seguindo o estilo já usado no projeto
  (template/styles inline ou separados, tokens SCSS existentes, PrimeFlex
  para layout, PrimeIcons nos botões da barra de ações).
- Responsivo (mobile-first já usado no `layout.component.ts`/sidebar); grid
  de filtros usa `minmax(0, 1fr)` em vez de `1fr` puro para não estourar a
  tela quando um `<select>`/input tiver texto mais largo que a coluna.
- Fallback local (dados em memória) se o mock-server estiver indisponível ao
  carregar os filtros, para não travar a tela.

## Critérios de aceite
- [x] `GET /api/filtros/lotes` retorna instituições, instituições
      responsáveis e situações.
- [x] `GET /api/lotes` aceita todos os filtros da tela + paginação e
      devolve `{ data, total, page, size, hasNext, hasPrevious }`.
- [x] `GET /api/lotes/:id` devolve o lote completo (nomes resolvidos de
      responsável/usuários, quantidade de lançamentos, lançamentos).
- [x] `POST /api/lotes/confirmar` e `/enviar` recebem `{ ids: number[] }`,
      retornam 200 e alteram a situação dos lotes no mock numa única
      chamada; `POST /api/lotes/:id/excluir` retorna 204 e remove o lote.
- [x] Parâmetro de filtro inválido (ex.: situação inexistente, `ids`
      ausente/vazio nas ações em massa) retorna 400.
- [x] Breadcrumb da tela via `[items]`: "Início > Outros Créditos/Débitos",
      com "Início" navegando para `/dashboard"; item de menu lateral leva
      à tela.
- [x] Botão "Pesquisar" com espaçamento visualmente distinto dos demais
      campos do painel de filtros; Enter em qualquer campo também pesquisa.
- [x] Barra de ações mostra ícone à esquerda de cada botão, fundo cinza;
      Confirmar/Enviar/Justificativa sempre habilitados, Excluir com 1+,
      Alterar/Visualizar com exatamente 1.
- [x] Confirmar/Enviar sem seleção abrem modal de escopo (página/todos);
      Confirmar só afeta lotes Abertos, Enviar só Confirmados, dentro do
      conjunto escolhido; uma única chamada HTTP por ação.
- [x] Tabela replica as colunas e a paginação do protótipo
      (`prototipos/consulta.png`), com a coluna extra Data/Hora Situação.
- [x] Tela não estoura horizontalmente em mobile; menu lateral acompanha a
      altura do conteúdo quando a listagem cresce.

## Dependências
- Backend: `@mocks-server/main` já configurado (spec 0003).
- Frontend: `AuthService`/interceptors/loading já existentes (spec 0003).

## Riscos
- Estado de seleção pode ficar inconsistente entre páginas (seleção não é
  limpa automaticamente ao trocar de página nesta versão).
- Fallback in-memory pode divergir do mock-server em formato de dados se um
  dos dois for alterado sem atualizar o outro.

## Bugs reais encontrados e corrigidos durante a implementação
Detalhes completos em `state.md`; resumo:
- `LayoutComponent` quebrava a navegação pós-login (optional chaining
  incompleto no breadcrumb).
- `LoteFacade.pesquisar()` disparava duas buscas (filtro novo + página
  antiga) por usar dois `BehaviorSubject` separados — unificado num só.
- `LotesPageComponent` chamava `facade.pesquisar({})` no construtor mesmo
  já disparando a busca inicial sozinho ao assinar `lotes$`/`total$` —
  causava 2 requisições GET /api/lotes a cada carregamento da tela.
- Output `rangeChange` do `RangeFieldComponent` tinha o mesmo nome do
  evento nativo `change`, que borbulhava dos `<input>` internos e
  sobrescrevia o valor certo com um `Event` cru — quebrava os filtros de
  faixa (ID/Valor/Data) por completo.
- Campo de moeda (Valor Lote): caractere inválido digitado ficava "colado"
  no input porque o binding `[value]` não reescrevia o DOM quando o texto
  sanitizado não mudava — corrigido escrevendo o valor direto no elemento.
- CSS do `GenericTableComponent` não batia mais nas células de cabeçalho/
  linha depois que passaram a ser projetadas via `ngTemplateOutlet` do
  template de quem usa (atributo de encapsulamento diferente) — corrigido
  com `:host ::ng-deep`.
- Grid de filtros com `1fr` puro "estourava" a tela no mobile (min-width
  implícito do CSS Grid) — corrigido com `minmax(0, 1fr)`.
- Sidebar só tinha `min-height:100vh`, não acompanhava a altura real da
  página quando o conteúdo principal crescia além de uma tela — corrigido
  com `height:100%` no elemento interno.
