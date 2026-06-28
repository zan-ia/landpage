# Tarefa 02 — Definição dos Agentes do Pipeline

## Objetivo
Criar/validar as definições dos agentes especializados do pipeline, garantindo que cada um tenha responsabilidades claras, ferramentas corretas, constraints bem definidas e interaja corretamente com os demais.

## Responsabilidades Específicas

1. **Definir/validar o `orquestrador`** (agente principal):
   - Responsável por coordenar o fluxo completo: issue → branch → planejar → implementar → revisar → commit → PR
   - Ferramentas necessárias: read, search, edit, execute, web, browser, subagents (planejador, implementador, revisor)
   - Constraints: NUNCA merge automático, SEMPRE HITL, máx. 3 iterações de revisão
   - Rastrear estado em `/memories/session/pipeline-state.md`

2. **Definir/validar o `planejador`** (subagente):
   - Responsável por analisar issues + codebase e gerar plano detalhado
   - Ferramentas: read, search, web (pesquisa de documentação)
   - Output: path do plano + resumo 2-3 frases + complexidade + lista de arquivos
   - Salvar plano em `.github/plans/issue-{N}-{slug}.md`

3. **Definir/validar o `implementador`** (subagente):
   - Responsável por executar o plano com alterações cirúrgicas
   - Ferramentas: read, search, edit, execute (npm run check, npm run build)
   - Constraints: NUNCA modificar `build/`, verificar erros após cada arquivo
   - Output: resumo do implementado + lista de arquivos modificados + erros

4. **Definir/validar o `revisor`** (subagente):
   - Responsável por analisar diff vs plano e verificar qualidade
   - Ferramentas: read, search
   - Verificar 10 dimensões de qualidade (detalhadas na Tarefa 08)
   - Output: status (APROVADO|ALTERACOES|REJEITADO) + issues classificados + recomendação

5. **Definir/validar agentes auxiliares**:
   - `criador-conteudo` — geração de conteúdo institucional
   - `performance-auditor` — auditoria de performance e Core Web Vitals
   - `refactor-css` — refatoração e otimização de CSS scoped
   - `Explore` — exploração read-only da codebase

6. **Garantir que cada agente**:
   - Tenha YAML frontmatter correto (`name`, `description`, `tools`, `agents`, `user-invocable`, `disable-model-invocation`)
   - Tenha descrição CLARA de quando deve ser invocado
   - Liste explicitamente as ferramentas permitidas
   - Declare subagentes que pode invocar
   - Defina constraints e regras de ouro

## Artefato de Saída
- 7 arquivos `.agent.md` criados/atualizados em `.github/agents/`

## Dependências
- **Tarefa 01** (pesquisa e validação) — para conhecer o formato correto

## Critérios de Aceitação
- [ ] `orquestrador.agent.md` — fluxo completo, HITL, constraints
- [ ] `planejador.agent.md` — análise + geração de plano com output padronizado
- [ ] `implementador.agent.md` — execução cirúrgica + verificação de build
- [ ] `revisor.agent.md` — 10 dimensões + classificação critical/major/minor
- [ ] `criador-conteudo.agent.md` — consulta docs/INSTITUCIONAL.md
- [ ] `performance-auditor.agent.md` — Core Web Vitals + sugestões
- [ ] `refactor-css.agent.md` — extração de padrões + tokens
- [ ] Cada agente tem `user-invocable: true` ou `false` conforme apropriado
- [ ] `disable-model-invocation` definido corretamente para cada um
