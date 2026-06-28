---
description: "Use when: working with pipeline agents, prompts, or skills — orchestrating development workflow, creating issues, branches, or PRs via agents. Defines the complete Plan→Implement→Review cycle with HITL, severity classification, and conventions."
applyTo:
  - ".github/agents/**"
  - ".github/prompts/**"
  - ".github/skills/**"
---

# Pipeline de Desenvolvimento — Fluxo de Trabalho

Este documento define o fluxo completo do pipeline de desenvolvimento CI/CD gerenciado por agentes do Copilot. O pipeline implementa um ciclo **Plan → Implement → Review** com **Human-in-the-Loop (HITL)** em pontos críticos.

---

## Fluxo Completo

```
USUÁRIO reporta bug/feature/melhoria
    │
    ▼
┌─────────────────────────────────────────────────┐
│ ORQUESTRADOR (agente principal)                  │
│ ├─ Analisa solicitação → classifica tipo         │
│ ├─ Cria ISSUE no GitHub                          │
│ ├─ 🛑 HITL: Usuário revisa e aprova a issue      │
│ ├─ Cria BRANCH: fix|feat|improve/descricao-curta │
│ ├─ Invoca PLANEJADOR (subagente)                 │
│ ├─ Invoca IMPLEMENTADOR (subagente)              │
│ ├─ Invoca REVISOR (subagente)                    │
│ │   └─ Loop de revisão (máx. 3 iterações)       │
│ ├─ Commit + Push                                 │
│ ├─ Cria PR (com Closes #N)                       │
│ ├─ 🛑 HITL: Usuário revisa e mergeia o PR        │
│ └─ Checkout para branch main                     │
└─────────────────────────────────────────────────┘
```

---

## Fases do Pipeline

### Fase 0: Entrada do Usuário

O usuário inicia o pipeline através de um dos prompts:
- `/iniciar-bugfix` — para correção de bugs
- `/iniciar-feature` — para novas funcionalidades
- `/iniciar-melhoria` — para melhorias e refatorações

O orquestrador **DEVE** usar `vscode_askQuestions` se o relato do usuário for ambíguo ou incompleto.

---

### Fase 1: Criação da Issue

O orquestrador cria uma issue no GitHub com:

**Template para Bugs (`fix:`):**
```markdown
### Descrição do Bug
[Descrição clara e concisa]

### Passos para Reproduzir
1. 
2. 
3. 

### Comportamento Esperado
[O que deveria acontecer]

### Comportamento Atual
[O que está acontecendo]

### Ambiente
- SO: Windows 11
- Navegador: Chrome
- Branch: main
```

**Template para Features (`feat:`):**
```markdown
### Motivação
[Por que esta feature é necessária]

### Descrição
[O que será implementado]

### Critérios de Aceitação
- [ ] Critério 1
- [ ] Critério 2

### Referências
- [Links, docs, inspirações]
```

**Template para Melhorias (`improve:`):**
```markdown
### Situação Atual
[Como está hoje]

### Melhoria Proposta
[O que será melhorado]

### Benefícios Esperados
[Impacto positivo da mudança]

### Impacto
- Componentes afetados: [lista]
- Risco: [baixo | médio | alto]
```

🛑 **HITL obrigatório:** Após criar a issue, o orquestrador DEVE apresentar a issue ao usuário e aguardar aprovação explícita antes de prosseguir. Use `vscode_askQuestions` com opção de aprovar/rejeitar/modificar.

---

### Fase 2: Criação da Branch

Após aprovação da issue, o orquestrador cria uma branch a partir de `main`:

**Convenção de nomenclatura:**
| Tipo | Prefixo | Exemplo |
|------|---------|---------|
| Bug | `fix/` | `fix/corrigir-cores-header` |
| Feature | `feat/` | `feat/adicionar-secao-precos` |
| Melhoria | `improve/` | `improve/otimizar-carregamento-fontes` |

