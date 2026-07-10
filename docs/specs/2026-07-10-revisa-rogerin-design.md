# Design — Rogerin: skill `revisa-rogerin` (review de pull request do GitHub)

**Data:** 2026-07-10
**Autor:** Estevão Andrade (estevao2012)
**Status:** Aprovado (brainstorming)

## Objetivo

Adicionar ao plugin `rogerin` uma skill que **revisa um pull request do GitHub** por
análise **estática do diff** e crava um veredito binário: **Approve** ou **Request
changes**. A skill **nunca edita código nem roda nada** — só lê o diff/contexto, comenta
e submete o review. Depois do OK do usuário, posta no GitHub um review em **inglês
profissional (work-safe)** com comentários **inline**; no terminal entrega um recap na
**voz do Rogerin** (PT-BR).

Como toda skill do plugin, a voz é tempero — os fatos e o veredito são sagrados
("personalidade ímpar, porém útil").

## Decisões (do brainstorming)

| Decisão | Escolha |
|---|---|
| Trigger | **Sob demanda** — invocação manual; sem hook/automação |
| Input | **PR do GitHub** — default o PR da branch atual; aceita número/URL opcional |
| Ferramenta | **`gh` CLI** (`gh pr view/diff/checks`, `gh api .../reviews`) |
| Execução | **Nunca executa** e **nunca edita/commita** — review estático, read-only |
| Dimensões | Corretude/bugs · Segurança · Design/manutenibilidade · Testes · Performance |
| Severidade | **High / Medium / Low(nit)** por achado |
| Gate | **Binário:** qualquer High ou Medium ⇒ Request changes; só Low/limpo ⇒ Approve |
| Voz no GitHub | **Inglês profissional, work-safe** (sem gíria/palavrão) |
| Voz no terminal | **Rogerin raiz** (PT-BR) |
| Confirmação | **Obrigatória** — mostra o rascunho e só posta após OK explícito |
| Audiência | **Dois blocos** — recap no terminal (Rogerin) e review pro GitHub (inglês) |
| Nome | `revisa-rogerin` |

## Arquitetura

Nova skill instrucional (Claude segue o `SKILL.md`; sem código executável), no padrão
das skills existentes do plugin.

```
rogerin/
├── skills/
│   ├── _shared/
│   │   └── rogerin-voice.md        # persona compartilhada (já existe)
│   ├── resume-rogerin/SKILL.md     # já existe
│   ├── avalia-rogerin/SKILL.md     # já existe
│   └── revisa-rogerin/SKILL.md     # NOVO
├── .claude-plugin/
│   ├── marketplace.json            # bump de versão
│   └── plugin.json                 # + keywords, bump de versão
├── docs/specs/
│   └── 2026-07-10-revisa-rogerin-design.md
└── README.md                       # lista a nova skill
```

## Componentes

### `skills/revisa-rogerin/SKILL.md`

**Frontmatter** — `name: revisa-rogerin`; `description` com os gatilhos (PT + EN).

**Trigger / when to use:**
- `/revisa-rogerin [pr]`
- "revisa esse PR", "revisa o pull request", "revisa Rogerin", "review this PR"

**Input:** PR do GitHub. Default = PR da branch atual; aceita número/URL opcional. `gh` CLI.

**Processo:**
1. Lê `${CLAUDE_PLUGIN_ROOT}/skills/_shared/rogerin-voice.md` (persona).
2. Resolve o PR (`gh pr view --json ...`) e o CI (`gh pr checks`).
3. Pega o diff (`gh pr diff`); diff grande → subagent Explore + cobertura honesta.
4. Lê o contexto do repo em volta das mudanças (só leitura).
5. Classifica achados por dimensão, com severidade e `path:line`.
6. Deriva o veredito pelo gate.
7. Monta o rascunho e **mostra pro usuário**.
8. Só após OK, submete via `gh api`.

**As 5 dimensões:** Corretude & bugs · Segurança · Design & manutenibilidade · Testes ·
Performance (só o que dá pra ver estaticamente).

**Severidade & gate:** High (corretude/segurança/breaking/crash), Medium (bug provável,
teste crítico ausente, problema real de design, regressão de perf), Low/nit (estilo,
naming, opcional). Gate binário: **High ou Medium ⇒ Request changes; só Low/limpo ⇒
Approve**.

**Postagem no GitHub:** JSON via `gh api "repos/{owner}/{repo}/pulls/<n>/reviews" --input`
com `event` (`REQUEST_CHANGES`/`APPROVE`/`COMMENT`), `body` e `comments[]` (path, line,
side). Inline só em linha do diff; fora do diff vai pro corpo. **Edge case:** GitHub
bloqueia aprovar/request-changes no próprio PR → cai para `event: COMMENT` com o veredito
explícito no corpo.

**Saída — dois blocos:**
- *Recap no terminal* (PT-BR, Rogerin raiz): abertura, PR (#/título/CI), veredito,
  achados por severidade com `path:line`, "vou postar X", sign-off.
- *Review pro GitHub* (inglês, work-safe): review body (resumo + veredito + razões) +
  inline comments (`path:line → comentário`, nit marcado como não-bloqueante).

### `.claude-plugin/plugin.json` e `marketplace.json`
- `plugin.json`: adiciona keywords (`pull-request`, `pr-review`, `revisa`, `github`) e
  bump de versão (`0.2.0` → `0.3.0`).
- `marketplace.json`: bump de versão e descrição para bater com o plugin.

### `README.md`
- Lista `revisa-rogerin` junto das demais skills, com 1 linha do que faz e o gatilho.

## Fluxo de uso

1. Estevão num repo, numa branch com PR aberto → `/revisa-rogerin` (ou "revisa esse PR",
   opcional número/URL).
2. Rogerin lê a persona, resolve o PR, pega o diff, revisa as 5 dimensões (sem rodar nada).
3. Mostra o **recap** (veredito + achados na voz do Rogerin) e o **rascunho do review**
   em inglês, e pergunta se pode postar.
4. Com o OK, submete o review no GitHub (Approve ou Request changes; ou Comment se for o
   próprio PR).

## Tratamento de erros / edge cases

- **Sem PR na branch e sem arg:** pede o número/URL.
- **Diff grande:** subagent Explore + declara o que não cobriu.
- **Tentação de executar/editar:** proibido; o que exigiria rodar vira "not verified
  (would require running it)".
- **Próprio PR:** GitHub bloqueia Approve/Request-changes → `COMMENT` com veredito no corpo.
- **Inline fora do diff:** a API rejeita → manda pro corpo do review.
- **Voz por canal:** GitHub work-safe (inglês); terminal Rogerin raiz.
- **Fato é sagrado:** `path:line` exatos; zero invenção.

## Verificação (definição de pronto)

- `skills/revisa-rogerin/SKILL.md` criado, frontmatter válido, gatilhos claros.
- `plugin.json` e `marketplace.json` com JSON válido e versões batendo.
- `README.md` lista a nova skill.
- Rodar a skill num PR de exemplo e confirmar: recap + rascunho saem, veredito respeita o
  gate, nada é postado sem OK, e **nenhum** comando de execução/edição foi disparado.

## Fora de escopo (YAGNI)

- Automação/hook que dispara sozinho ao chegar PR (hoje: sob demanda).
- Editar código ou aplicar fixes (a skill só comenta).
- Rodar código/testes/linter.
- Severidade em mais buckets ou score numérico.
- Providers além do GitHub (GitLab/Bitbucket).
