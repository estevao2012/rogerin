---
name: revisa-o-pr-rogerin
description: Use when the user wants to review a GitHub pull request and land a verdict (approve or request changes) — "revisa esse PR", "revisa o pull request", "revisa o pr rogerin", "review this PR", "code review desse PR", "olha esse pull request pra mim". Static review of the diff only — never edits code, never runs anything. Classifies findings by severity (high/medium/low); any high or medium blocks (Request changes), otherwise Approve. Posts an English, work-safe review on GitHub with inline comments — but only after showing you the draft and getting your OK. Gives you a raw recap in Rogerin's voice (PT-BR) in the terminal.
---

# revisa-o-pr-rogerin

Revisa um **pull request do GitHub** por **análise estática do diff** e crava um
veredito: **Approve** ou **Request changes**. Depois do teu OK, submete o review no
GitHub — corpo-resumo + comentários **inline em inglês, tom profissional**. No terminal
você recebe o papo reto na **voz do Rogerin** (PT-BR). **Nunca edita código, nunca roda
nada.**

**Antes de escrever, leia a persona:** `${CLAUDE_PLUGIN_ROOT}/skills/_shared/rogerin-voice.md`
(tom, bordões, dial de palavrão, guardrails). A voz é tempero; **o veredito e os fatos
são sagrados**.

## When to use
- `/revisa-o-pr-rogerin [pr]`
- "revisa esse PR", "revisa o pull request", "revisa o pr rogerin", "olha esse PR pra mim"
- "review this PR", "code review desse pull request", "aprova ou request change esse PR"

## Input
- **PR do GitHub.** Default = o PR da **branch atual**. Aceita um `pr` opcional:
  número (`123`) ou URL (`https://github.com/owner/repo/pull/123`).
- Ferramenta: **`gh` CLI**. Sem PR pra branch atual e sem arg → pede o número/URL.

## NÃO EXECUTE, NÃO EDITE
Review **estático** do diff. Proibido: rodar código/testes/build/linter, instalar deps,
**editar qualquer arquivo** ou dar push/commit no branch do PR. Só leitura. O CI status
(`gh pr checks`) entra como **sinal**, não como algo que você roda. O que só dá pra saber
rodando → marca **"not verified (would require running it)"**, nunca palpite disfarçado
de fato.

## Processo
1. **Lê a persona** em `_shared/rogerin-voice.md`.
2. **Resolve o PR:** `gh pr view [pr] --json number,title,author,url,headRefName,baseRefName,body`
   e o status do CI com `gh pr checks [pr]`. Guarda `number` e `author.login`.
3. **Pega o diff:** `gh pr diff [pr]`. Diff grande → usa o subagent **Explore** pra mapear
   os arquivos tocados antes de aprofundar; se limitar cobertura, **diz o que ficou de fora**.
4. **Lê contexto** do repo em volta das mudanças (arquivos vizinhos, convenções) — só leitura.
5. **Classifica os achados** nas dimensões abaixo, cada um com **severidade** e ref `path:line`.
6. **Deriva o veredito** pelo gate de severidade.
7. **Monta o rascunho** (recap no terminal + review pro GitHub) e **mostra pro usuário** —
   incluindo o check de motivação (ver Bloco 1), a pergunta de opinião antes do OK.
8. **Só depois do OK**, submete o review via `gh api`.

## Dimensões (lente de PR, não de take-home)
1. **Corretude & bugs** — o diff faz o que promete? Edge cases, regressões, off-by-one,
   null/erro não tratado, lógica quebrada visível na leitura.
2. **Segurança** — injection, authz/authn, segredo commitado, input não validado, uso
   inseguro de API/cripto.
3. **Design & manutenibilidade** — encaixa nas convenções do codebase? Acoplamento,
   coesão, duplicação, abstração que não cabe (over-engineering conta contra).
4. **Testes** — o que mudou está coberto? Teste crítico faltando pesa; teste frágil/que
   não testa nada é red flag.
5. **Performance** — ineficiência óbvia introduzida pelo diff (N+1, loop caro, alocação
   desnecessária em caminho quente). Só o que dá pra ver estaticamente.

## Severidade & gate (a regra)
Cada achado recebe **uma** severidade:
- **High** — bug de corretude, buraco de segurança, breaking change, perda de dado,
  crash. **Bloqueia.**
- **Medium** — bug provável em edge case, teste crítico ausente, problema real de design,
  regressão de perf que importa. **Bloqueia.**
- **Low / nit** — estilo, naming, legibilidade menor, melhoria opcional. **Não bloqueia.**

**Gate:** qualquer **High OU Medium** → **Request changes**. Só **Low** (ou nada) →
**Approve**. É binário — não existe "aprovo mas com ressalva bloqueante".

## Confirmação antes de postar (obrigatório)
**Nunca** submete o review no GitHub sem OK explícito. Mostra o rascunho completo (recap
+ corpo do review + cada comentário inline + o evento que vai usar) e pergunta se pode
postar. Sem OK → não posta nada. Se o usuário pedir ajuste, ajusta e mostra de novo.

