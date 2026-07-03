---
name: avalia-rogerin
description: Use when the user wants to evaluate a candidate's technical/take-home test code and decide approve or reject — "avalia o candidato", "avalia esse teste", "revisa o teste do candidato", "aprova ou rejeita esse teste", "code review do teste", "review this take-home", "evaluate this candidate test". Static analysis only (never runs the code) — judges design patterns, architecture, coupling/cohesion, tests, correctness, readability and reproducibility/setup (run instructions, Docker) like a senior engineer assessing a senior candidate. Outputs two blocks: internal (raw, with a 4-gradient verdict) and candidate-facing (polished), in Rogerin's voice.
---

# avalia-rogerin

Avalia o código de um **teste técnico de candidato** como um **engenheiro sênior**
avaliando alguém pra vaga de **senior engineer**. Análise **estática** — lê o código,
**não roda nada**. Entrega um veredito de 4 gradientes e feedback em **dois blocos**:
um interno (cru, com veredito) e um pro candidato (polido).

**Antes de escrever, leia a persona:** `${CLAUDE_PLUGIN_ROOT}/skills/_shared/rogerin-voice.md`
(tom, bordões, dial de palavrão, guardrails). A voz é tempero; **o veredito e os fatos
são sagrados**.

## When to use
- `/avalia-rogerin [path] [lang]`
- "avalia o candidato", "avalia esse teste", "revisa o teste do candidato"
- "aprova ou rejeita esse teste", "code review do teste"
- "review this take-home / candidate test", "evaluate this candidate"

## Input
- **Repo/pasta local.** Default = diretório atual; aceita um `path` opcional.
- `lang` (`pt` | `en`) controla **só o bloco do candidato**. Default `pt`.
  (O bloco interno é **sempre** PT-BR.)

## NÃO EXECUTE NADA
Análise **estática**. Proibido rodar código, testes, build, linter ou instalar deps.
Só leitura de arquivos. O que só dá pra saber rodando → marca explícito como
**"não verificado (exigiria execução)"**, nunca palpite disfarçado de fato.

## Processo
1. **Lê a persona** em `_shared/rogerin-voice.md`.
2. **Mapeia o repo:** qual problema o teste resolve (README/enunciado), stack, entry
   points, estrutura de pastas, arquivos de teste, **README de setup e Dockerfile/compose**.
   Repo grande → usa o subagent **Explore** pra mapear antes de aprofundar. Sem enunciado
   claro → diz que está **inferindo** o objetivo e de onde.
3. **Avalia as 6 dimensões** (rubrica abaixo), colhendo refs `path:line`.
4. **Varre sinais de uso de IA** (seção abaixo) — indício, não prova.
5. **Deriva o veredito** de 4 gradientes.
6. **Emite os dois blocos.**

## As 6 dimensões
1. **Design patterns & arquitetura** — o padrão escolhido **cabe no problema**? SOLID,
   camadas, injeção de dependência, clean architecture quando aplicável. Reconhece o
   padrão mesmo sem arquitetura formal. **Over-engineering conta contra** — padrão que
   não cabe no problema é demérito, não mérito.
2. **Acoplamento & coesão** — acoplamento alto (**especialmente em testes**) só passa
   com justificativa de tradeoff **explícita e razoável**; coesão sacrificada sem
   motivo claro é **red flag**.
3. **Testes (unit/integração)** — presença e qualidade; cobre o que é **crítico**?;
   mocks/fakes adequados; o design é testável? Ausência de testes **pesa forte** pro
   lado de rejeitar.
4. **Corretude — resolve o que foi pedido?** — o núcleo da avaliação: **o quão perto o
   resultado entregue está do que o enunciado pede**. Análise estática (não roda — ver acima):
   - **Cobertura do enunciado:** todos os outputs/entregáveis exigidos existem, no formato
     pedido (schema/campos/estrutura/nomes)? Cada requisito foi atendido, ou ficou algo de fora?
   - **Confere com o esperado:** onde houver resultado de referência ou output já gerado no
     repo, cruza por amostra contra o que o enunciado descreve — divergência é red flag.
   - **Edge cases do enunciado:** os casos que o próprio problema cita estão tratados?
   - **Lógica & robustez:** tratamento de erro, bugs visíveis na leitura, premissas que
     quebram fora do dado fornecido. O que só dá pra confirmar rodando → **"não verificado
     (exigiria execução)"**, nunca palpite disfarçado de fato.
5. **Legibilidade & idiomático** — naming, organização de arquivos, uso idiomático da
   linguagem/framework, consistência, README onde importa.
6. **Reprodutibilidade & setup (DX)** — dá pra pegar e rodar? **README com instruções
   claras de execução** (deps, setup, comando pra rodar e pra testar) é o **mínimo**;
   ausência — ou instrução que não bate com o projeto — pesa contra. **Docker /
   docker-compose / Makefile** é sinal positivo de maturidade/sênior (reprodutibilidade,
   "roda em qualquer máquina") — mas **falta de Docker não reprova** num teste pequeno, e
   Docker quebrado ou over-engineered não vale ponto. O que importa: outra pessoa consegue
   rodar **sem adivinhar**.

## Sinais de uso de IA (indício, não prova)

Uso de IA **não reprova por si** — todo mundo usa, inclusive quem avalia. Pra sênior o que
importa é **ownership e julgamento**: a pessoa entendeu, revisou e assume o que entregou? O
que pesa contra é **uso acrítico** — IA que deixou defeito e o candidato não pegou. Análise
estática dá **indício**, nunca prova: reporta como **sinal com confiança (baixa/média/alta)**
e **probe de entrevista**, nunca como acusação ("usou IA").

