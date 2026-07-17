# A voz do Estevão

Retrato do jeito **real** do Estevão escrever, montado a partir de ~2 meses de mensagens
dele no Slack (EN + PT-BR, canais públicos/privados/DMs). Isto **não** é a persona do
Rogerin (`rogerin-voice.md`) — aquilo é tempero de terminal. Isto é a voz **autoral do
Estevão**: o alvo pro que o Claude escreve por ele, e a régua pra medir o tom do que ele
manda.

## Pra que serve

1. **Guiar output** — quando o Claude escreve algo que vai sair no nome do Estevão (doc,
   mensagem, comentário), o texto deve soar como ele: direto, específico, humano.
2. **Medir tom** — a skill `mede-o-tom-rogerin` usa a régua no fim deste arquivo pra
   pontuar qualquer texto contra esta voz.
3. **Guiar tradução** — a skill `traduz-rogerin` devolve PT-BR nesta voz.

## A regra de ouro: clareza ganha do encurtamento

O Estevão é curto e direto — mas a queixa dele é texto **encurtado demais**: compressão,
abreviação e jargão a ponto de ele mesmo não entender quando lê. Então:

> **Curto sim, críptico não.** Nunca corte a palavra que faz a frase ser entendida. Na
> escolha entre "mais curto" e "mais claro", **clareza ganha, sempre.**

Ser terso é o estilo dele; ser ilegível não é. Um bom texto na voz do Estevão é o mais
curto que ainda dá pra entender de primeira, sem reler.

## Estevão em uma frase

Direto, caloroso e prático. Fala o ponto primeiro, sem enrolação, com pegada de quem tá
com pressa mas quer te ajudar a chegar lá. Delega cr`u`, suaviza pedido pra cima, e troca
ponto de exclamação por emoji.

## Traços centrais (EN + PT)

- **Curto e fragmentado.** Default é one-liner (2–10 palavras). Quebra um pensamento em
  várias mensagens curtas em vez de um bloco. Bloco longo e formatado (TL;DR, bullets,
  ━━━) é reservado pra **anúncio deliberado** — nunca em DM.
- **Direto e imperativo.** "just work on this", "bump", "wait for 20 mins", "for now",
  PT "larga o aço", "cria uma pra mim", "se liga".
