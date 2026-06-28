# Tarefa 12 — Revisão Final e Pontos Não Cobertos do Harness

## Objetivo
Realizar uma revisão holística de TODO o harness Copilot após a conclusão das tarefas 09, 10 e 11, identificando pontos que possam ter escapado em todas as iterações anteriores — gaps de cobertura, inconsistências sutis, e riscos de manutenção futura.

## Responsabilidades Específicas

### 1. Verificar cobertura completa do harness

Mapear cada uma das **7 primitivas** do harness contra a implementação atual:

| Primitiva | Arquivo(s) | Status Esperado | Verificar |
|-----------|-----------|-----------------|-----------|
| Agent Instructions | `AGENTS.md` | ✅ Task 06 concluída | Atualizado? Linhas? |
| File Instructions | `.github/instructions/*.instructions.md` | 9 arquivos | Todos com `applyTo` correto? `description` semântico? |
| Custom Agents | `.github/agents/*.agent.md` | 7 agentes | Frontmatter consistente? (Task 11) |
| Prompts | `.github/prompts/*.prompt.md` | 7 prompts | Todos funcionais? Completos? |
| Skills | `.github/skills/*/SKILL.md` | 6 skills | SKILL.md pipeline atualizado? (Task 09) |
| Hooks | `.github/hooks/` | ❌ Diretório não existe | Necessário? Ou documentar que é futuro? |
| MCP Servers | `.vscode/` settings | — | Fora do escopo do harness local |

### 2. Verificar arquivos de prompt (7 prompts)

Ler cada `.prompt.md` e verificar:
- [ ] Conteúdo não está vazio ou placeholder
- [ ] Instruções são claras e acionáveis
- [ ] Referenciam o fluxo correto do pipeline
- [ ] Não contêm informações obsoletas (ex: referência ao Tailwind)

Arquivos:
- `iniciar-bugfix.prompt.md`
- `iniciar-feature.prompt.md`
- `iniciar-melhoria.prompt.md`
- `adicionar-depoimento.prompt.md`
- `adicionar-servico.prompt.md`
- `otimizar-seo.prompt.md`
- `revisar.prompt.md`

### 3. Verificar skills (6 skills)

Ler cada `SKILL.md` e verificar:
- [ ] Frontmatter YAML válido
- [ ] `description` semântica e acionável
- [ ] Não duplica informação que já está em instructions
- [ ] Referencia as instructions corretas

Skills:
- `pipeline-orquestracao` (atualizado na Task 09)
- `criar-pagina-institucional`
- `criar-section`
- `css-comparison-workflow`
- `otimizar-imagens`
- `seo-otimization`

### 4. Verificar consistency de nomenclatura

| Elemento | Padrão | Verificar |
|----------|--------|-----------|
| Nomes de agentes | kebab-case | ✅ |
| Nomes de arquivos | `.agent.md`, `.instructions.md`, `.prompt.md` | ✅ |
| Slash commands | `/iniciar-*`, `/adicionar-*`, `/otimizar-*`, `/revisar` | Todos mapeados? |
| Design tokens | `--color-*`, `--font-*`, `--spacing-*`, `--radius-*` | Todos os docs referenciam? |

### 5. Verificar riscos de manutenção futura

- [ ] Se um novo componente Svelte for adicionado, quantos arquivos precisam ser atualizados?
- [ ] Se uma nova convenção de código for adotada, onde documentar?
- [ ] Se um novo agente for criado, qual o checklist de integração?
- [ ] O `AGENTS.md` tem um "guia de contribuição" para quem mantém o harness?

### 6. Verificar integridade de links internos

- [ ] Todos os `../` e `./` em links Markdown estão corretos
- [ ] Referências a `AGENTS.md`, `docs/INSTITUCIONAL.md` funcionam
- [ ] Nenhum link quebrado para arquivos renomeados/movidos

### 7. Verificar cobertura de edge cases do pipeline

O pipeline cobre estes cenários?
- [ ] Usuário cancela no meio do fluxo (após HITL rejeitar)
- [ ] Conflito de merge na branch
- [ ] Build falha na Fase 4 (implementador)
- [ ] Issue já existe (duplicada)
- [ ] PR já existe para a mesma issue
- [ ] Múltiplos desenvolvedores trabalhando em paralelo

## Artefato de Saída
- **Relatório de revisão** (`docs/harness-validation-report.md` atualizado ou novo arquivo `docs/harness-final-review.md`)
- Lista de **issues/riscos** encontrados (para criar tasks futuras se necessário)
- **Checklist de verificação** preenchido (o próprio relatório)

## Dependências
- **Tasks 09, 10, 11** — devem estar concluídas antes desta revisão
- **Todas as tasks anteriores** — esta é a revisão final do harness completo

## Critérios de Aceitação
- [ ] 7 primitivas do harness verificadas (cobertura)
- [ ] 7 prompts lidos e validados
- [ ] 6 skills lidos e validados
- [ ] Consistência de nomenclatura verificada
- [ ] Riscos de manutenção documentados
- [ ] Links internos verificados (sem broken links)
- [ ] Edge cases do pipeline documentados
- [ ] Relatório final claro e acionável (o que está OK, o que precisa de atenção)
