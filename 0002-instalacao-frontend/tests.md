# Testes — Spec 0002

## Casos de teste — Frontend
| ID | Cenário | Passos | Resultado esperado | Status |
|----|---------|--------|---------------------|--------|
| T1 | AppComponent renderiza | Rodar `ng test` | Componente é criado e o DOM contém o título/estrutura inicial | pendente |
| T2 | Pipe currency formata em pt-BR | Testar o pipe com `1234.5` | Retorna `R$ 1.234,50` | pendente |
| T3 | Pipe date formata em pt-BR | Testar o pipe com `2026-09-13` | Retorna `13/09/2026` | pendente |
| T4 | Tema verde-petróleo aplicado | Abrir `ng serve` | Botões/inputs usam a paleta (--petroleo-700, --agua-500) | pendente |
| T5 | Build de produção | Rodar `ng build` | Build conclui sem erros | pendente |

## Casos de teste — Backend (mock scenarios)
| ID | Cenário | Mock/behavior utilizado | Resultado esperado | Status |
|----|---------|--------------------------|---------------------|--------|
| T1 | Proxy /api aponta ao mock server | (backend ainda vazio nesta spec) | Request a `/api/**` chega a `localhost:3100` (404 do mock até configurar rotas) | pendente |

## Testes de integração (frontend + backend mockado)
- [ ] Com o mock server rodando na 3100, `ng serve` deve repassar as chamadas
      `/api/**` sem CORS e sem rewrite (proxy dev).
- [ ] O proxy deve ser validado de fato quando o backend tiver a primeira rota
      (spec futura); nesta spec, basta o `proxy.conf.json` existir e estar
      registrado no `angular.json`.
