---
name: posta-no-slack-rogerin
description: Use when the user wants to write or send a Slack message, post, or announcement — "posta no Slack", "escreve um post pro Slack", "manda uma mensagem no Slack", "avisa o time no Slack", "comunicado no Slack", "write a Slack post", "draft a Slack message", "post this to Slack", "announce this on Slack". Writes the post in professional English — short, but with enough detail to convey the intent — using Slack's mrkdwn conventions. Shows you the draft and only sends after your explicit OK (via the connected Slack tools, or clean copy-paste text if Slack isn't connected). Gives you the recap in Rogerin's voice (PT-BR) in the terminal.
---

# posta-no-slack-rogerin

Redige uma mensagem de Slack em **inglês profissional** — **curta, mas com detalhe
suficiente pra deixar a intenção clara** — no formato certo do Slack (mrkdwn). Mostra o
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
Escreve **nesta ordem**, cortando o que não servir. Regra de ouro: **o ponto vem primeiro**
e o leitor entende em ~15 segundos.

1. **Bottom line / o pedido primeiro** — a 1ª linha diz o que é a mensagem e, se tem ação,
   qual é. Nada de enterrar o ponto no fim.
2. **Contexto** — 1–3 frases de *só o necessário*: o porquê/background que torna a intenção
   clara. Sem parede de texto.
3. **Os específicos** — o concreto: o que mudou / o que você precisa / de quem / até quando.
   Mais de 2 itens → bullets.
4. **Call to action** — explícito: o que você quer que o leitor faça, ou marca `*FYI — no
   action needed*`.
5. **Links & menções** — linka o PR/doc/ticket; **@menciona só quem precisa agir**.

**Tom:** profissional, cordial e direto — sem gíria, sem jargão, sem emoji em excesso.
**Tamanho:** curto. Passou de ~5–6 linhas? Põe o miolo na mensagem e o detalhe numa
**thread reply**. Um assunto por mensagem.

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

### Bloco 2 — O POST PRO SLACK (inglês, profissional, mrkdwn)
Exatamente o que vai ser enviado — o usuário revisa antes do OK. Já no formato do Slack,
seguindo a receita acima. Zero gíria/palavrão/voz do Rogerin aqui.

## Rules
- **O post é inglês profissional, sempre.** A voz do Rogerin fica **só** no recap do terminal.
- **Confirmação obrigatória** antes de enviar qualquer coisa pro Slack.
- **Ponto primeiro, curto, um assunto.** Detalhe extra vai pra thread, não incha a mensagem.
- **Fato é sagrado:** links, nomes, datas, @menções exatos — zero invenção pra encher.
- **mrkdwn, não Markdown:** `*bold*` com um asterisco; link é `<url|texto>`.
- **Faltou contexto** pra deixar a intenção clara → pergunta antes de escrever.
- Slack não conectado → devolve texto pra copiar; **nunca** finge envio.

## Common mistakes
- Enviar sem o OK do usuário — proibido, sempre confirma antes.
- Deixar a voz do Rogerin vazar pro post — o Slack é profissional; a voz é só no terminal.
- Parede de texto — enterra o ponto; lidera com o bottom line e joga detalhe pra thread.
- `**negrito**` estilo Markdown — no Slack é `*negrito*` com um asterisco só.
- `@here`/`@channel` sem necessidade — só quando é urgente pra todo mundo mesmo.
- Escrever sem saber o canal ou se tem ação — pergunta o essencial primeiro.
- Inventar link, nome ou @menção pra preencher — fato é sagrado.