Regras:
- Nome em minúsculas, palavras separadas por hífen
- Máximo 50 caracteres
- Derivado do título da issue
- SEMPRE criar a partir de `main` (branch limpa e atualizada)

```bash
git checkout main
git pull origin main
git checkout -b fix/descricao-curta
```

---

### Fase 3: Planejamento (subagente `planejador`)

O orquestrador invoca o subagente `planejador` com:
- Número e título da issue
- Descrição completa do problema/solicitação
- Contexto adicional relevante

O planejador:
1. Busca a issue no GitHub para contexto completo
2. Explora a codebase para identificar arquivos relevantes
3. Consulta `AGENTS.md`, `docs/INSTITUCIONAL.md`, e instructions aplicáveis
4. Gera plano detalhado com: arquivos a modificar, padrões a seguir, riscos, ordem de implementação
5. Salva em `.github/plans/issue-{N}-{slug}.md`
6. Retorna: path do plano + resumo curto (2-3 frases) + complexidade + arquivos impactados

**Exemplo de retorno do planejador:**
```
📋 Plano: .github/plans/issue-42-fix-header-colors.md
📝 Resumo: Corrigir cores do header que divergem entre DEV e LIVE. 
   Ajustar 2 tokens em Header.svelte e verificar glass-panel em app.css.
🔧 Complexidade: Baixa
📁 Arquivos: Header.svelte, app.css
```

O orquestrador salva estas informações em `/memories/session/pipeline-state.md`.

---

### Fase 4: Implementação (subagente `implementador`)

O orquestrador invoca o subagente `implementador` com:
- Path do arquivo de plano
- Resumo do planejador
- Arquivos impactados

O implementador:
1. Lê o plano completo
2. Executa cada passo em ordem, fazendo alterações cirúrgicas
3. Após cada arquivo, verifica erros com `get_errors`
4. Executa `npm run check` ao final
5. Executa `npm run build` ao final
6. NUNCA modifica `build/` diretamente
7. Respeita TODAS as convenções: scoped CSS, design tokens, BEM naming, sem Tailwind
8. Retorna: resumo do que foi implementado + lista de arquivos modificados

---

### Fase 5: Revisão (subagente `revisor`)

O orquestrador invoca o subagente `revisor` com:
- Path do arquivo de plano
- Resumo da implementação
- Lista de arquivos modificados

O revisor analisa o diff (`git diff`) contra o plano e verifica:

**Checklist de Qualidade:**
| Dimensão | O que verificar |
|----------|----------------|
| **Código** | Scoped CSS, design tokens, BEM naming, Svelte 5 Runes, sem Tailwind, sem hex hardcoded |
| **Arquitetura** | Composição correta de componentes, sem quebrar layout, imports corretos |
| **Design** | Glass-panel aplicado, tipografia correta, paleta MD3, badges e seções padronizados |
| **Legibilidade** | Nomes descritivos, código limpo, comentários onde necessário |
| **Performance** | `will-change` só durante interação, `contain` onde aplicável, sem animações de `width`/`height` |
| **Manutenibilidade** | Padrões consistentes, reutilização de tokens, sem duplicação |
| **Especificidade** | CSS com baixa especificidade, sem `!important` |
| **Dependências** | Material Symbols correto, Google Fonts com swap+preconnect, sem novas deps CDN |
| **Testes** | `npm run check` sem erros, `npm run build` limpo, critérios de aceitação atendidos |
| **Acessibilidade** | ARIA labels, heading hierarchy, alt texts, `prefers-reduced-motion` |

---

### Fase 6: Decisão de Revisão

O revisor classifica cada issue encontrado e retorna:

```
📊 Relatório de Revisão — Issue #N

Status: ALTERACOES

Issues Encontrados:
🔴 CRITICAL (2):
  - Header.svelte: cor hardcoded #1a1a2e em vez de var(--color-surface)
  - app.css: animação usa 'height' em vez de 'transform'

🟡 MAJOR (1):
  - Novo componente não usa glass-panel

🟢 MINOR (2):
  - Comentário em inglês (padrão é português)
  - Variável poderia ter nome mais descritivo

Recomendação: RE-PLANEJAR (2 issues críticos)
```

