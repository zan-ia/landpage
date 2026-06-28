---
description: "Use when: working with pipeline agents, prompts, or skills — orchestrating development workflow, creating issues, branches, or PRs via agents. Defines the complete Plan→Implement→Review cycle with HITL, severity classification, and conventions."
applyTo: ".github/agents/**, .github/prompts/**, .github/skills/**"
---

# Development Pipeline — Workflow

This document defines the complete CI/CD development pipeline workflow managed by Copilot agents. The pipeline implements a **Plan → Implement → Review** cycle with **Human-in-the-Loop (HITL)** at critical points.

---

## Complete Flow

```
USER reports bug/feature/improvement
    │
    ▼
┌─────────────────────────────────────────────────┐
│ ORCHESTRATOR (main agent)                        │
│ ├─ Analyzes request → classifies type            │
│ ├─ Creates ISSUE on GitHub                       │
│ ├─ 🛑 HITL: User reviews and approves issue      │
│ ├─ Creates BRANCH: fix|feat|improve/short-desc   │
│ ├─ Invokes PLANNER (subagent)                    │
│ ├─ Invokes IMPLEMENTER (subagent)                │
│ ├─ Invokes REVIEWER (subagent)                   │
│ │   └─ Review loop (max. 3 iterations)           │
│ ├─ Commit + Push                                 │
│ ├─ Creates PR (with Closes #N)                   │
│ ├─ 🛑 HITL: User reviews and merges PR           │
│ └─ Checkout to main branch                       │
└─────────────────────────────────────────────────┘
```

---

## Pipeline Phases

### Phase 0: User Input

The user starts the pipeline through one of the prompts:
- `/iniciar-bugfix` — for bug fixes
- `/iniciar-feature` — for new features
- `/iniciar-melhoria` — for improvements and refactoring

The orchestrator **MUST** use `vscode_askQuestions` if the user's report is ambiguous or incomplete.

---

### Phase 1: Issue Creation

The orchestrator creates a GitHub issue with:

**Template for Bugs (`fix:`):**
```markdown
### Bug Description
[Clear and concise description]

### Steps to Reproduce
1. 
2. 
3. 

### Expected Behavior
[What should happen]

### Current Behavior
[What is happening]

### Environment
- OS: Windows 11
- Browser: Chrome
- Branch: main
```

**Template for Features (`feat:`):**
```markdown
### Motivation
[Why this feature is needed]

### Description
[What will be implemented]

### Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

### References
- [Links, docs, inspirations]
```

**Template for Improvements (`improve:`):**
```markdown
### Current Situation
[How it is today]

### Proposed Improvement
[What will be improved]

### Expected Benefits
[Positive impact of the change]

### Impact
- Affected components: [list]
- Risk: [low | medium | high]
```

🛑 **Mandatory HITL:** After creating the issue, the orchestrator MUST present the issue to the user and wait for explicit approval before proceeding. Use `vscode_askQuestions` with approve/reject/modify options.

---

### Phase 2: Branch Creation

After issue approval, the orchestrator creates a branch from `main`:

**Naming convention:**
| Type | Prefix | Example |
|------|---------|---------|
| Bug | `fix/` | `fix/fix-header-colors` |
| Feature | `feat/` | `feat/add-pricing-section` |
| Improvement | `improve/` | `improve/optimize-font-loading` |

Rules:
- Lowercase name, words separated by hyphens
- Max 50 characters
- Derived from the issue title
- ALWAYS create from `main` (clean and updated branch)

```bash
git checkout main
git pull origin main
git checkout -b fix/short-description
```

---

### Phase 3: Planning (subagent `planejador`)

The orchestrator invokes the `planejador` subagent with:
- Issue number and title
- Complete problem/request description
- Any additional relevant context

The planner:
1. Fetches the GitHub issue for complete context
2. Explores the codebase to identify relevant files
3. Consults `AGENTS.md`, `docs/INSTITUCIONAL.md`, and applicable instructions
4. Generates a detailed plan with: files to modify, patterns to follow, risks, implementation order
5. Saves to `.github/plans/issue-{N}-{slug}.md`
6. Returns: plan path + short summary (2-3 sentences) + complexity + impacted files

**Example planner return:**
```
📋 Plan: .github/plans/issue-42-fix-header-colors.md
📝 Summary: Fix header colors that diverge between DEV and LIVE. 
   Adjust 2 tokens in Header.svelte and verify glass-panel in app.css.
🔧 Complexity: Low
📁 Files: Header.svelte, app.css
```

The orchestrator saves this information to `/memories/session/pipeline-state.md`.

---

### Phase 4: Implementation (subagent `implementador`)

The orchestrator invokes the `implementador` subagent with:
- Plan file path
- Planner summary
- Impacted files

The implementer:
1. Reads the complete plan
2. Executes each step in order, making surgical changes
3. After each file, checks for errors with `get_errors`
4. Runs `npm run check` at the end
5. Runs `npm run build` at the end
6. NEVER modifies `build/` directly
7. Respects ALL conventions: scoped CSS, design tokens, BEM naming, no Tailwind
8. Returns: summary of what was implemented + list of modified files

---

