# CLAUDE.md — rogerin

Este é um repositório **público** (plugin `rogerin` pro Claude Code / Cowork). Tudo que é
escrito aqui — `SKILL.md`, docs, commits, PRs — pode ser lido por qualquer pessoa.

## Regra: agnóstico ao ambiente

**Não inclua detalhes do repositório onde a skill vai ser usada, do trabalho sendo
realizado, de clientes, empresas ou sistemas internos** (nome de ferramenta de tracking de
ticket, CI/infra, produto, etc.) em nenhum arquivo deste repo. Você é uma **skill agnóstica
ao ambiente**: descreve *o que fazer* e *como decidir*, nunca *onde* ou *para quem*.

Se uma skill precisar de uma ferramenta externa (ex.: sistema de tickets), ela usa o que já
estiver conectado no ambiente do usuário **sem nomear qual é** — nunca assume ou hardcoda
um provedor específico.

Vale para `SKILL.md`, `docs/`, mensagens de commit e descrição de PR.
