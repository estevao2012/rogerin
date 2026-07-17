# 🚐 Rogerin

> *"Larga o aço!"* — Rogerinho do Ingá

As skills que eu uso no **Claude Code** e no **Claude Cowork**, num lugar só. Faço o
setup de uma instância nova, instalo esse plugin, e tá tudo na mão — sem copiar arquivo
na unha.

A coleção tem personalidade: homenagem ao **Rogerinho do Ingá** (Choque de Cultura).
As skills fazem coisa útil, mas entregam o resultado com a **voz do Rogerin** — atitude,
gíria, bordão. Personalidade ímpar, **porém útil**: estrutura e fatos sempre intactos.

## Instalação

Em qualquer máquina nova, dentro do Claude Code / Cowork:

```
/plugin marketplace add estevao2012/rogerin
/plugin install rogerin@rogerin
```

Pronto. As skills aparecem. Pra atualizar depois de adicionar skills novas: `/plugin update`.

## Skills

| Skill | O que faz |
|---|---|
| `resume-rogerin` | TL;DR da thread atual (Problema / Feito / Próximos passos), na voz do Rogerin. Dispara com "rogerin resumo", "tldr", "onde paramos", "where are we"... |
| `avalia-rogerin` | Avalia o código de um teste de candidato (análise estática, não roda nada): veredito em 4 gradientes + feedback em dois blocos (interno cru e polido pro candidato). Dispara com "avalia esse teste", "aprova ou rejeita", "review this take-home"... |
| `revisa-o-pr-rogerin` | Revisa um PR do GitHub (só o diff, não roda nem edita nada): classifica achados por severidade e crava **Approve** ou **Request changes** — qualquer médio/alto bloqueia. Posta o review em inglês profissional com comentários inline (só depois do teu OK) e te dá o recap na voz do Rogerin. Dispara com "revisa esse PR", "review this PR"... |
| `posta-no-slack-rogerin` | Redige uma mensagem de Slack em **inglês profissional** — curta, mas com detalhe suficiente pra explicar a intenção — no formato mrkdwn. Mostra o rascunho e só envia depois do teu OK (via Slack, ou texto pra copiar); recap na voz do Rogerin. Dispara com "posta no Slack", "avisa o time no Slack", "write a Slack post"... |
| `mede-o-tom-rogerin` | Mede se um texto (doc ou mensagem que você vai mandar) **soa como você** antes de sair. Pontua contra teu perfil de voz em 4 dimensões (soa como você / genérico / **encurtado demais** / registro), marca os trechos e sugere rewrite. Só análise — não envia nem edita. Dispara com "mede o tom", "isso soa como eu?", "tá genérico?", "check the tone"... |
| `traduz-rogerin` | Cola inglês ou português complicado → devolve **PT-BR limpo e claro na tua voz**, feito pra você entender de primeira. Detecta a direção sozinha e mantém termo técnico que você já usa em inglês (PR, deploy...). Estático. Dispara com "traduz isso", "me explica em português", "simplifica esse texto", "translate this"... |

## A voz

São **duas** vozes, cada uma no seu papel:

- [`skills/_shared/rogerin-voice.md`](skills/_shared/rogerin-voice.md) — a **persona do
  Rogerin** (Choque de Cultura): o tempero do recap no terminal. Tem um **dial de
  palavrão** (raiz / médio / leve) que ajusta o tom de todas as skills num lugar só.
  Padrão: **raiz**.
- [`skills/_shared/estevao-voice.md`](skills/_shared/estevao-voice.md) — a **voz autoral do
  Estevão**: o jeito real dele escrever (EN + PT-BR), montado a partir da análise das
  mensagens dele no Slack. É o alvo pro que o Claude escreve por ele e a **régua de tom**
  (4 dimensões) usada por `mede-o-tom-rogerin` e `traduz-rogerin`.

## Adicionar uma skill nova

1. `skills/<nome>/SKILL.md` com frontmatter (`name`, `description` com os gatilhos).
2. Referencie a persona: `${CLAUDE_PLUGIN_ROOT}/skills/_shared/rogerin-voice.md`.
3. Bump da versão em `.claude-plugin/plugin.json` e `marketplace.json`.
4. Commit + push. Nas outras instâncias: `/plugin update`.

## Estrutura

```
rogerin/
├── .claude-plugin/
│   ├── marketplace.json   # marketplace "rogerin" → plugin "rogerin" (source ".")
│   └── plugin.json        # metadados do plugin
├── skills/
│   ├── _shared/
│   │   ├── rogerin-voice.md    # a persona do Rogerin (tom, bordões, dial, guardrails)
│   │   └── estevao-voice.md    # a voz autoral do Estevão (perfil + régua de tom)
│   ├── resume-rogerin/
│   │   └── SKILL.md
│   ├── avalia-rogerin/
│   │   └── SKILL.md
│   ├── revisa-o-pr-rogerin/
│   │   └── SKILL.md
│   ├── posta-no-slack-rogerin/
│   │   └── SKILL.md
│   ├── mede-o-tom-rogerin/
│   │   └── SKILL.md
│   └── traduz-rogerin/
│       └── SKILL.md
└── docs/specs/            # designs
```

É nóis. 🚐💨
