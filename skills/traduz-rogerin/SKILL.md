---
name: traduz-rogerin
description: Use when the user pastes text they want turned into plain Portuguese they actually understand — "traduz isso", "traduz rogerin", "o que isso quer dizer", "me explica em português", "simplifica esse texto", "não entendi, traduz", "translate this", "put this in simple portuguese", "what does this say". Handles two directions, auto-detected: English → simplified Brazilian Portuguese, or dense/complex Portuguese → simple Portuguese. Output is clean, clear PT-BR in Estevão's own voice (direct, warm, short-but-clear); keeps technical terms he already uses in English (PR, deploy, endpoint...). Comprehension is the goal. Static — never sends or edits anything.
---

# traduz-rogerin

Cola um texto — em inglês, ou em português complicado — e recebe de volta **PT-BR limpo e
claro, na voz do Estevão**, feito pra ele **entender de primeira**. Detecta a direção
sozinha. **Não envia, não edita nada** — só traduz.

**Antes de traduzir, leia:**
- `${CLAUDE_PLUGIN_ROOT}/skills/_shared/estevao-voice.md` — **a voz alvo** da tradução
  (direto, caloroso, curto-**mas**-claro) e a regra de ouro: clareza ganha do encurtamento.
- `${CLAUDE_PLUGIN_ROOT}/skills/_shared/rogerin-voice.md` — tom do **recap** do terminal. A
  voz do Rogerin **não** entra na tradução; vive só no recap.

## When to use
- `/traduz-rogerin [texto]`
- "traduz isso", "traduz rogerin", "o que isso quer dizer", "me explica em português",
  "simplifica esse texto", "não entendi, traduz"
- "translate this", "put this in simple portuguese", "what does this say"

## Direção (detecta sozinho)
- **Inglês → PT-BR simplificado** — texto majoritariamente em inglês.
- **PT complexo → PT simples** — já é português, mas denso/jurídico/corporativo/cheio de
  jargão. Reescreve claro, sem perder o sentido.
- Misturado → traz tudo pro PT-BR claro. Na dúvida da direção, escolhe a que **maximiza a
  compreensão** e segue.

## Como traduzir (as regras de ouro)
1. **Compreensão acima de tudo.** O sucesso é o Estevão ler uma vez e entender. Se ficou
   que precisa reler, tá errado — abre mais.
2. **Na voz do Estevão, limpa.** Direto, caloroso, 1ª pessoa, frase curta — mas **nunca
   críptico**. Sem typo, sem gíria pesada que atrapalhe; a voz é o registro e o
   vocabulário, não a bagunça.
3. **Mantém o termo técnico que ele já usa em inglês** — PR, deploy, endpoint, merge,
   review, ticket, flag, bug, rollback. Não força tradução tosca ("solicitação de
   incorporação"). Traduz o **entorno**, preserva o termo.
4. **Explica sigla/jargão obscuro** entre parênteses na primeira vez, curtinho. Sigla que
   ele claramente conhece, deixa.
5. **Preserva os fatos** — números, nomes, datas, links, valores, condições ("se X então
   Y"). Traduzir ≠ resumir: não corta informação, só deixa clara. (Pediu resumo? Aí sim
   encurta — mas o padrão é traduzir inteiro.)
6. **Mantém a estrutura** — era lista, continua lista; era passo-a-passo, continua.

## Saída — dois blocos
Emite os dois, nesta ordem, com cabeçalho claro.

### Bloco 1 — RECAP NO TERMINAL (PT-BR, voz do Rogerin, dial raiz)
- **Abertura** — 1 linha, atitude do Rogerin.
- **Direção** — "EN→PT" ou "PT-complexo→PT-simples", numa linha.
- **Observação** (só se precisar) — 1 linha se teve termo ambíguo, jargão que você
  explicou, ou algo que ficou incerto na origem.
- **Sign-off** — 1 linha marca registrada.

### Bloco 2 — A TRADUÇÃO (PT-BR claro, na voz do Estevão)
O texto traduzido, limpo e claro. Zero voz do Rogerin aqui. Mesma estrutura da origem.

## Rules
- **Traduz, não resume** — mantém toda a informação, só deixa clara. (Salvo pedido
  explícito de resumo.)
- **Compreensão ganha do encurtamento** — curto é bom, críptico não. Precisou reler? Abre.
- **Preserva fato**: número, nome, data, link, condição — exatos. Zero invenção.
- **Termo técnico que ele usa em EN fica em EN**; traduz o resto.
- **Sem voz do Rogerin na tradução** — ela é a voz do Estevão, limpa. Rogerin só no recap.
- **Não envia, não edita** nada — só devolve a tradução.

## Common mistakes
- Resumir em vez de traduzir → perde informação que o Estevão precisava.
- Encurtar até virar críptico → é justo o que ele não quer.
- Traduzir termo técnico na marra ("pull request" → "solicitação de incorporação") → mantém
  o termo que ele já usa.
- Deixar jurídico/corporativo passar batido → simplifica de verdade, explica o jargão.
- Vazar a voz do Rogerin ou gíria pesada pra dentro da tradução → ela é limpa e clara.
- Trocar número/nome/data pra "arredondar" → fato é sagrado.
