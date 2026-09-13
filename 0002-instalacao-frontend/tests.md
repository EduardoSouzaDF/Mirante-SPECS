# Testes — Spec 0002

## Casos de teste — Frontend
| ID | Cenário | Passos | Resultado esperado | Status |
|----|---------|--------|---------------------|--------|
| T1 | AppComponent renderiza | Rodar `ng test` | Componente é criado e o DOM contém o título/estrutura inicial | passou |
| T2 | Pipe currency formata em pt-BR | Testar o pipe com `1234.5` | Retorna `R$ 1.234,50` | passou |
| T3 | Pipe date formata em pt-BR | Testar o pipe com `2026-09-13` | Retorna `13/09/2026` | passou |
| T4 | Tema verde-petróleo aplicado | Abrir `ng serve` | Botões/inputs usam a paleta (--petroleo-700, --agua-500) | passou (build inclui tokens; revisão visual pendente) |
| T5 | Build de produção | Rodar `ng build` | Build conclui sem erros | passou |

## Casos de teste — Backend (mock scenarios)
| ID | Cenário | Mock/behavior utilizado | Resultado esperado | Status |
|----|---------|--------------------------|---------------------|--------|
| T1 | Proxy /api aponta ao mock server | (backend ainda vazio nesta spec) | Request a `/api/**` chega a `localhost:3100` (404 do mock até configurar rotas) | configurado (proxy.conf.json registrado no angular.json) |

## Testes de integração (frontend + backend mockado)
- [x] `proxy.conf.json` criado (`/api` → `http://localhost:3100`) e registrado
      no `angular.json` (serve → proxyConfig).
- [ ] Validação de chamada real via proxy — fica pendente para a spec futura
      que configurar a primeira rota no backend mock.
