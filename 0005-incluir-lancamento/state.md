# Estado da Spec 0005

- Fase atual: implementacao (backend concluído)
- Última ação: backend implementado e commitado — entidades ContaCorrente/
  Lancamento/Instituicao (compartilhada), Lote sem valor/quantidadeLancamentos
  próprios (sempre calculado), rotas GET /api/contas-correntes e
  POST /api/lancamentos; validado com script Node (curl+bash mangla
  acentuação UTF-8 no corpo JSON — usar Node para testes manuais com texto
  acentuado).
- Próximo passo: implementar frontend (modelos, funções de cálculo,
  ajuste da tabela de consulta, IncluirLancamentoDialogComponent).
- Bloqueios: nenhum
- Atualizado em: 2026-09-13

## Notas técnicas
- `curl -d '{"historico":"Lançamento Manual"}'` via heredoc no Git Bash do
  Windows corrompe caracteres acentuados (virava "Histórico inválido"
  mesmo enviando o valor certo) — não é bug do backend. Testes manuais
  com acentuação devem usar um script Node (http.request com
  Content-Type charset=utf-8) em vez de curl direto no bash.
