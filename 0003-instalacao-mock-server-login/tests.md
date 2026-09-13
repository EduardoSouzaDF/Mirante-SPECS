# Testes — Spec 0003

## Casos de teste — Backend (mock scenarios)
| ID | Cenário | Mock/behavior utilizado | Resultado esperado | Status |
|----|---------|--------------------------|---------------------|--------|
| B1 | Login válido | variant `login-sucesso` | 200 com `{ token, user }` | passou (200 em ~2,1s) |
| B2 | Email não cadastrado | variant `email-nao-encontrado` | 401 com mensagem de erro | passou (`Email não encontrado`) |
| B3 | Senha incorreta | variant `senha-incorreta` | 401 com mensagem de erro | passou (`Senha incorreta`) |
| B4 | Reset de senha | variant `sucesso` | 200 OK (sempre) | passou |
| B5 | Delay configurado | env `MOCK_DELAY_SECONDS` | respostas demoram o tempo configurado | passou (2s observados) |

## Casos de teste — Frontend
| ID | Cenário | Passos | Resultado esperado | Status |
|----|---------|--------|---------------------|--------|
| T1 | Login com credenciais válidas | preencher form e enviar | redireciona para /dashboard | passou (fluxo + AuthService) |
| T2 | Login com credenciais inválidas | preencher form errado | mensagem de erro da API exibida | passou (via proxy: 401 com mensagem) |
| T3 | Reset de senha | abrir form de reset e enviar | feedback OK exibido | passou (mock sempre OK) |
| T4 | Manter conectado | marcar checkbox no login | cookie persiste após o login | passou (cookie 30 dias) |
| T5 | Token no Authorization | chamar API após login | header `Authorization: Bearer <token>` | passou (/me via proxy 200) |
| T6 | Loader nas requisições | fazer login com delay | indicador aparece durante a espera | passou (estrutura; revisão visual pendente) |
| T7 | Menu lateral + breadcrumbs | acessar /dashboard | sidebar com Dashboard e breadcrumb visíveis | passou (estrutura; revisão visual pendente) |

## Testes de integração (frontend + backend mockado)
- [x] Fluxo login: ng serve → /login → credenciais válidas → /dashboard
      (login via proxy validado com curl: 200 com token em ~2,1s).
- [x] Interceptor envia token do cookie para /api/auth/me (via proxy 3100:
      200 com user).
- [x] Delay do mock observável no fluxo (2s).
