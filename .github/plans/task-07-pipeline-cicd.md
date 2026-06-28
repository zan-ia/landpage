# Tarefa 07 — Pipeline CI/CD Completo (Plan → Implement → Review)

## Objetivo
Projetar e documentar o fluxo completo do pipeline de desenvolvimento orientado por agentes, cobrindo desde o reporte do usuário até o merge do PR, com HITL em pontos críticos e ciclo de revisão com qualidade garantida.

## Responsabilidades Específicas

### 1. Fluxo Principal (8 Fases)

**Fase 0 — Entrada do Usuário:**
- Usuário inicia via `/iniciar-bugfix`, `/iniciar-feature`, ou `/iniciar-melhoria`
- Orquestrador classifica a solicitação (bug | feature | melhoria)
- Se ambíguo: usar `vscode_askQuestions` obrigatoriamente

**Fase 1 — Criação da Issue:**
- Criar issue no GitHub com template específico por tipo
- Template bug: descrição, passos para reproduzir, comportamento esperado/atual, ambiente
- Template feature: motivação, descrição, critérios de aceitação, referências
- Template melhoria: situação atual, melhoria proposta, benefícios, impacto
- 🛑 **HITL**: apresentar issue ao usuário e aguardar aprovação explícita

**Fase 2 — Criação da Branch:**
- A partir de `main` (git checkout main && git pull && git checkout -b)
- Convenção: `fix/`, `feat/`, `improve/` + slug em minúsculas (máx. 50 chars)
- Nome derivado do título da issue

**Fase 3 — Planejamento:**
- Invocar subagente `planejador` com: nº da issue, título, descrição completa
- Planejador: busca issue → explora codebase → consulta docs → gera plano
- Output padronizado: path do plano + resumo (2-3 frases) + complexidade + arquivos
- Salvar estado em `/memories/session/pipeline-state.md`

**Fase 4 — Implementação:**
- Invocar subagente `implementador` com: path do plano + resumo + arquivos
- Implementador: lê plano → executa passos em ordem → verifica erros → build
- Output: resumo + lista de arquivos modificados + erros

**Fase 5 — Revisão:**
- Invocar subagente `revisor` com: path do plano + resumo implementação + arquivos
- Revisor: analisa `git diff` contra o plano usando 10 dimensões de qualidade
- Output: status + issues classificados + recomendação

**Fase 6 — Decisão:**
- APROVADO → prosseguir para commit/PR
- ALTERACOES (só MINOR) → prosseguir, documentar no PR
- ALTERACOES (CRITICAL/MAJOR) → re-planejar (volta Fase 3)
- REJEITADO → re-planejar (volta Fase 3)
- Máximo 3 iterações do ciclo (Fase 3→4→5→6)

**Fase 7 — Commit, Push e PR:**
- Commit: Conventional Commits (`fix:`, `feat:`, `improve:`)
- Mensagem: tipo(escopo): descrição curta + corpo + Closes #N
- PR: mesmo título do commit, corpo com resumo + Closes #N
- Base: main ← Compare: branch de trabalho
- 🛑 **HITL**: apresentar PR ao usuário, NUNCA merge automático

**Fase 8 — Finalização:**
- Após merge (feito pelo usuário): checkout main + pull
- Atualizar pipeline-state.md com FINALIZADO
- Opcional: deletar branch local

### 2. Rastreamento de Estado

Arquivo `/memories/session/pipeline-state.md` mantido pelo orquestrador:
```markdown
- Issue: #N — título
- Tipo: bug | feature | melhoria
- Branch: fix|feat|improve/nome
- Fase Atual: planejamento | implementação | revisão
- Iteração: X de 3
- Plano: .github/plans/issue-N-slug.md
- Arquivos Modificados: [lista]
- Status: EM_ANDAMENTO | AGUARDANDO_HITL | FINALIZADO
```

### 3. Convenções do Pipeline

| Convenção | Padrão |
|-----------|--------|
| Branch | `fix/`, `feat/`, `improve/` + slug minúsculas |
| Commit | Conventional Commits |
| PR | Incluir `Closes #N` |
| Issue | Template por tipo |
| Plano | `.github/plans/issue-{N}-{slug}.md` |
| Estado | `/memories/session/pipeline-state.md` |
| Revisão | Máx. 3 iterações |
| HITL | Issue + PR |

## Artefato de Saída
- `.github/instructions/pipeline-workflow.instructions.md` criado/atualizado

## Dependências
- **Tarefa 02** (agentes) — agentes que executam cada fase
- **Tarefa 08** (critérios de revisão) — dimensões de qualidade da Fase 5

## Critérios de Aceitação
- [ ] 8 fases documentadas com entradas, processos e saídas claras
- [ ] HITL em 2 pontos obrigatórios (issue e PR)
- [ ] Templates de issue para os 3 tipos
- [ ] Convenções de branch, commit, PR documentadas
- [ ] Loop de revisão com máximo de 3 iterações
- [ ] Rastreamento de estado definido
- [ ] Diagrama de fluxo (ASCII ou Mermaid) incluído
- [ ] Regras de decisão da Fase 6 claras e sem ambiguidade
