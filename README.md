# SDD Toolkit — Spec-Driven Development

Kit de arquivos para trabalhar com **especificações numeradas**, separando
planejamento/tarefas por repositório (frontend e backend) e permitindo
retomar qualquer demanda de onde parou.

## Estrutura

```
sdd-toolkit/
├── README.md
├── config/
│   ├── project.yaml        # identifica os repos (frontend/backend) e a convenção de branch
│   └── repos.env           # caminhos reais dos repos, lido pelo new_spec.sh para criar branches
├── skills/
│   └── sdd-workflow/
│       ├── SKILL.md        # skill que ensina o Claude a operar esse fluxo
│       └── references/
│           ├── spec-format.md
│           └── mocks-server.md
├── specs/
│   ├── INDEX.md            # índice sequencial de todas as specs
│   ├── STATE.md            # estado global — o que está ativo agora
│   └── 0001-exemplo/       # spec de exemplo, use como modelo/template
│       ├── spec.md
│       ├── plan.md
│       ├── tasks.md
│       ├── tests.md
│       └── state.md
└── scripts/
    └── new_spec.sh         # cria uma nova spec numerada automaticamente
```

## Como usar

1. **Configure os repositórios**:
   - `config/project.yaml` — descrição dos repos e convenção de branch
     (documentação lida pela skill).
   - `config/repos.env` — caminhos locais reais do frontend e do backend,
     mais `BASE_BRANCH` e `BRANCH_PREFIX` (usados de fato pelo script).
2. **Instale a skill**: copie a pasta `skills/sdd-workflow` para onde o
   Claude carrega skills do seu projeto (ex.: `.claude/skills/` ou o local
   equivalente do seu setup). A partir daí, ao pedir para "criar uma spec",
   "planejar tal demanda" ou "retomar a spec 0003", o Claude segue o fluxo
   descrito em `skills/sdd-workflow/SKILL.md`.
3. **Crie a primeira spec real** rodando:
   ```
   ./scripts/new_spec.sh "Título da demanda"
   ```
   Isso cria a pasta numerada da spec **e** a branch
   `feature/NNNN-titulo-da-demanda` em cada repo configurado em
   `config/repos.env`, a partir da branch base (`main` por padrão). Use
   `--no-branch` para pular a parte de git.
4. **Sempre que parar no meio de uma demanda**, atualize `state.md` da spec
   e `specs/STATE.md` (estado global) — é isso que permite retomar depois
   sem perder contexto.
5. **Ao concluir uma spec**: primeiro valide as telas com o **Playwright**
   (mock + `ng serve` + `npm run e2e:open` no frontend — o usuário confere em
   desktop e mobile e repassa as correções; **o PR só sobe após essa
   validação**). Depois, faça **commits** e **push** da branch em cada repo
   com alterações (frontend, backend e specs) e abra um **PR para a `main`**
   (via `gh pr create` quando disponível). Repos sem alterações não precisam
   de PR. Veja `skills/sdd-workflow/SKILL.md` (fase 5) para o passo a passo.

## Convenção de numeração

- Specs são pastas `NNNN-slug-curto`, ex.: `0001-login-sso`, `0002-tela-relatorios`.
- Números não se repetem nem são reaproveitados, mesmo que uma spec seja
  cancelada (marque como `cancelada` no índice, não apague o número).
- `specs/INDEX.md` é a fonte da verdade da numeração — sempre confira o
  último número usado ali antes de criar uma nova spec.
