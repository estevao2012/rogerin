# Design — Rogerin: skill `posta-no-slack-rogerin` (posts profissionais no Slack)

**Data:** 2026-07-10
**Autor:** Estevão Andrade (estevao2012)
**Status:** Aprovado (brainstorming)

## Objetivo

Adicionar ao plugin `rogerin` uma skill que **redige (e opcionalmente envia) mensagens de
Slack**. O texto que vai pro Slack é **sempre inglês profissional** — **curto, mas com
detalhe suficiente pra deixar a intenção clara**. Mostra o rascunho e **só envia depois do
OK** do usuário; no terminal entrega o recap na **voz do Rogerin** (PT-BR).

Como toda skill do plugin, a voz é tempero — mas aqui ela **não entra no deliverable**: o
post é profissional, a voz vive só no recap do terminal ("personalidade ímpar, porém útil").

## Decisões (do brainstorming)

| Decisão | Escolha |
|---|---|
| Trigger | **Sob demanda** — invocação manual; sem hook/automação |
| Input | Intenção a comunicar + canal/pessoa (opcional) + FYI vs. tem-ação |
| Deliverable | Post em **inglês profissional**, curto, formato mrkdwn do Slack |
| Voz no post | **Zero** — profissional, sem gíria/palavrão/Rogerin |
| Voz no terminal | **Rogerin raiz** (PT-BR) — só no recap |
| Envio | Via **Slack MCP** (`slack_send_message` etc.) **após OK**; fallback copiar-e-colar |
| Confirmação | **Obrigatória** — mostra o rascunho e só envia após OK explícito |
| Receita | Bottom-line first → contexto → específicos → CTA → links/menções |
| Audiência | **Dois blocos** — recap no terminal (Rogerin) e post pro Slack (inglês) |
| Nome | `posta-no-slack-rogerin` |

## Arquitetura

Nova skill instrucional (Claude segue o `SKILL.md`; sem código executável), no padrão das
skills existentes do plugin. Espelha o `revisa-o-pr-rogerin`: deliverable profissional em
inglês + recap Rogerin no terminal + confirmação antes de qualquer ação externa.

```
rogerin/
├── skills/
│   ├── _shared/rogerin-voice.md          # persona compartilhada (já existe)
│   ├── resume-rogerin/SKILL.md           # já existe
│   ├── avalia-rogerin/SKILL.md           # já existe
│   ├── revisa-o-pr-rogerin/SKILL.md      # já existe
│   └── posta-no-slack-rogerin/SKILL.md   # NOVO
├── .claude-plugin/
│   ├── marketplace.json                  # bump de versão + descrição
│   └── plugin.json                       # + keywords, bump de versão
├── docs/specs/
│   └── 2026-07-10-posta-no-slack-rogerin-design.md
└── README.md                             # lista a nova skill
```

## Componentes

### `skills/posta-no-slack-rogerin/SKILL.md`

**Frontmatter** — `name: posta-no-slack-rogerin`; `description` com gatilhos (PT + EN) e a
forma do deliverable (inglês profissional, curto, confirma antes de enviar).

**Processo:**
1. Lê a persona em `_shared/rogerin-voice.md` (só pro recap).
2. Confere se tem o essencial (o quê, pra quem, tem ação?) — faltou → pergunta.
3. Escreve o post pela receita (bottom-line first → contexto → específicos → CTA → links).
4. Monta os dois blocos e **mostra pro usuário**.
5. Só após OK: resolve o canal (`slack_search_channels`) e envia (`slack_send_message` /
   `slack_schedule_message` / `slack_send_message_draft`). Sem Slack conectado → copiar-e-colar.

**Receita do post:** (1) bottom line / pedido primeiro, (2) contexto mínimo, (3) específicos
(bullets se >2), (4) CTA explícito ou `*FYI*`, (5) links + @menções só de quem age. Tom
profissional e direto; ~15s de leitura; detalhe extra vai pra thread.

**mrkdwn:** `*bold*` (um asterisco), `_italic_`, `` `code` ``, `> quote`, `• bullet`,
`<url|texto>`, `@user`/`#channel`. Evita `@here`/`@channel` sem necessidade.

**Saída — dois blocos:**
- *Recap no terminal* (PT-BR, Rogerin raiz): abertura, destino, resumo, "vou mandar", sign-off.
- *Post pro Slack* (inglês profissional, mrkdwn): exatamente o que será enviado.

### `.claude-plugin/plugin.json` e `marketplace.json`
- `plugin.json`: adiciona keywords (`slack`, `post`, `mensagem`, `comunicado`, `announcement`)
  e bump de versão (`0.3.1` → `0.4.0`).
- `marketplace.json`: bump de versão e descrição batendo com o plugin.

### `README.md`
- Lista `posta-no-slack-rogerin` na tabela de skills e na árvore de estrutura.

## Fluxo de uso

1. Estevão: "posta no Slack que o deploy X saiu" (ou `/posta-no-slack-rogerin`).
2. Rogerin confere o essencial, escreve o post em inglês profissional pela receita.
3. Mostra o **recap** (voz do Rogerin) + o **rascunho do post** e pergunta se pode mandar.
4. Com o OK, envia via Slack MCP (ou devolve pra copiar, se não conectado).

## Tratamento de erros / edge cases

- **Faltou contexto** (canal, se tem ação): pergunta antes de escrever, não inventa.
- **Slack não conectado:** devolve o texto pronto pra copiar; nunca finge envio.
- **Mensagem longa:** miolo na mensagem, detalhe na thread reply.
- **Voz por canal:** post = inglês profissional; terminal = Rogerin raiz. A voz não vaza pro post.
- **Fato é sagrado:** links, nomes, datas, @menções exatos; zero invenção.

## Verificação (definição de pronto)

- `skills/posta-no-slack-rogerin/SKILL.md` criado, frontmatter válido, gatilhos claros.
- `plugin.json` e `marketplace.json` com JSON válido e versões batendo.
- `README.md` lista a nova skill.
- Rodar a skill e confirmar: post sai em inglês profissional pela receita, recap na voz do
  Rogerin, e **nada é enviado sem OK**.

## Fora de escopo (YAGNI)

- Automação/hook que dispara sozinho (hoje: sob demanda).
- Templates fixos por tipo de post (deixa a receita guiar).
- Ler/responder threads existentes como fluxo próprio (foco é redigir + postar).
- Providers de chat além do Slack.
