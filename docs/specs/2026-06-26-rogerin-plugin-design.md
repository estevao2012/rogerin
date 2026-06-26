# Design — Rogerin: plugin de skills do Claude Code/Cowork

**Data:** 2026-06-26
**Autor:** Estevão Andrade (estevao2012)
**Status:** Aprovado (brainstorming) — pendente review do spec

## Objetivo

Ter um repositório no GitHub (`estevao2012/rogerin`, público) que guarda as skills
que o Estevão usa no Claude Code e no Claude Cowork. Em qualquer instância nova,
basta adicionar o marketplace e instalar o plugin — as skills ficam disponíveis,
sem copiar arquivo na mão.

A coleção tem identidade própria: **Rogerin**, homenagem ao Rogerinho do Ingá
(Choque de Cultura). As skills fazem coisas úteis, mas entregam o resultado **com
a personalidade do Rogerinho** — atitude, gíria, bordões — sem nunca sacrificar a
correção e a utilidade do conteúdo ("personalidade ímpar, porém útil").

## Decisões (do brainstorming)

| Decisão | Escolha |
|---|---|
| Personalidade | **Voz no output** — deliverables saem com a voz do Rogerinho, corretos por baixo |
| Estrutura | **Repo único** = marketplace + plugin `rogerin` (padrão auto-contido) |
| Visibilidade | **Público** — install trivial em máquina nova |
| Onde mora a voz | **Doc compartilhado** `skills/_shared/rogerin-voice.md`, referenciado por todas as skills |
| Dial de palavrão | **Raiz** — palavrão como tempero on-brand permitido; dial ajustável num lugar só |
| Cópia local atual | **Aposentar** `~/.claude/skills/resume-rogerin` depois de validar o plugin |

## Arquitetura

Repositório auto-contido que é, ao mesmo tempo, marketplace e plugin:

```
rogerin/
├── .claude-plugin/
│   ├── marketplace.json      # marketplace "rogerin"; um plugin; source "./"
│   └── plugin.json           # plugin "rogerin"; versão; keywords
├── skills/
│   ├── _shared/
│   │   └── rogerin-voice.md   # A persona: tom, bordões, guardrails, dial
│   └── resume-rogerin/
│       └── SKILL.md           # portado do local + camada de voz
├── docs/
│   └── specs/
│       └── 2026-06-26-rogerin-plugin-design.md
└── README.md                  # o que é + comando de install
```

### Componentes

**`.claude-plugin/marketplace.json`** — declara o marketplace `rogerin` com um único
plugin `rogerin` apontando para `./` (mesmo repo) — padrão de marketplace auto-contido.

**`.claude-plugin/plugin.json`** — metadados do plugin `rogerin`: nome, descrição,
versão (`0.1.0`), autor, `repository`, e `keywords` (rogerin, resume, resumo, tldr,
choque-de-cultura...).

**`skills/_shared/rogerin-voice.md`** — fonte única da personalidade. Define:
- **Persona:** Rogerinho do Ingá — atitude, gíria PT-BR, bordões
  ("tá ligado?", "vai pra cima deles", "é nóis", "Renaaan", "tamo junto").
- **Guardrails (a metade "útil"):** estrutura e fatos são sagrados — bullets continuam
  bullets; refs/paths/tickets/comandos continuam exatos; nada inventado pela piada.
  A voz entra na **abertura, no fraseado e na assinatura**, nunca ao custo da clareza
  ou da escaneabilidade.
- **Dial de palavrão:** padrão **raiz** (palavrão como interjeição/tempero on-brand,
  ex. "porra", "caralho" como ênfase). Ajustável num único parâmetro descrito no doc —
  de "raiz" a "leve/work-safe" — sem editar cada skill.
- **Anti-padrões:** virar texto corrido só pela piada, esconder o próximo passo,
  ofender pessoa real, inventar fato pra encaixar bordão.

**`skills/resume-rogerin/SKILL.md`** — primeira skill. Mesma estrutura/valor da versão
local de hoje (TL;DR em 3 seções: **Problema / Feito / Próximos passos**, bullets,
≤~12 bullets, espelha o idioma do usuário), mas:
- Abertura curta com a voz do Rogerinho.
- Bullets com atitude no fraseado, **sem** perder ref (`path:line`, PR, ticket, comando).
- Assinatura/sign-off característico.
- Referencia `_shared/rogerin-voice.md` como fonte da persona.
- Mantém as regras de hoje: bullets only, ser específico, puxar do estado atual da
  sessão, `- (nada ainda)` quando vazio.

**`README.md`** — o que é o Rogerin + instalação:
```
/plugin marketplace add estevao2012/rogerin
/plugin install rogerin@rogerin
```

## Fluxo de uso

1. Máquina nova → `/plugin marketplace add estevao2012/rogerin` → `/plugin install rogerin@rogerin`.
2. Usuário pede um resumo ("rogerin resumo", "tldr", "onde paramos"...) → a skill
   `resume-rogerin` dispara e entrega o TL;DR na voz do Rogerinho.
3. Skills novas → criar `skills/<nome>/SKILL.md` referenciando `_shared/rogerin-voice.md`,
   commit, push. Próximo `/plugin update` distribui pra todas as instâncias.

## Tratamento de erros / edge cases

- **Colisão de nome:** o plugin passa a ser a fonte canônica de `resume-rogerin`;
  a cópia local em `~/.claude/skills/resume-rogerin` é removida **depois** de validar.
- **Repo privado vs público:** público evita auth no install em máquina nova. Como é
  pessoal e sem segredo, é público.
- **Voz forte demais:** o dial em `_shared/rogerin-voice.md` permite baixar o tom sem
  tocar nas skills.

## Verificação (definição de pronto)

- `marketplace.json` e `plugin.json` válidos (JSON parseia; nomes batem).
- Repo público criado em `estevao2012/rogerin` e push do `main`.
- Instalar o marketplace/plugin localmente e confirmar que `resume-rogerin` aparece
  e dispara, entregando um resumo na voz do Rogerin — correto e escaneável.
- Só então remover a cópia local `~/.claude/skills/resume-rogerin`.

## Fora de escopo (YAGNI)

- Outras skills além da `resume-rogerin` (entram depois, uma a uma).
- Hooks, MCP servers, agents.
- Marketplace multi-plugin (a estrutura permite evoluir, mas não agora).
- CI/release automation.
