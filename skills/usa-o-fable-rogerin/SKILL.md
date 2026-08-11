---
name: usa-o-fable-rogerin
description: Use when the user wants a second pair of eyes on the work done in this session — "usa o fable", "revisa com fable", "manda o fable revisar", "pede review pro fable", "fable review", "review this with fable", "second opinion on this session's work". Dispatches a MANDATORY subagent running the Fable model to statically review the session's work (the pull request, or the code changes made in this session) and report the problems it finds. Read-only: the subagent never edits, never runs anything, never commits. Findings come back classified by severity (high/medium/low) with `path:line`, and nothing gets fixed without your explicit OK. Recap in Rogerin's voice (PT-BR).
---

# usa-o-fable-rogerin

Chama um **revisor de fora** pro trabalho desta sessão. O revisor é um **subagent rodando
o modelo Fable** — contexto limpo, sem apego ao código que acabou de ser escrito. Ele
**aponta os problemas**; quem decide o que fazer é você.

**Antes de escrever, leia a persona:** `${CLAUDE_PLUGIN_ROOT}/skills/_shared/rogerin-voice.md`
(tom, bordões, dial de palavrão, guardrails). A voz é tempero; **os achados são sagrados**.

## When to use
- `/usa-o-fable-rogerin [alvo]`
- "usa o fable", "revisa com fable", "manda o fable revisar", "pede um review pro fable"
- "fable review", "review this with fable", "segunda opinião nesse trabalho"

## A regra que não se negocia: TEM que ser subagent

A revisão **roda num subagent**, nunca no agente principal. O motivo é o ponto da skill:
quem escreveu o código é o pior juiz dele. Um subagent com contexto limpo lê o diff como um
revisor de verdade leria.

Despacha com a ferramenta de subagent do ambiente, **explicitamente no modelo Fable**:

- ferramenta: `Agent`
- `model: "fable"` — obrigatório. Sem isso não é essa skill.
- `subagent_type: "general-purpose"` (ou o equivalente de propósito geral do ambiente)
- `run_in_background: false` — você precisa do resultado nesta volta
- `description`: `"Fable review da sessão"`

Se o ambiente não expuser subagent ou não aceitar o modelo Fable → **não improvisa
revisando você mesmo**. Diz isso na cara dura, em 1 linha, e pergunta se ele quer o review
no modelo disponível mesmo assim.

## Alvo da revisão (nessa ordem)
1. **Alvo explícito** passado pelo usuário (PR, branch, arquivo, path).
2. **PR da branch atual**, se existir (`gh pr view --json number,title,url,headRefName,baseRefName,body`).
3. **O trabalho desta sessão**: mudanças não commitadas (`git diff`, `git diff --staged`,
   arquivos novos não rastreados) **+** os commits feitos na branch atual em cima da base
   (`git log --oneline <base>..HEAD`, `git diff <base>...HEAD`).

Não achou nada em nenhum dos três → diz "não tem trabalho pra revisar nesta sessão" e para.
Não inventa alvo.

## NÃO EXECUTE, NÃO EDITE
Revisão **estática**. Nem o subagent nem você: rodar código/teste/build/linter, instalar
dep, **editar arquivo**, commitar, dar push, postar em lugar nenhum. Só leitura. O que só dá
pra saber rodando → **"not verified (would require running it)"**, nunca palpite disfarçado
de fato.

## Processo
1. **Lê a persona** em `_shared/rogerin-voice.md`.
2. **Resolve o alvo** pela ordem acima e monta o contexto: o diff, os paths tocados e (se
   houver) título/descrição do PR.
3. **Junta a intenção**: o que essa sessão *queria* resolver — do pedido do usuário, da
   descrição do PR e de qualquer referência de ticket/link que apareça ali. Se o ambiente
   tiver uma ferramenta de tracking de tickets conectada, usa ela (a skill é **agnóstica a
   qual sistema é esse**, nunca assume nem nomeia um). Sem intenção clara → segue e marca
   como "intenção não declarada".
4. **Despacha o subagent no Fable** com o prompt do bloco abaixo. Um subagent só, síncrono.
5. **Recebe os achados** e confere a sanidade de cada um contra o diff: achado que aponta
   pra linha que não existe ou pra código que não mudou **cai fora** (e você diz que caiu).
6. **Classifica por severidade** (High/Medium/Low) — a do subagent vale, você só corrige o
   que estiver claramente fora da régua.