### Phase 5: Review (subagent `revisor`)

The orchestrator invokes the `revisor` subagent with:
- Plan file path
- Implementation summary
- List of modified files

The reviewer analyzes the diff (`git diff`) against the plan and verifies:

**Quality Checklist:**
| Dimension | What to check |
|----------|----------------|
| **Code** | Scoped CSS, design tokens, BEM naming, Svelte 5 Runes, no Tailwind, no hex hardcoded |
| **Architecture** | Correct component composition, no layout breakage, correct imports |
| **Design** | Glass-panel applied, correct typography, MD3 palette, standardized badges and sections |
| **Readability** | Descriptive names, clean code, comments where needed |
| **Performance** | `will-change` only during interaction, `contain` where applicable, no `width`/`height` animations |
| **Maintainability** | Consistent patterns, token reuse, no duplication |
| **Specificity** | Low-specificity CSS, no `!important` |
| **Dependencies** | Correct Material Symbols, Google Fonts with swap+preconnect, no new CDN deps |
| **Tests** | `npm run check` without errors, clean `npm run build`, acceptance criteria met |
| **Accessibility** | ARIA labels, heading hierarchy, alt texts, `prefers-reduced-motion` |

---

### Phase 6: Review Decision

The reviewer classifies each issue found and returns:

```
📊 Review Report — Issue #N

Status: CHANGES_NEEDED

Issues Found:
🔴 CRITICAL (2):
  - Header.svelte: hardcoded color #1a1a2e instead of var(--color-surface)
  - app.css: animation uses 'height' instead of 'transform'

🟡 MAJOR (1):
  - New component doesn't use glass-panel

🟢 MINOR (2):
  - Comment in English (standard is Portuguese)
  - Variable could have a more descriptive name

Recommendation: RE-PLAN (2 critical issues)
```

**Severity Classification:**

| Level | Definition |
|-------|-----------|
| 🔴 **CRITICAL** | Functional bug, security, build breakage, severe architecture violation |
| 🟡 **MAJOR** | Pattern violation, inconsistent design, degraded performance |
| 🟢 **MINOR** | Style, documentation, naming, cosmetic improvements |

**Decision Matrix (4 Possible Statuses):**

| Report Status | Condition | Orchestrator Action |
|---------------------|----------|----------------------|
| **APPROVED** | No issues found | Proceed to Phase 7 (Commit/PR) |
| **CHANGES_NEEDED** | Only MINOR | Proceed to Phase 7; document follow-ups in PR body |
| **CHANGES_NEEDED** | CRITICAL or MAJOR present | Return to Phase 3 (Re-plan) |
| **REJECTED** | Severe structural issues (e.g., invalid approach) | Return to Phase 3 (Re-plan with new strategy) |

**Review loop rules:**
1. **Maximum 3 iterations** of the cycle (Phase 3 → 4 → 5 → 6)
2. If the 3rd iteration still has CRITICAL/MAJOR → document known risks in PR and proceed with caveats
3. The orchestrator tracks the iteration counter in `pipeline-state.md`

---

### Phase 7: Commit, Push and PR

**Commit messages (Conventional Commits):**
```
fix(Header): fix glass-panel colors to MD3 tokens

Adjusts backgroundColor and borderColor to use CSS variables
instead of hardcoded values. Fixes visual divergence with LIVE.

Closes #42
```

**PR Creation:**
- Title: same commit prefix (`fix:`, `feat:`, `improve:`)
- Body: summary of changes + `Closes #N`
- Base: `main` ← Compare: working branch

🛑 **Mandatory HITL:** After creating the PR, the orchestrator MUST present the PR to the user for final review. Do NOT merge automatically.

---

### Phase 8: Finalization

After the PR merge (done by the user):
```bash
git checkout main
git pull origin main
```

The orchestrator cleans up the pipeline state:
- Updates `/memories/session/pipeline-state.md` with status COMPLETED
- Optional: deletes the local branch (remote is deleted by GitHub on merge)

---

## State Tracking

The orchestrator maintains the `/memories/session/pipeline-state.md` file with:

```markdown
# Pipeline State — Issue #N

- **Issue:** [#N](url) — Title
- **Type:** bug | feature | improvement
- **Branch:** fix|feat|improve/name
- **Current Phase:** planning | implementation | review
- **Review Iteration:** X of 3
- **Plan:** .github/plans/issue-N-slug.md
- **Modified Files:** [list]
- **Status:** IN_PROGRESS | WAITING_HITL | COMPLETED
- **Next Step:** [description]
```

---

## Conventions Summary

| Convention | Standard |
|-----------|--------|
| **Branch** | `fix/`, `feat/`, `improve/` + lowercase slug |
| **Commit** | Conventional Commits: `fix:`, `feat:`, `improve:` |
| **PR** | Include `Closes #N` in body |
| **Issue** | Type-specific template (bug/feature/improvement) |
| **Plan** | `.github/plans/issue-{N}-{slug}.md` |
| **State** | `/memories/session/pipeline-state.md` |
| **Review** | Max. 3 iterations; critical/major → re-plan; minor → document |
| **HITL** | Mandatory after issue creation and after PR creation |
