---
name: posta-no-slack-rogerin
description: Use when the user wants to write or send a Slack message, post, or announcement — "posta no Slack", "escreve um post pro Slack", "manda uma mensagem no Slack", "avisa o time no Slack", "comunicado no Slack", "write a Slack post", "draft a Slack message", "post this to Slack", "announce this on Slack". Writes the post in professional English, hard-capped at 20 words, using Slack's mrkdwn conventions. Shows you the draft and only sends after your explicit OK (via the connected Slack tools, or clean copy-paste text if Slack isn't connected). Gives you the recap in Rogerin's voice (PT-BR) in the terminal.
---

# posta-no-slack-rogerin

Redige uma mensagem de Slack em **inglês profissional** — **no máximo 20 palavras** — no
formato certo do Slack (mrkdwn). Mostra o
rascunho e **só envia depois do teu OK**. No terminal você recebe o papo reto na **voz do
Rogerin** (PT-BR). A voz é tempero; **o texto que vai pro Slack é profissional, sempre**.

**Antes de escrever, leia a persona:** `${CLAUDE_PLUGIN_ROOT}/skills/_shared/rogerin-voice.md`
(tom, bordões, dial de palavrão, guardrails). A voz **não entra no post** — vive só no
recap do terminal.

## When to use
- `/posta-no-slack-rogerin [assunto]`
- "posta no Slack", "escreve um post pro Slack", "manda uma mensagem no Slack",
  "avisa o time no Slack", "faz um comunicado no Slack"
- "write a Slack post", "draft a Slack message", "post this to Slack", "announce this on Slack"

## Input
O que você quer comunicar (a intenção) e, se souber, o **canal/pessoa** de destino e se é
**FYI** ou tem **pedido/ação**. Faltou o essencial pra deixar a intenção clara (o quê, pra
quem, tem ação?) → **pergunta antes de escrever**, não inventa.

## Anatomia de um bom post (a receita)
**Limite duro: 20 palavras.** Não é sugestão — é teto. Conta as palavras antes de mostrar;
passou de 20, corta até caber. Se não cabe em 20, primeiro tenta jogar o resto na **thread
reply**.

**Se ainda assim não couber, pergunta antes de estourar o teto** — mostra o rascunho no
limite, diz o que ficou de fora e quantas palavras precisaria, e espera o OK. **Nunca**
estoura as 20 palavras por conta própria.

Ordem, cortando tudo que não servir:

1. **O ponto / o pedido primeiro** — a mensagem começa pelo que é e, se tem ação, qual é.
2. **O específico que falta** — só o dado sem o qual o leitor não age (o quê, quem, quando).
3. **Link ou @menção** — linka o PR/doc/ticket; @menciona só quem precisa agir.

Contexto, background e justificativa **não entram nas 20 palavras** — vão pra thread se
alguém pedir. Sem ação? Fecha com `*FYI*`.

**Tom:** profissional, cordial e direto — sem gíria, sem jargão, sem emoji em excesso.
**Tamanho:** 1–2 linhas, um assunto só. Link e `*FYI*` contam como palavra.

## Formato do Slack (mrkdwn — não é Markdown puro)
| Quer | Escreve |
|---|---|
| Negrito | `*bold*` (um asterisco só — **não** `**`) |
| Itálico | `_italic_` |
| Código | `` `code` `` · bloco com ``` ``` ``` |
| Citação | `> quote` |
| Bullet | `• item` (ou `- item`) |
| Link com label | `<https://url\|texto do link>` |
| Menção / canal | `@user` · `#channel` |

Emoji `:like_this:` com parcimônia. Evita `@here`/`@channel` a não ser que seja mesmo
urgente pra todo mundo.

## Confirmação antes de enviar (obrigatório)
**Nunca** envia sem OK explícito. Enviar é ação pública e difícil de desfazer. Mostra o
rascunho final + o destino (canal/pessoa, e se é thread) e pergunta se pode mandar. Sem
OK → não envia. Pediu ajuste → ajusta e mostra de novo.

## Como enviar
Depois do OK, usa as ferramentas de Slack conectadas (MCP):
1. **Resolve o destino** — canal por nome com `slack_search_channels`; pega o `channel` id.
   Thread → guarda o `thread_ts` da mensagem raiz.
2. **Envia** com `slack_send_message` (`channel`, `text`, `thread_ts` opcional). Agendar →
   `slack_schedule_message`. Quer revisar dentro do Slack antes → `slack_send_message_draft`.
3. **Slack não conectado** → entrega o texto **pronto pra copiar e colar** (mrkdwn), e diz
   o canal sugerido. Nunca finge que enviou.

## Saída — dois blocos
Emite os dois, nesta ordem, com cabeçalho claro.

### Bloco 1 — RECAP NO TERMINAL (PT-BR, voz do Rogerin, dial raiz)
Papo reto pro usuário. Estrutura:
- **Abertura** — 1 linha, atitude do Rogerin.
- **Destino** — canal/pessoa + FYI ou tem-ação, numa linha.
- **Resumo** — 1 linha do que a mensagem comunica.
- **Vou mandar** — "envio via Slack" ou "te devolvo pra copiar" + se é thread/agendado.
- **Sign-off** — 1 linha marca registrada.

### Bloco 2 — O POST PRO SLACK (inglês, profissional, mrkdwn, ≤20 palavras)
Exatamente o que vai ser enviado — o usuário revisa antes do OK. Já no formato do Slack,
seguindo a receita acima. Zero gíria/palavrão/voz do Rogerin aqui. Fecha com a contagem de
palavras entre parênteses (ex.: `(17 words)`) pro usuário conferir o teto.

## Rules
- **O post é inglês profissional, sempre.** A voz do Rogerin fica **só** no recap do terminal.
- **Confirmação obrigatória** antes de enviar qualquer coisa pro Slack.
- **Máximo 20 palavras, ponto primeiro, um assunto.** Conta as palavras; detalhe extra vai
  pra thread, não incha a mensagem.
- **Precisa de mais que 20 palavras → pergunta primeiro.** Nunca estoura o teto sem OK.
- **Fato é sagrado:** links, nomes, datas, @menções exatos — zero invenção pra encher.
- **mrkdwn, não Markdown:** `*bold*` com um asterisco; link é `<url|texto>`.
- **Faltou contexto** pra deixar a intenção clara → pergunta antes de escrever.
- Slack não conectado → devolve texto pra copiar; **nunca** finge envio.

## Common mistakes
- Enviar sem o OK do usuário — proibido, sempre confirma antes.
- Deixar a voz do Rogerin vazar pro post — o Slack é profissional; a voz é só no terminal.
- Passar de 20 palavras por conta própria — corta, ou pergunta antes de estourar o teto.
- Parede de texto — enterra o ponto; lidera com o pedido e joga contexto pra thread.
- `**negrito**` estilo Markdown — no Slack é `*negrito*` com um asterisco só.
- `@here`/`@channel` sem necessidade — só quando é urgente pra todo mundo mesmo.
- Escrever sem saber o canal ou se tem ação — pergunta o essencial primeiro.
- Inventar link, nome ou @menção pra preencher — fato é sagrado.
