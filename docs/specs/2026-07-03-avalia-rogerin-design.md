# Design — Rogerin: skill `avalia-rogerin` (avaliação de teste de candidato)

**Data:** 2026-07-03
**Autor:** Estevão Andrade (estevao2012)
**Status:** Aprovado (brainstorming) — pendente review do spec

## Objetivo

Adicionar ao plugin `rogerin` uma skill que **avalia o código de um teste técnico de
candidato** — análise **estática** (sem executar nada) — como faria um engenheiro
sênior avaliando alguém para uma vaga de **senior engineer**. A skill produz um
veredito (aprovar/rejeitar em 4 gradientes) e feedback em **dois blocos**: um interno
(cru, com veredito) e um para o candidato (polido). Foco nas qualidades do teste:
padrões de projeto, arquitetura, acoplamento/coesão, testes, corretude e legibilidade.

Como toda skill do plugin, entrega com a **voz do Rogerinho do Ingá** — atitude e
gíria por cima, correção e utilidade por baixo ("personalidade ímpar, porém útil").

## Decisões (do brainstorming)

| Decisão | Escolha |
|---|---|
| Estrutura da skill | **SKILL.md único** com a rubrica embutida (padrão da `resume-rogerin`) |
| Input | **Repo/pasta local** — default diretório atual; aceita path opcional |
| Execução | **Nunca executa** código/testes/deps — só leitura estática |
| Veredito | **4 gradientes:** Rejeitar Fortemente / Rejeitar / Aprovar / Aprovar Fortemente |
| Dimensões fixas | Design & arquitetura · Acoplamento & coesão · Testes · Corretude · Legibilidade |
| Audiência | **Dois blocos** — interno (cru + veredito) e candidato (polido) |
| Idioma | Interno **sempre PT-BR**; bloco do candidato escolhido no comando (`pt`/`en`), default `pt` |
| Lente de avaliação | Sênior avaliando candidato a **senior engineer** |
| Nome | `avalia-rogerin` |

## Arquitetura

Nova skill instrucional (Claude segue o `SKILL.md`; sem código executável), no padrão
das skills existentes do plugin.

```
rogerin/
├── skills/
│   ├── _shared/
│   │   └── rogerin-voice.md        # persona compartilhada (já existe)
│   ├── resume-rogerin/
│   │   └── SKILL.md                # já existe
│   └── avalia-rogerin/
│       └── SKILL.md                # NOVO
├── .claude-plugin/
│   ├── marketplace.json            # bump de versão
│   └── plugin.json                 # + keywords, bump de versão
├── docs/specs/
│   └── 2026-07-03-avalia-rogerin-design.md
└── README.md                       # lista a nova skill
```

## Componentes

### `skills/avalia-rogerin/SKILL.md`

**Frontmatter** — `name: avalia-rogerin`; `description` com os gatilhos (ver abaixo).

**Trigger / when to use:**
- `/avalia-rogerin [path] [lang]`
- "avalia o candidato", "revisa o teste", "aprova ou rejeita esse teste",
  "code review do teste", "review this take-home / candidate test"

**Input:**
- Repo/pasta local. Default = diretório atual; aceita path opcional.
- `lang` (`pt`/`en`) controla **só o bloco do candidato**; default `pt`.

**Processo:**
1. Lê `${CLAUDE_PLUGIN_ROOT}/skills/_shared/rogerin-voice.md` (persona).
2. Mapeia o repo: qual problema o teste resolve (README/enunciado), stack, entry
   points, estrutura, arquivos de teste. Se o repo for grande, usa o subagent Explore
   para mapear antes de aprofundar.
3. Avalia nas 5 dimensões (rubrica abaixo).
4. Deriva o veredito de 4 gradientes.
5. Emite os dois blocos.

**As 5 dimensões (rubrica):**
1. **Design patterns & arquitetura** — o padrão escolhido cabe no problema? SOLID,
   camadas, DI, clean architecture quando aplicável. Reconhece o padrão mesmo sem
   arquitetura formal. **Over-engineering conta contra** (padrão que não cabe no
   problema é demérito, não mérito).
2. **Acoplamento & coesão** — **regra especial:** acoplamento alto (especialmente em
   testes) só é aceitável com justificativa de tradeoff **explícita e razoável**;
   coesão sacrificada sem motivo claro é red flag.
3. **Testes (unit/integração)** — presença e qualidade; cobre o que é crítico?;
   mocks/fakes adequados; testabilidade do design. Ausência de testes pesa forte para
   o lado de rejeitar.
4. **Corretude & funciona razoável** — leitura estática: resolve o problema?, edge
   cases, tratamento de erro, bugs visíveis. Nunca executa; o que só dá para saber
   rodando é marcado como "não verificado (exigiria execução)".
