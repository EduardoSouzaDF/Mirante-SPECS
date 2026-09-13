# Estado da Spec 0004

- Fase atual: concluida
- Última ação: validação interativa completa com o usuário (Playwright
  headed, janela aberta, ajustes passados ao vivo); build + 41 testes
  unitários + 6 e2e verdes; documentação (spec/plan/tasks/tests) atualizada
  com todos os pedidos da sessão.
- Próximo passo: commit + push + PR nos 3 repos.
- Bloqueios: nenhum
- Atualizado em: 2026-09-13

## Notas técnicas relevantes (bugs reais encontrados e corrigidos)
1. `LayoutComponent.lerBreadcrumb()` quebrava a navegação pós-login
   (`Cannot read properties of undefined (reading 'data')`) por optional
   chaining incompleto (`atual?.snapshot.data[...]` em vez de
   `atual?.snapshot?.data?.[...]`).
2. `LoteFacade.pesquisar()` atualizava filtro e página em dois
   `BehaviorSubject` separados, causando uma busca intermediária com
   filtro novo + página antiga — unificado num único `ConsultaState`.
3. Output `rangeChange` do `RangeFieldComponent` tinha originalmente o
   nome `change`, igual ao evento nativo do DOM que borbulha dos `<input>`
   internos — um segundo disparo com o `Event` cru sobrescrevia o valor
   certo no componente pai, quebrando os filtros de faixa por completo.
   Renomeado para `rangeChange`.
4. Campo de moeda (Valor Lote): digitar uma letra após o valor já
   mascarado deixava a letra "colada" no campo, porque o binding
   `[value]` não reescreve o DOM quando o texto sanitizado não muda
   (Angular pula o write por achar que não houve mudança). Corrigido
   escrevendo `input.value` diretamente no handler, além do signal.
5. CSS do `GenericTableComponent` parou de bater nas células de
   cabeçalho/linha depois que passaram a ser projetadas via
   `ngTemplateOutlet` a partir do template de quem usa (atributo de
   encapsulamento Angular diferente do host). Corrigido com
   `:host ::ng-deep`.
6. Grid de filtros com `grid-template-columns: 1fr` (puro) "estourava" a
   tela no mobile — `1fr` tem `min-width:auto` implícito no CSS Grid, e um
   `<select>`/input mais largo que a coluna força o grid a crescer além do
   container. Corrigido com `minmax(0, 1fr)`.
7. Sidebar só tinha `min-height:100vh` no elemento interno (`<aside
   class="sidebar">`); quando o conteúdo principal cresce além de uma
   tela (ex.: 10 linhas de tabela), o host (`<app-sidebar>`) estica via
   flex, mas o `<aside>` interno não acompanhava, cortando o menu.
   Corrigido com `height:100%` no `<aside>` (mais `min-height:100vh` como
   piso).
8. `LotesPageComponent` chamava `facade.pesquisar({})` no construtor,
   além da busca automática que já acontece ao assinar `lotes$`/`total$`
   (o `BehaviorSubject` de consulta reemite o valor padrão para quem
   assina) — causava 2 requisições `GET /api/lotes` em todo carregamento
   da tela. Removida a chamada redundante.

## Decisões tomadas durante a validação com o usuário
- Confirmar/Enviar/Visualizar Justificativa não exigem mais seleção prévia
  para ficarem habilitados; sem seleção, abrem um modal perguntando o
  escopo (página atual ou todos os lotes do filtro).
- Confirmar só afeta lotes com situação Aberto; Enviar só afeta
  Confirmados — filtrado dentro do conjunto escolhido pelo usuário
  (seleção manual ou escopo da modal).
- As três ações em massa (Confirmar/Enviar/Justificativa) fazem uma única
  chamada HTTP com a lista de ids, nunca uma por lote.
- `FilterPanelComponent` e a tabela de lotes foram generalizados
  (`GenericTableComponent`) para não conhecerem `Lote` — corpo/colunas
  projetados por quem usa.
