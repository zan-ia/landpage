---
name: pipeline-orquestracao
description: "Orchestrates the complete Zan.IA landing page development pipeline: Plan→Implement→Review with HITL. Use when: initiating any development task (bugfix, feature, improvement) that needs to go through the complete quality cycle — issue creation, planning, implementation, review, and PR. Also use when mentioning: pipeline, bugfix, fix bug, new feature, implement functionality, improvement, refactoring, workflow, orchestration, automated code review."
argument-hint: "[bugfix | feature | improvement] — describe the task..."
user-invocable: true
disable-model-invocation: false
---

# Orchestration Pipeline — Zan.IA

CI/CD development pipeline orchestration skill managed by Copilot agents.

## Overview

The pipeline implements a **Plan → Implement → Review** cycle with **Human-in-the-Loop (HITL)** at critical points (issue approval, PR review). The pipeline consists of **4 pipeline agents** (`orquestrador`, `planejador`, `implementador`, `revisor`) and **3 support agents** (`criador-conteudo`, `performance-auditor`, `refactor-css`).

```
USER → /iniciar-bugfix|feature|improvement
    │
    ▼
ORCHESTRATOR
    ├─ (Phase 0) User input → classifies type
    ├─ (Phase 1) Creates Issue → 🛑 HITL
    ├─ (Phase 2) Creates Branch (fix|feat|improve/...)
    ├─ (Phase 3) PLANNER (subagent) → Plan in .github/plans/
    ├─ (Phase 4) IMPLEMENTER (subagent) → Code + Build
    ├─ (Phase 5) REVIEWER (subagent) → Quality report
    ├─ (Phase 6) Decision → loop max. 3x if critical/major
    ├─ (Phase 7) Commit (Conventional) + Push + PR (Closes #N) → 🛑 HITL
    └─ (Phase 8) Checkout main
```

---

## How to Use

### Via Slash Command
Type `/` in chat and select one of the prompts:
- `/iniciar-bugfix` — for bug fixes
- `/iniciar-feature` — for new features
- `/iniciar-melhoria` — for improvements and refactoring

### Via Direct Mention
Mention the task type and description:
- "Fix the header colors bug on mobile"
- "Add a testimonials section to the page"
- "Improve Google Fonts loading"

The skill will be loaded automatically and the pipeline flow will be initiated.

---

## Pipeline Agents

### Pipeline Agents

| Agent | Role | Tools |
|--------|-------|-------------|
| `orquestrador` | Coordinator — manages the complete flow | All |
| `planejador` | Analyst — explores codebase and creates plan | read, search, web |
| `implementador` | Developer — executes the plan | read, search, edit, execute |
| `revisor` | QA — analyzes diff and verifies quality | read, search |

### Support Agents

| Agent | Role | Tools |
|--------|-------|-------------|
| `criador-conteudo` | Generates and updates institutional content | read, search |
| `performance-auditor` | Audits Core Web Vitals and performance | read, search, browser |
| `refactor-css` | Refactors scoped CSS and audits tokens | read, search, edit |

---

## Conventions

### Branches
| Type | Prefix | Example |
|------|---------|---------|
| Bugfix | `fix/` | `fix/fix-header-colors` |
| Feature | `feat/` | `feat/add-pricing-section` |
| Improvement | `improve/` | `improve/optimize-fonts` |

### Commits (Conventional Commits)
```
fix(Header): fix glass-panel colors
feat(Solutions): add pricing section
improve(Testimonials): optimize carousel

Closes #N
```

### Issues
- Title with prefix: `fix:`, `feat:`, `improve:`
- Type-specific template (bug/feature/improvement)
- `Closes #N` in PR for auto-close

---

## Quality Checklist (Reviewer)

> See `.github/agents/revisor.agent.md` for the detailed checklist with sub-checks per dimension.

| Dimension | Verifies |
|----------|----------|
| Code | Scoped CSS, design tokens, BEM, Svelte 5 Runes, no Tailwind, no hex |
| Architecture | Correct composition, imports, layout intact |
| Design | Glass-panel, typography (Space Grotesk/Geist/JetBrains Mono), MD3 palette |
| Readability | Descriptive names, clean code, Portuguese |
| Performance | `will-change` only during interaction, `contain`, `transform`/`opacity` animations |
| Maintainability | Consistent patterns, token reuse, no duplication |
| Specificity | Low, no `!important`, no ID selectors |
| Dependencies | Material Symbols + Google Fonts only, no new CDN |
| Tests | `npm run check` and `npm run build` pass, issue criteria met |
| Accessibility | ARIA labels, heading hierarchy, alt texts, reduced motion |

---

## HITL Points (Human-in-the-Loop)

🛑 The pipeline ALWAYS stops and waits for the user at:
1. **After issue creation** — user reviews title, description, and scope
2. **After PR creation** — user reviews diff, reviewer comments, and merges

---

## Review Loop Rules

- **CRITICAL** issues (functional bug, broken build) → re-plan
- **MAJOR** issues (pattern violation, inconsistent design) → re-plan
- **MINOR** issues (style, naming) → document in PR as follow-up
- **Maximum 3 iterations** — if it fails, document risks and force PR with caveats

---

## Plan Files

Plans are saved to `.github/plans/issue-{N}-{slug}.md` with:
- Approach summary
- Files to modify/create
- Patterns to follow
- Implementation order
- Identified risks
- Verification checklist

---

## References

- **Detailed pipeline workflow:** `.github/instructions/pipeline-workflow.instructions.md`
- **Tool usage rules:** `.github/instructions/tool-usage.instructions.md`
- **Code conventions:** `AGENTS.md`
- **Design system:** `src/lib/app.css`
- **Institutional information:** `docs/INSTITUCIONAL.md`

### Entry Prompts
- `/iniciar-bugfix` — `.github/prompts/iniciar-bugfix.prompt.md`
- `/iniciar-feature` — `.github/prompts/iniciar-feature.prompt.md`
- `/iniciar-melhoria` — `.github/prompts/iniciar-melhoria.prompt.md`

### Pipeline Agents
- `orquestrador` — `.github/agents/orquestrador.agent.md`
- `planejador` — `.github/agents/planejador.agent.md`
- `implementador` — `.github/agents/implementador.agent.md`
- `revisor` — `.github/agents/revisor.agent.md`

### Support Agents
- `criador-conteudo` — `.github/agents/criador-conteudo.agent.md`
- `performance-auditor` — `.github/agents/performance-auditor.agent.md`
- `refactor-css` — `.github/agents/refactor-css.agent.md`

### Related Skills
- `criar-section` — create new sections
- `criar-pagina-institucional` — create institutional pages
- `css-comparison-workflow` — compare DEV vs LIVE
- `otimizar-imagens` — optimize images
- `seo-otimization` — technical SEO knowledge (meta tags, structured data)

### Direct Action Prompts
- `/adicionar-depoimento` — `.github/prompts/adicionar-depoimento.prompt.md`
- `/adicionar-servico` — `.github/prompts/adicionar-servico.prompt.md`
- `/otimizar-seo` — `.github/prompts/otimizar-seo.prompt.md`
- `/revisar` — `.github/prompts/revisar.prompt.md`

---

## Related Documentation
- Detailed pipeline: `.github/instructions/pipeline-workflow.instructions.md`
- Review criteria: `.github/agents/revisor.agent.md`
- Agent guide: `AGENTS.md` (Agent Orchestration section)