5. **Legibilidade & idiomático** — naming, organização de arquivos, uso idiomático da
   linguagem/framework, consistência, README onde importa.

**Veredito (4 gradientes)** — lente de sênior avaliando candidato a senior:
- **Rejeitar Fortemente** — erro crítico: não resolve o problema, bug grave, design
  ruim somado a ausência de testes, ou padrão totalmente inadequado.
- **Rejeitar** — funciona, mas falha em critério de nuance (ex.: sem teste
  unit/integração, acoplamento injustificado, design questionável).
- **Aprovar** — sólido, atende os critérios, tradeoffs razoáveis, com pontos de melhoria.
- **Aprovar Fortemente** — acerto crítico: design que encaixa no problema, testes
  fortes, código idiomático, tradeoffs bem justificados.

O gradiente "Fortemente" indica **erros ou acertos críticos**; o meio-termo
(Rejeitar/Aprovar) é decisão de nuance por critérios.

**Saída — dois blocos:**

*Bloco 1 — INTERNO* (PT-BR; voz do Rogerin solta, dial raiz; xinga o código, nunca a
pessoa):
- Abertura (1 linha) na voz do Rogerin.
- **Veredito:** gradiente + 1 linha de justificativa.
- **Nível demonstrado:** leitura curta de senioridade.
- **Por dimensão:** as 5, cada uma 1–2 bullets (o que brilhou / o que capotou) com
  refs `path:line`.
- **Red flags / Destaques:** bullets.
- Sign-off (1 linha).

*Bloco 2 — CANDIDATO* (idioma escolhido, default PT; voz contida, **dial leve**;
construtivo e respeitoso):
- Abertura curta e cordial.
- **Pontos fortes:** bullets.
- **Oportunidades de melhoria:** bullets construtivos (o quê + porquê + como).
- Fechamento cordial.
- **Sem** veredito cru nem "nível demonstrado".

### `.claude-plugin/plugin.json` e `marketplace.json`
- `plugin.json`: adiciona keywords (ex.: `avalia`, `code-review`, `candidato`,
  `entrevista`, `hiring`) e faz bump de versão (`0.1.1` → `0.2.0`).
- `marketplace.json`: bump de versão para bater com o plugin.

### `README.md`
- Lista `avalia-rogerin` junto de `resume-rogerin`, com 1 linha do que faz e o gatilho.

## Fluxo de uso

1. Estevão está num repo com o teste do candidato → `/avalia-rogerin` (ou "avalia esse
   teste", opcional `en` para o bloco do candidato em inglês).
2. Rogerin lê a persona, mapeia o repo, avalia as 5 dimensões (sem rodar nada).
3. Entrega o **bloco interno** (veredito + análise por dimensão, na voz do Rogerin) e
   o **bloco do candidato** (polido, no idioma escolhido).
4. Estevão usa o interno para decidir e o do candidato para dar retorno.

## Tratamento de erros / edge cases

- **Sem enunciado/README:** se o problema do teste não estiver claro, o Rogerin diz
  que está **inferindo** o objetivo e de onde (estrutura, nomes, testes).
- **Repo grande:** usa subagent Explore para mapear antes de aprofundar; se precisar
  limitar cobertura, **diz o que ficou de fora** (sem fingir que analisou tudo).
- **Tentação de executar:** proibido. O que exigiria execução vira "não verificado
  (exigiria execução)", nunca um palpite disfarçado de fato.
- **Voz vs. respeito:** bloco interno xinga o código/situação, **nunca a pessoa**;
  bloco do candidato usa dial leve e é sempre respeitoso e construtivo.
- **Fato é sagrado:** refs (`path:line`) exatas; zero invenção para encaixar veredito
  ou bordão.

## Verificação (definição de pronto)

- `skills/avalia-rogerin/SKILL.md` criado, com frontmatter válido e gatilhos claros.
- `plugin.json` e `marketplace.json` com JSON válido e versões batendo.
- `README.md` lista a nova skill.
- Rodar a skill num repo de teste de exemplo e confirmar: os dois blocos saem, o
  interno traz o veredito de 4 gradientes com refs, o do candidato é polido e no
  idioma escolhido, e **nenhum** comando de execução foi disparado.

## Fora de escopo (YAGNI)

- Executar código, testes ou instalar dependências.
- Rubrica em arquivo separado (`rubric.md`) — só se a rubrica crescer muito depois.
- Scorecard numérico por dimensão / múltiplos subagents de scoring.
- Suporte a input via PR/link do GitHub ou código colado no chat (hoje: só repo local).
- Idiomas além de PT/EN.
