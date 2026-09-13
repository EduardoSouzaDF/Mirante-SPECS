# Spec 0004 — Consultar Lotes

- Status: em-planejamento
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
disparar ações sobre o(s) lote(s) selecionado(s).

## Escopo

### Dentro do escopo
- Tela "Outros Créditos/Débitos" com breadcrumb, painel de filtros
  recolhível, barra de ações e tabela paginada de lotes.
- Backend mockado: rotas de filtros disponíveis, listagem paginada de lotes
  e detalhe de um lote.
- Ações que alteram estado real no mock: Confirmar, Enviar, Excluir.
- Ações que só abrem o modal de detalhe (sem lógica de negócio nova nesta
  spec): Visualizar, Visualizar Justificativa, Incluir, Alterar — endpoints
  respondem 200 com corpo básico (placeholder), tratados a fundo em spec
  futura.
- Componentização reutilizável: breadcrumb (por `@Input`, não mais derivado
  da URL), campo de faixa "De/Até", painel de filtros, tabela de lotes.
- Estado da tela via RxJS (facade/service), sem uso de signals para os dados
  remotos (segue o padrão já usado no `AuthService`).

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
   "Pesquisar" dispara a busca.
3. Listar lotes em tabela paginada com colunas: checkbox, ID, Data Entrada,
   Valor, Quant. Lançamentos, Usuário Registro, Usuário Aprovação, Situação.
4. Selecionar um ou vários lotes via checkbox (linha e "selecionar todos" no
   cabeçalho, com estado indeterminado quando a seleção é parcial).
5. Exibir barra de ações acima da tabela: Confirmar, Enviar, Visualizar
   Justificativa, Incluir, Alterar, Excluir, Visualizar.
   - Confirmar, Enviar, Visualizar Justificativa, Excluir: habilitados com
     pelo menos 1 lote selecionado.
   - Incluir: sempre habilitado (não depende de seleção).
   - Alterar, Visualizar: habilitados apenas com exatamente 1 lote
     selecionado.
6. Abrir modal de detalhe do lote (Visualizar/Alterar) buscando
   `GET /api/lotes/:id`, mostrando todos os campos do lote.
7. Ações Confirmar/Enviar/Excluir chamam o respectivo endpoint e atualizam a
   listagem após sucesso.

## Requisitos não-funcionais
- Tipagem explícita para os modelos (`Lote`, `Instituicao`, `Usuario`,
  `SituacaoEnum`, `FiltroLote`, respostas paginadas).
- Componentes standalone (Angular 17), seguindo o estilo já usado no projeto
  (template/styles inline, tokens SCSS existentes, PrimeFlex para layout).
- Responsivo (mobile-first já usado no `layout.component.ts`/sidebar).
- Fallback local (dados em memória) se o mock-server estiver indisponível ao
  carregar os filtros, para não travar a tela.

## Critérios de aceite
- [ ] `GET /api/filtros/lotes` retorna instituições, instituições
      responsáveis e situações.
- [ ] `GET /api/lotes` aceita todos os filtros da tela + paginação e
      devolve `{ data, total, page, size, hasNext, hasPrevious }`.
- [ ] `GET /api/lotes/:id` devolve o lote completo (nomes resolvidos de
      responsável/usuários, quantidade de lançamentos, lançamentos).
- [ ] `POST /api/lotes/:id/confirmar` e `/enviar` retornam 200 e alteram a
      situação do lote no mock; `POST /api/lotes/:id/excluir` retorna 204 e
      remove o lote do mock.
- [ ] Parâmetro de filtro inválido (ex.: situação inexistente) retorna 400.
- [ ] Breadcrumb da tela via `[items]`: "Início > Outros Créditos/Débitos",
      com "Início" navegando para `/dashboard`.
- [ ] Botão "Pesquisar" com espaçamento visualmente distinto dos demais
      campos do painel de filtros.
- [ ] Barra de ações mostra ícone à esquerda de cada botão, fundo cinza, e
      segue as regras de habilitação do requisito funcional 5.
- [ ] Tabela replica as colunas e a paginação do protótipo
      (`prototipos/consulta.png`).
- [ ] Alterar e Visualizar só habilitados com exatamente 1 selecionado;
      Excluir/Confirmar/Enviar/Justificativa com 1+.

## Dependências
- Backend: `@mocks-server/main` já configurado (spec 0003).
- Frontend: `AuthService`/interceptors/loading já existentes (spec 0003).

## Riscos
- Estado de seleção pode ficar inconsistente entre páginas (seleção não
  persiste ao trocar de página, a menos que combinado com o usuário).
- Fallback in-memory pode divergir do mock-server em formato de dados se um
  dos dois for alterado sem atualizar o outro.