7. **Entrega o recap** na voz do Rogerin (formato abaixo).
8. **Não corrige nada** por conta própria. Pergunta no fim se ele quer que você conserte, e
   o quê.

## O prompt do subagent

Passa pro subagent, em inglês, um briefing que contenha:

- **Papel**: senior engineer doing a static code review; the code was just written by
  another agent, so be skeptical, not polite.
- **Alvo**: o diff / os paths / o PR resolvidos no passo 2 (inclui o comando `git` que ele
  pode rodar pra ler o diff — leitura é permitida).
- **Intenção**: o que o trabalho deveria resolver (passo 3), e a pergunta explícita **"does
  this change actually do that?"**.
- **Proibições**: read-only — no edits, no commits, no running code/tests/build/linter, no
  installing anything, no posting anywhere.
- **Dimensões** a cobrir (as 5 abaixo).
- **Formato de saída**: uma lista de achados, cada um com `path:line`, `severity`
  (high/medium/low), 1 frase do **problema** e 1 frase do **por que importa** (o cenário de
  falha concreto: entrada/estado → resultado errado). Mais um veredito de 1 linha e, no
  máximo, 3 pontos de "what I could not verify without running it". **Nada de elogio, nada
  de resumo do diff** — só o que está errado ou arriscado.
- **Regra anti-invenção**: se não tem achado, devolve lista vazia. Encher linguiça com nit
  inventado é falha da revisão.

## Dimensões (o que o revisor cobre)
1. **Corretude & bugs** — o diff faz o que promete? Edge case, regressão, off-by-one,
   null/erro não tratado, lógica quebrada visível na leitura.
2. **Segurança** — injection, authz/authn, segredo commitado, input não validado, uso
   inseguro de API/cripto.
3. **Design & manutenibilidade** — encaixa nas convenções do codebase? Acoplamento,
   coesão, duplicação, abstração que não cabe (over-engineering conta contra).
4. **Testes** — o que mudou está coberto? Teste crítico faltando pesa; teste frágil ou que
   não testa nada é red flag.
5. **Escopo** — o diff faz coisa que ninguém pediu? Mudança órfã, arquivo tocado sem
   motivo, TODO/debug esquecido.

## Severidade
- **High** — bug de corretude, buraco de segurança, breaking change, perda de dado, crash.
- **Medium** — bug provável em edge case, teste crítico ausente, problema real de design,
  escopo vazando de forma que importa.
- **Low / nit** — estilo, naming, legibilidade menor, melhoria opcional.

Aqui **não tem gate de veredito** — essa skill é a segunda opinião, não o portão. Quem
aprova PR é a `revisa-o-pr-rogerin`.

## Output (recap no terminal, voz do Rogerin, PT-BR)

**Abertura** — 1 linha: o que o Fable olhou (alvo + tamanho do diff).

**O que o Fable achou**
- 1 bullet por achado, ordenado por severidade: `[HIGH] path:line — problema, e o que
  quebra.` Sem jargão, sem despejar o texto cru do subagent.
- Nada encontrado → `- (nada. o diff passou limpo.)`

**Não deu pra verificar**
- Até 3 bullets do que exigiria rodar. Vazio → omite a seção.

**Sign-off** — 1 linha na marca do Rogerin + **a pergunta**: quer que eu conserte quais?

## Rules
- Subagent **obrigatório** e **no modelo Fable**. Sem exceção silenciosa.
- Zero edição, zero execução, zero post. Nem você, nem o subagent.
- Achado sem `path:line` não entra no recap — ou você localiza, ou descarta.
- Não amacia achado High pra sessão parecer boa. O código é do agente, não seu ego.
- Não corrige nada antes do OK explícito, e corrige **só** o que ele mandar.
- Sem "ótima pergunta", sem repetir estas instruções, sem sermão no final.

## Common mistakes
- Revisar no agente principal "porque é mais rápido" → mata o propósito da skill.
- Esquecer `model: "fable"` → é outra skill, não essa.
- Sair consertando os achados sem perguntar → o pedido era **apontar**.
- Repassar o texto cru do subagent → traduz pra 1 linha por achado.
- Inventar nit pra não voltar de mãos vazias → diff limpo é resultado válido.
- Elogiar o diff → o recap é sobre problema; elogio não é entregável aqui.
