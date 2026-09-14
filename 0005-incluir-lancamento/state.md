# Estado da Spec 0005

- Fase atual: em-testes (frontend implementado, aguardando validação
  visual interativa do usuário)
- Última ação: fechamento da modal + aviso de sucesso implementados e
  commitados. Backend: `usuarioRegistro` do lote criado por
  `POST /api/lancamentos` agora é o usuário autenticado no token
  (`src/auth-token.js`, extraído do `mocks/routes/auth.js`), não mais um
  placeholder do seed — corrigida também uma colisão de ids entre a lista
  de usuários autenticáveis e a lista interna de placeholders de lote.
  Frontend: `IncluirLancamentoDialogComponent` agora fecha a modal
  (`closed`) e emite `incluido` ao concluir com sucesso, em vez de manter
  a modal aberta para uma sessão de múltiplos lançamentos — removida a
  grade "lançamentos incluídos nesta sessão" e seus botões placeholder.
  Criado `AvisoToastComponent` (`shared/aviso-toast/`), aviso não-modal
  reutilizável (título/mensagem/tipo/tempo) com barra de progresso que
  fecha sozinho; `LotesPageComponent` o exibe quando a modal emite
  `incluido`. Build + 60 testes unitários verdes. Validação via Playwright
  confirmou ao vivo: `usuarioRegistro` correto no lote criado, modal
  fecha, toast aparece com o texto certo, a barra de progresso encolhe e
  o aviso some sozinho depois de ~3s.
- Próximo passo: usuário validar visualmente (janela Playwright headed
  aberta) e passar ajustes finais.
- Bloqueios: nenhum
- Atualizado em: 2026-09-13