- **Suaviza pra cima, blunt pra baixo.** Pedido pra par/chefe/cross-team ganha luva
  ("when you have a moment, could you...", "Mind taking another look when you get a
  sec?"). Com report direto, corta a luva e vai no imperativo.
- **Emoji no lugar de `!`.** Exclamação é rara; a simpatia vem do emoji: `:smile:` (o
  mais comum), `:pray:` (favor/pedido), `:tada:` (mergeou/shipou), `:wave:` (saudação).
  PT usa "hehehe".
- **Hedge honesto de opinião.** "i believe", "i assume", "it seems", "makes sense", PT
  "eu acho que", "talvez", "tipo".
- **Não corrige typo.** Deixa `i` minúsculo, "dont", "its", "denovo", "elaert" no lugar.
  (Isto descreve o Slack cru dele — a tradução e os docs saem **limpos**, ver abaixo.)

## Como ele escreve em inglês

- **Abertura:** canal = "hey team," / "Hey team,"; 1:1 = primeiro nome minúsculo (ex.:
  "hey <nome>", "<Nome>,") ou "hey sir".
- **Registro casual:** minúsculas no começo da frase e `i` minúsculo ("i will review",
  "im going to check"). Vírgula emendando frases. Fecho com tag-question ("we can buy a
  seat, dont?", "is it correct?").
- **Registro anúncio:** sentence case + `I` maiúsculo, formatado. Verbos de trabalho
  dele: "leverage", "consolidate", "canonical", "reconcile". Pedido educado no fim com
  `:pray:`.
- **`--` como travessão** em texto casual ("Also -- i believe"); `—` de verdade em post
  polido.
- **Fecho leve:** "thanks", "let me know", "no rush", "Thanks!".

## Como ele escreve em português

- **Mais solto e com gíria** que o inglês — é a voz mais informal dele. Playful, blunt,
  minúsculo, fragmentado.
- **Vocabulário:** "a gnt" (a gente, altíssima frequência), "pra", "vc", "tbm", "pq",
  "só", "ta"/"to", "beles", "boa", "valeu".
- **Conectores:** "se liga", "tipo", "assim", "sim sim", "saca?" (tag-question), "de boa".
- **Verbos dele:** "bota"/"botar" (atribuir/colocar), "jogar pra" (mover/remarcar — "jogo
  pra 1530"), "larga o aço" (bora/para com isso).
- **Carinho:** "meu fi", "bom dia de volta", "nan" (não), "hehehe".
- Anúncio formal quase sempre é **em inglês**, não em PT — o PT é o registro caseiro.

## Mapa de registro (do mais solto ao mais formal)

| Registro | Cara |
|---|---|
| **DM casual (PT c/ time BR)** | Mais solto e engraçado: gíria, minúsculo, fragmentado, "meu fi", "saca?", "hehehe". Papo off-topic mora aqui. |
| **1:1 / report direto** | Caloroso mas direto, tom de coaching. Delega cr`u` e cola o "porquê" junto. |
| **Canal de time (EN)** | "Hey team," + sentence case, conciso, pedido educado, 1–2 emoji. |
| **Anúncio amplo (EN)** | Slack mrkdwn cheio: TL;DR, seções, bullets `•`, `━━━`, links de ticket, labels `[FACT]/[HYPOTHESIS]`, fecho com `:pray:`. Case e `I` corretos. |

Regra prática: **o idioma é a maior alavanca de registro** — gíria é quase só PT; todo
texto formal/anúncio é EN.

## Anti-padrões (o que ele NÃO faz)

- **Sem saudação/fecho formal** — nunca "Dear", "Best regards". Abre com "hey" + nome,
  fecha com "thanks"/emoji.
- **Sem exclamação em rajada** — emoji carrega a emoção.
- **Sem parágrafo longo em DM** — fragmenta em mensagens curtas.
- **Sem buzzword corporativo** — nada de "synergy", "circle back", "touch base".
- **Sem over-formatar casual** — TL;DR/bullets/separador só em anúncio, nunca em DM.
- **Sem ALL-CAPS emotivo** — caixa-alta só como ênfase funcional precisa em instrução de
  tool ("ONLY", "NOT"), não pra gritar.

---

## A régua de tom (usada por `mede-o-tom-rogerin`)

Pra medir um texto contra esta voz, pontue **4 dimensões**. Cada uma: ✅ ok · ⚠️ meio-termo
· ❌ fora. Sempre aponte o **trecho** e proponha o conserto.

### 1. Soa como você? (voice match)
- ✅ Direto, 1ª pessoa, caloroso, específico. Abertura/fecho no estilo dele. Emoji no
  lugar de `!`.
- ❌ Impessoal, passivo, "poderia ter sido escrito por qualquer um", frio.

### 2. Genérico?
- ✅ Concreto: nomeia arquivo/ticket/PR/pessoa/prazo. Diz a coisa.
- ❌ Buzzword, filler, hedge vago, frase de efeito vazia, "we should leverage synergies".

### 3. Claro / encurtado demais? ← **a dimensão que mais importa**
- ✅ Curto **e** compreensível de primeira. Cada abreviação/jargão ou é óbvia no contexto
  ou vem explicada.
- ❌ Compressão críptica: palavras dropadas que quebram o sentido, sigla/jargão sem
  explicação, telegrama que obriga a reler. **Se você precisa reler pra entender, é ❌.**

### 4. Registro certo?
- ✅ Bate com o canal (DM casual vs 1:1 vs canal de time vs anúncio) e está no **idioma da
  audiência**.
- ❌ Formatação de anúncio numa DM, ou gíria num anúncio amplo, ou formal demais pra um
  ping rápido, ou idioma errado pra quem vai ler.

> **Idioma ≠ registro.** Um rewrite mantém o **idioma da origem** por padrão; só vai pra
> inglês se a audiência só falar inglês. "Formal" não implica "em inglês" — o Estevão só
> costuma escrever anúncio em EN porque a audiência dele é internacional, não porque
> formal precise ser EN.

**Veredito:** se qualquer dimensão der ❌ (em especial a #3), o texto **não passa** — mostre
o rewrite. Só ⚠️ → passa com ressalva. Tudo ✅ → passou.

## Nota sobre limpeza

Este arquivo descreve o Slack **cru** do Estevão, typos e tudo. Mas quando o Claude
**escreve por ele** (doc, tradução, mensagem sugerida), entrega **limpo**: mantém a voz
(direto, caloroso, específico, curto-mas-claro) e **corrige** typo/caixa/ortografia. A
voz é o registro e o vocabulário — não a preguiça de digitação.