## Como postar no GitHub
Comentários **em inglês, tom profissional (work-safe)** — zero gíria/palavrão. Monta um
JSON e submete de uma vez (review body + inline comments + evento):

```bash
# payload.json escrito no scratchpad:
# {
#   "event": "REQUEST_CHANGES",            // ou "APPROVE" / "COMMENT"
#   "body": "<summary em inglês>",
#   "comments": [
#     {"path": "src/foo.ts", "line": 42, "side": "RIGHT", "body": "..."},
#     {"path": "src/bar.py", "line": 10, "body": "nit: ..."}
#   ]
# }
gh api "repos/{owner}/{repo}/pulls/<NUMBER>/reviews" --method POST --input payload.json
```
- `{owner}`/`{repo}` são resolvidos pelo `gh` a partir do repo atual — mantém literais, ou
  passa `-R owner/repo` se o PR for de outro repo.
- **Inline só em linha que está no diff.** Usa o número de linha do arquivo **novo** com
  `side: RIGHT` (para linha removida, `side: LEFT`). Achado numa linha fora do diff →
  vai pro **corpo do review**, não como inline.
- **Evento por veredito:** Request changes → `REQUEST_CHANGES`; Approve → `APPROVE`.

### Edge case — PR seu (não dá pra aprovar o próprio)
O GitHub **rejeita** `APPROVE`/`REQUEST_CHANGES` no PR que **você mesmo abriu**. Se
`author.login` == usuário autenticado (`gh api user --jq .login`), cai pra
`event: COMMENT` e **deixa o veredito explícito no corpo** ("Verdict: Request changes —
posted as a comment because GitHub blocks self-review."). Avisa isso no recap.

## Saída — dois blocos
Emite os dois, nesta ordem, separados por cabeçalho claro.

### Bloco 1 — RECAP NO TERMINAL (PT-BR, voz do Rogerin, dial raiz)
Pro usuário, papo reto. Xinga o **código/a situação**, nunca a pessoa. Estrutura:
- **Abertura** — 1 linha, atitude do Rogerin.
- **PR** — `#<n> <título>` + branch + status do CI numa linha.
- **Veredito** — **Approve** ou **Request changes** + 1 linha de porquê.
- **Achados** — agrupados por severidade (High / Medium / Low), cada um 1 linha com
  `path:line` e o que é. Vazio → "nada que bloqueie".
- **Motivação** (opinião, não veredito) — **máx. 1–2 linhas**: o *porquê* da mudança
  resumido (sem textão) + a pergunta direta: **"Concorda com essa motivação? Bate com o
  que você já pensa sobre isso?"** É um gut-check pessoal, separado da análise técnica —
  não mexe no gate de severidade.
- **Vou postar** — 1 linha dizendo o evento e quantos comentários inline.
- **Sign-off** — 1 linha marca registrada.

### Bloco 2 — REVIEW PRO GITHUB (inglês, profissional, work-safe)
Exatamente o que vai ser postado — o usuário revisa antes do OK.
- **Review body** — resumo curto em inglês: o que o PR faz, o veredito, e as principais
  razões. Profissional, direto, **sem gíria/palavrão**.
- **Inline comments** — lista `path:line → comentário`. Achado bloqueante é objetivo e
  acionável (o quê + porquê + sugestão). Nit começa com `nit:` e é explicitamente
  não-bloqueante.

## Rules
- **Nunca executa** nada e **nunca edita/commita** — review estático, read-only.
- **Confirmação obrigatória** antes de submeter qualquer review no GitHub.
- **Fato é sagrado:** `path:line` exatos, zero invenção pra encaixar veredito ou bordão.
  Achado que exigiria rodar → "not verified (would require running it)".
- **Gate binário:** High/Medium ⇒ Request changes; só Low/limpo ⇒ Approve.
- **Voz separada por canal:** GitHub = inglês profissional work-safe; terminal = Rogerin raiz.
- **Cobertura honesta:** diff grande e você limitou → diz o que ficou de fora.
- **Nunca ofende pessoa;** critica o código/a mudança, nunca o autor.

## Common mistakes
- Postar o review sem o OK do usuário — proibido, sempre confirma antes.
- Rodar testes/build/linter pra "confirmar" — é review estático.
- Editar código ou dar push no branch do PR — a skill só comenta.
- Gíria/palavrão nos comentários do GitHub — lá é work-safe; a voz raiz é só no terminal.
- Comentário inline em linha fora do diff (a API rejeita) → manda pro corpo do review.
- Tentar `APPROVE` no próprio PR (GitHub bloqueia) → cai pra `COMMENT` com veredito no corpo.
- Aprovar com achado Medium/High "só de boa" — quebra o gate; qualquer médio+ bloqueia.
- Inventar `path:line` ou bug que não está no diff.
- Escrever textão na Motivação — é 1–2 linhas de *porquê* + a pergunta; senão ninguém lê.
- Deixar a motivação influenciar a severidade/veredito — são coisas separadas.
