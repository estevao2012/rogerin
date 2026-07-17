# Design — A voz do Estevão + skills de tom e tradução

**Data:** 2026-07-17
**Autor:** Estevão (via Claude)

## Problema

Os textos que o Claude escreve pro Estevão saem **genéricos** (corporativês, "qualquer
um escreveria isso") ou **encurtados demais** — compressão/abreviação/jargão a ponto de o
próprio Estevão não entender quando lê. Falta um retrato do jeito real dele escrever pra
(a) guiar o que o Claude produz por ele e (b) medir o tom do que ele manda.

## Solução — 3 entregáveis

### 1. `skills/_shared/estevao-voice.md` (a "voz")
Perfil do jeito real do Estevão escrever, montado a partir da análise de ~2 meses de
mensagens dele no Slack (EN + PT-BR, canais públicos/privados/DMs). Irmão do
`rogerin-voice.md`, mas é a voz **autoral do Estevão**, não a persona do Rogerin.

Conteúdo:
- Retrato do estilo em EN e PT-BR, com exemplos.
- Mapa de registro: DM casual → 1:1 → canal de time → anúncio.
- **Régua de tom** com 4 dimensões pra medir qualquer texto:
  1. **Soa como você?** — direto, caloroso, 1ª pessoa, não-genérico.
  2. **Genérico?** — flag buzzword, enrolação, filler.
  3. **Claro / encurtado demais?** — flag compressão críptica, abreviação/jargão que
     trava a leitura (o problema #1 do Estevão).
  4. **Registro certo?** — DM vs time vs anúncio.

### 2. `skills/mede-o-tom-rogerin/SKILL.md` (medir tom)
Recebe um texto (doc ou mensagem a enviar), pontua contra `estevao-voice.md` nas 4
dimensões, marca os trechos problemáticos e sugere um rewrite na voz do Estevão.
Análise estática — não envia, não edita nada. Recap na voz do Rogerin.

### 3. `skills/traduz-rogerin/SKILL.md` (tradução)
Cola EN ou PT complexo → devolve **PT-BR limpo e claro na voz do Estevão**. Detecta a
direção sozinha (EN→PT ou PT-complexo→PT-simples). Mantém termos técnicos que o Estevão
já usa em inglês (PR, deploy, endpoint...). Objetivo: compreensão. Estático, sem efeitos.

### 4. Plumbing
- Bump de versão `0.4.0 → 0.5.0` em `plugin.json` e `marketplace.json` (+ descrições).
- Adiciona as duas skills na tabela do README.

## Fora de escopo
- Não retrofita skills existentes (posta-no-slack etc.). O `mede-o-tom` mede a saída
  delas sem precisar editá-las.
- O perfil é agregado (estilo), não guarda/cita conteúdo sensível de DM verbatim.

## Decisões (do brainstorming)
- Voz empacotada como **doc de referência + skill dedicada** (os dois).
- Tradução devolve **na voz do Estevão** (não neutro).
- Análise do Slack cobre **tudo que ele mandou** (público + privado + DMs).
