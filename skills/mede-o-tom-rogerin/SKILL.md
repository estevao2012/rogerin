---
name: mede-o-tom-rogerin
description: Use when the user wants to check whether a piece of text (a doc, a Slack message, an email, a comment) sounds like them before sending — "mede o tom", "isso soa como eu?", "tá genérico?", "tá encurtado demais?", "revisa o tom disso", "does this sound like me", "check the tone", "is this too generic", "score the tone". Static analysis only — never sends, never edits. Scores the text against Estevão's real voice profile across 4 dimensions (soa como você / genérico / claro-não-encurtado-demais / registro), flags the problem spans, and suggests a rewrite in his voice. Recap in Rogerin's voice (PT-BR).
---

# mede-o-tom-rogerin

Pega um texto (doc, mensagem de Slack, e-mail, comentário) e mede se ele **soa como o
Estevão** antes de sair. Aponta onde tá genérico, onde tá **encurtado demais** (o problema
principal: compressão/jargão que o próprio Estevão não entende ao ler), e devolve um
rewrite na voz dele. **Só análise — não envia, não edita nada.**

**Antes de medir, leia as duas fontes:**
- `${CLAUDE_PLUGIN_ROOT}/skills/_shared/estevao-voice.md` — **a régua** (o perfil de voz +
  as 4 dimensões). É o padrão contra o qual você mede. Sagrado.
- `${CLAUDE_PLUGIN_ROOT}/skills/_shared/rogerin-voice.md` — tom, dial de palavrão e
  guardrails do **recap**. A voz do Rogerin é tempero do terminal; **não** entra na
  medição nem no rewrite.

## When to use
- `/mede-o-tom-rogerin [texto]`
- "mede o tom", "isso soa como eu?", "tá genérico?", "tá encurtado demais?", "revisa o tom disso"
- "does this sound like me", "check the tone", "is this too generic", "score the tone"

## Input
O texto a medir (colado, ou um arquivo/rascunho referenciado). Se ajudar, o **destino**
(DM, canal de time, anúncio, doc) e o **idioma** pretendido — muda o registro esperado.
Não veio o destino → assuma pelo conteúdo e **diga a suposição**; não invente.

## Como medir (a régua)
Pontue as **4 dimensões** do `estevao-voice.md`, cada uma ✅ / ⚠️ / ❌, sempre citando o
**trecho** e o conserto:

1. **Soa como você?** — direto, 1ª pessoa, caloroso, específico; abertura/fecho no estilo
   dele; emoji no lugar de `!`. ❌ = impessoal/frio/"qualquer um escreveria".
2. **Genérico?** — concreto (nomeia arquivo/ticket/PR/pessoa/prazo). ❌ = buzzword, filler,
   hedge vago, frase de efeito vazia.
3. **Claro / encurtado demais?** ← **a que mais importa.** ✅ = curto **e** entendível de
   primeira. ❌ = compressão críptica, palavra dropada que quebra o sentido, sigla/jargão
   sem explicação. **Precisou reler pra entender? É ❌.**
4. **Registro certo?** — bate com o canal (DM casual vs 1:1 vs time vs anúncio) e está no
   idioma da audiência. ❌ = anúncio formatado numa DM, gíria num anúncio, formal demais
   num ping, ou idioma errado pra quem vai ler.

**Veredito:** qualquer ❌ (em especial a #3) → **não passa**, mostra o rewrite. Só ⚠️ →
**passa com ressalva**. Tudo ✅ → **passou**.

## Saída — dois blocos
Emite os dois, nesta ordem, com cabeçalho claro.

### Bloco 1 — RECAP NO TERMINAL (PT-BR, voz do Rogerin, dial raiz)
Papo reto pro usuário:
- **Abertura** — 1 linha, atitude do Rogerin.
- **Veredito** — passou / passa com ressalva / não passa, numa linha.
- **Placar** — as 4 dimensões com ✅/⚠️/❌ e 1 frase cada, citando o trecho.
- **Sign-off** — 1 linha marca registrada.

### Bloco 2 — O REWRITE (na voz do Estevão, limpo)
Só se deu ⚠️ ou ❌. O texto reescrito na voz do Estevão (direto, caloroso, específico,
curto-**mas**-claro), no registro certo pro destino, **limpo** (typo/caixa corrigidos — a
voz é o registro, não a preguiça de digitação). Passou 100%? Escreve "Tá na tua voz, pode
mandar" e pula o rewrite.

**Idioma do rewrite:** por padrão **mantém o idioma do texto original**. Se a audiência/
destino só fala inglês (canal internacional, leitor que não lê PT), reescreve **em
inglês**. Não sabe a audiência → mantém o idioma da origem e **diz a suposição**. Nunca
troca de idioma só por causa do registro (formal não vira EN por ser formal).

## Rules
- **Só mede e sugere — nunca envia, nunca edita arquivo.** A ação é do usuário.
- **A dimensão #3 (encurtado demais) é a prioridade** — é a dor do Estevão. Na dúvida entre
  "curto" e "claro", clareza ganha.
- **Cita o trecho.** Feedback sem apontar o pedaço específico é vago demais — não serve.
- **O rewrite sai limpo e na voz dele**, não na voz do Rogerin. Rogerin só no recap.
- **Idioma do rewrite = idioma da origem**, por padrão. Audiência só-inglês → reescreve em
  inglês. Sem saber a audiência → mantém a origem e diz a suposição. Registro (casual vs
  formal) é coisa separada de idioma.
- Faltou o destino → assume pelo conteúdo e **diz a suposição**.

## Common mistakes
- Reescrever tão curto que fica críptico — recai no problema que a skill existe pra pegar.
- Feedback vago sem citar o trecho → sempre aponta o pedaço.
- Deixar a voz do Rogerin vazar pro rewrite → o rewrite é a voz do Estevão, limpa.
- Trocar o idioma da origem à toa — só vira inglês se a audiência for só-inglês; senão mantém.
- "Consertar" a voz dele deixando corporativo/genérico — o alvo é ele, não um manual.
- Editar o arquivo ou mandar a mensagem — proibido; a skill só mede.