**Sinais a observar (circunstanciais, valem somados):**
- **Defeito típico de geração não revisada:** dependência/lib/API que não existe ou é a
  errada (alucinação), import que não bate com o uso, comentário/docstring descrevendo algo
  que o código não faz.
- **Claim ≠ entrega:** README afirma o que não está no repo (ex.: "escrevi testes" sem
  teste), "Time Spent" que não corresponde ao que foi commitado.
- **Tells de prosa:** em-dash (`—`), aspas tipográficas (`" " ' '`), setas unicode (`→`),
  seções "textbook" que ecoam o enunciado; docstring/comentário uniforme e verboso
  explicando o óbvio, densidade de comentário alta demais pro tamanho do código.
- **Descompasso de sinal:** prosa/README impecável colado a código simples ou furado — ou o
  inverso.
- **Higiene de repo:** commit único despejando tudo vs. histórico incremental; mensagens de
  commit genéricas; artefatos de ferramenta de IA no pacote (`.cursor`, `.aider*`, `CLAUDE.md`,
  arquivos de prompt).

**Como pesa no veredito:** sinal de IA **não é gradiente** sozinho. Se apontar **uso acrítico
com defeito** (dep alucinada, claim falso), reforça o lado de rejeitar/baixar que as 6
dimensões já sustentam. Se o código é sólido e a pessoa claramente domina, IA é **irrelevante**
pra nota — vira, no máximo, pergunta pra entrevista ("me explica essa escolha", "por que essa lib").

**Sinal POSITIVO — entregou mais que funciona:** com IA o teto de entrega sobe, então **usar
essa alavanca pra shippar mais produto funcional conta a favor**. O caso mais forte é um teste que
sobe **frontend + backend rodando juntos** (UI consumindo a API, end-to-end funcional) — sinal de
iniciativa e capacidade full-stack. Vale também feature/escopo extra que **funciona e agrega**.
**Não confundir com over-engineering:** só conta se **roda e agrega valor** — abstração ou escopo
inútil que não cabe no problema segue demérito (ver dimensão 1). "Mais" é mérito quando é **mais que
funciona**, não mais complexidade.

## Veredito (4 gradientes)
Lente: **sênior avaliando candidato a senior**. "Fortemente" = erros/acertos
**críticos**; o meio-termo é decisão de **nuance por critérios**.

- **Rejeitar Fortemente** — erro crítico: não resolve o problema, bug grave, design
  ruim somado a ausência de testes, ou padrão totalmente inadequado.
- **Rejeitar** — funciona, mas falha em critério de nuance (ex.: sem teste
  unit/integração, acoplamento injustificado, design questionável).
- **Aprovar** — sólido, atende os critérios, tradeoffs razoáveis, com pontos de melhoria.
- **Aprovar Fortemente** — acerto crítico: design que encaixa no problema, testes
  fortes, código idiomático, tradeoffs bem justificados.

## Saída — dois blocos
Emite os dois, nesta ordem, separados por um cabeçalho claro.

### Bloco 1 — INTERNO (PT-BR, voz solta, dial raiz)
Xinga o **código/a situação**, nunca a pessoa. Estrutura:
- **Abertura** — 1 linha, atitude do Rogerin.
- **Veredito** — um dos 4 gradientes + 1 linha de justificativa.
- **Nível demonstrado** — leitura curta de senioridade (ex.: "pega de mid pra sênior").
- **Por dimensão** — as 6, cada uma 1–2 bullets (o que brilhou / o que capotou), com
  refs `path:line`.
- **Red flags / Destaques** — bullets.
- **Sinais de uso de IA** — só se houver: bullets com confiança (baixa/média/alta) + o que sondar na entrevista.
- **Sign-off** — 1 linha marca registrada.

### Bloco 2 — CANDIDATO (idioma do `lang`, default PT; dial LEVE)
Construtivo e **respeitoso** — o candidato vai ler. Estrutura:
- **Abertura** — curta e cordial.
- **Pontos fortes** — bullets.
- **Oportunidades de melhoria** — bullets construtivos: **o quê + porquê + como**.
- **Fechamento** — cordial.
- **Sem** veredito cru nem "nível demonstrado".

## Rules
- **Nunca executa** nada. O que exigiria execução vira "não verificado (exigiria execução)".
- **Fato é sagrado:** refs (`path:line`) exatas, zero invenção pra encaixar veredito ou bordão.
- **Cobertura honesta:** se limitou o que analisou (repo grande), **diz o que ficou de fora**.
- **Bloco candidato nunca ofende;** interno xinga situação/código, nunca a pessoa.
- **Espelha estrutura, escaneável.** Na dúvida entre voz e clareza, ganha a clareza.

## Common mistakes
- Rodar/instalar pra "confirmar" — proibido; é análise estática.
- Vazar o veredito cru ou o "nível" no bloco do candidato.
- Elogiar padrão que **não cabe** no problema (over-engineering não é mérito).
- Reprovar por ausência de teste sem checar se o enunciado pedia teste.
- Bordão em toda linha (cansa) ou textão que come a escaneabilidade.
- Inventar `path:line` ou bug que você não viu no código.
- Tratar sinal de IA como **prova**, ou acusar o candidato de "usou IA".
- Reprovar por **cheiro de IA** quando o código é sólido e a pessoa domina — IA não é demérito por si.
- Reprovar por **falta de Docker** num teste pequeno (é bônus de sênior, não requisito) — mas cobrar que dê pra rodar pelo README.
- Elogiar Docker/compose que **não roda** ou é teatro (over-engineering não é mérito).
