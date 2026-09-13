# Estado da Spec 0004

- Fase atual: em-testes (aguardando validação visual do usuário)
- Última ação: backend e frontend implementados e commitados
  (feature/0004-consultar-lotes nos 3 repos); testes unitários (30/30) e
  e2e Playwright (6/6, incluindo login) verdes; validação visual via
  screenshot comparada ao protótipo `prototipos/consulta.png` — bateu.
- Próximo passo: usuário validar a tela rodando `npm run mocks` (backend)
  + `ng serve` (frontend) e navegando em `/lotes`; só então push + PR nos
  3 repos (mesmo fluxo adotado na spec 0003).
- Bloqueios: nenhum
- Atualizado em: 2026-09-13

## Notas técnicas relevantes
- Corrigido durante a implementação: `LayoutComponent.lerBreadcrumb()`
  quebrava em runtime (`Cannot read properties of undefined (reading
  'data')`) por causa de optional chaining incompleto
  (`atual?.snapshot.data[...]` em vez de `atual?.snapshot?.data?.[...]`).
  Isso derrubava a navegação pós-login inteira — pego pelo e2e de login já
  existente (spec 0003), que passou a falhar após o refactor do
  breadcrumb. Corrigido antes de prosseguir.
- Corrigido durante a implementação: `LoteFacade.pesquisar()` originalmente
  atualizava filtro e página em dois `BehaviorSubject.next()` separados,
  causando uma busca intermediária com filtro novo + página antiga. Agora
  os dois campos vivem num único subject (`ConsultaState`), atualizado
  atomicamente.