**Classificação de Severidade:**

| Nível | Definição |
|-------|-----------|
| 🔴 **CRITICAL** | Bug funcional, segurança, quebra de build, violação grave de arquitetura |
| 🟡 **MAJOR** | Violação de padrão, design inconsistente, performance degradada |
| 🟢 **MINOR** | Estilo, documentação, naming, melhorias cosméticas |

**Matriz de Decisão (4 Status Possíveis):**

| Status do Relatório | Condição | Ação do Orquestrador |
|---------------------|----------|----------------------|
| **APROVADO** | Nenhum issue encontrado | Prosseguir para Fase 7 (Commit/PR) |
| **ALTERACOES** | Apenas MINOR | Prosseguir para Fase 7; documentar follow-ups no corpo do PR |
| **ALTERACOES** | CRITICAL ou MAJOR presente | Voltar para Fase 3 (Re-planejar) |
| **REJEITADO** | Issues estruturais graves (ex.: abordagem inválida) | Voltar para Fase 3 (Re-planejar com nova estratégia) |

**Regras do loop de revisão:**
1. **Máximo 3 iterações** do ciclo (Fase 3 → 4 → 5 → 6)
2. Se a 3ª iteração ainda tiver CRITICAL/MAJOR → documentar riscos conhecidos no PR e prosseguir com ressalvas
3. O orquestrador controla o contador de iterações em `pipeline-state.md`

---

### Fase 7: Commit, Push e PR

**Mensagens de commit (Conventional Commits):**
```
fix(Header): corrigir cores do glass-panel para tokens MD3

Ajusta backgroundColor e borderColor para usar variáveis CSS
em vez de valores hardcoded. Corrige divergência visual com LIVE.

Closes #42
```

**Criação do PR:**
- Título: mesmo prefixo do commit (`fix:`, `feat:`, `improve:`)
- Corpo: resumo das mudanças + `Closes #N`
- Base: `main` ← Compare: branch de trabalho

🛑 **HITL obrigatório:** Após criar o PR, o orquestrador DEVE apresentar o PR ao usuário para revisão final. NÃO fazer merge automaticamente.

---

### Fase 8: Finalização

Após o merge do PR (feito pelo usuário):
```bash
git checkout main
git pull origin main
```

O orquestrador limpa o estado do pipeline:
- Atualiza `/memories/session/pipeline-state.md` com status FINALIZADO
- Opcional: deleta a branch local (a remota é deletada pelo GitHub no merge)

---

## Rastreamento de Estado

O orquestrador mantém o arquivo `/memories/session/pipeline-state.md` com:

```markdown
# Pipeline State — Issue #N

- **Issue:** [#N](url) — Título
- **Tipo:** bug | feature | melhoria
- **Branch:** fix|feat|improve/nome
- **Fase Atual:** planejamento | implementação | revisão
- **Iteração de Revisão:** X de 3
- **Plano:** .github/plans/issue-N-slug.md
- **Arquivos Modificados:** [lista]
- **Status:** EM_ANDAMENTO | AGUARDANDO_HITL | FINALIZADO
- **Próximo Passo:** [descrição]
```

---

## Resumo de Convenções

| Convenção | Padrão |
|-----------|--------|
| **Branch** | `fix/`, `feat/`, `improve/` + slug em minúsculas |
| **Commit** | Conventional Commits: `fix:`, `feat:`, `improve:` |
| **PR** | Incluir `Closes #N` no corpo |
| **Issue** | Template específico por tipo (bug/feature/melhoria) |
| **Plano** | `.github/plans/issue-{N}-{slug}.md` |
| **Estado** | `/memories/session/pipeline-state.md` |
| **Revisão** | Máx. 3 iterações; critical/major → re-planeja; minor → documenta |
| **HITL** | Obrigatório após criação da issue e após criação do PR |
