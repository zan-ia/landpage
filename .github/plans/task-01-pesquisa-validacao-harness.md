# Tarefa 01 — Pesquisa e Validação do Harness Copilot

## Objetivo
Pesquisar na documentação oficial do VS Code e GitHub Copilot como funciona o sistema de customização (agents, skills, instructions, commands, AGENTS.md), validar se o que já existe no projeto está correto e identificar gaps.

## Responsabilidades Específicas

1. **Pesquisar documentação oficial** sobre:
   - `.agent.md` — formato, campos YAML (`name`, `description`, `tools`, `agents`, `user-invocable`, `disable-model-invocation`)
   - `.instructions.md` — formato, `applyTo` glob patterns, `description`, precedência
   - `SKILL.md` — formato, quando e como skills são carregados
   - `.prompt.md` — comandos customizados (slash commands)
   - `AGENTS.md` — arquivo raiz de configuração do workspace
   - `copilot-instructions.md` — instruções genéricas do Copilot

2. **Validar o que já existe** no projeto:
   - `.github/agents/*.agent.md` — 7 agentes: estão com YAML e conteúdo corretos?
   - `.github/instructions/*.instructions.md` — 7 arquivos: `applyTo` patterns estão corretos?
   - `.github/skills/*/SKILL.md` — 5 skills: estrutura e conteúdo corretos?
   - `.github/prompts/*.prompt.md` — 6 comandos: formato e conteúdo corretos?
   - `AGENTS.md` — cobre todas as áreas necessárias?

3. **Identificar gaps** — o que falta ou está incorreto:
   - Agentes que deveriam existir mas não existem
   - Instruções faltantes (ex: `svelte.instructions.md`, `typescript.instructions.md`)
   - Skills que precisam ser criados
   - Prompts/comandos que faltam

4. **Documentar descobertas** em um relatório de validação

## Fontes de Pesquisa
- Documentação oficial do VS Code: `get_vscode_api` tool
- GitHub Copilot docs: https://docs.github.com/en/copilot
- VS Code Copilot Chat customization docs
- `agent-customization` skill (já disponível no sistema)

## Artefato de Saída
- Relatório `docs/harness-validation-report.md` com:
  - O que está correto ✅
  - O que precisa de ajuste ⚠️
  - O que está faltando ❌
  - Recomendações de melhoria

## Dependências
- Nenhuma (é a primeira tarefa)

## Critérios de Aceitação
- [ ] Documentação oficial consultada e sumarizada
- [ ] Todos os arquivos `.agent.md` validados
- [ ] Todos os arquivos `.instructions.md` validados (incluindo `applyTo`)
- [ ] Todos os arquivos `SKILL.md` validados
- [ ] Todos os arquivos `.prompt.md` validados
- [ ] `AGENTS.md` validado contra documentação oficial
- [ ] Relatório de validação criado com achados claros
