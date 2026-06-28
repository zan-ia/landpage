# Institutional Landing Page — Agent Guidelines

## Project Overview

Institutional landing page.

> See [`docs/INSTITUCIONAL.md`](./docs/INSTITUCIONAL.md) for complete company overview, services, and differentiators. This document is the official source of project information.

## Tech Stack

| Layer | Technology |
|--------|-----------|
| **Framework** | SvelteKit 5 (Runes mode) |
| **Build** | Vite + `@sveltejs/adapter-static` |
| **Markup** | Svelte components with scoped CSS |
| **Styles** | Scoped `<style>` per component + global `app.css` (design tokens) |
| **Icons** | Google Material Symbols Outlined |
| **Typography** | Space Grotesk, Geist, JetBrains Mono (Google Fonts) |
| **Theme** | Dark mode (Material Design 3) |
| **Deploy** | GitHub Pages + GitHub Actions |

## Architecture

- **SvelteKit 5** with Runes mode (reactivity via `$state`, `$effect`, `$props`)
- **Static site generation** via `@sveltejs/adapter-static` — output in `build/`
- **Native scoped CSS:** each Svelte component has its own `<style>`, no class conflicts
- **Single global CSS:** `src/lib/app.css` with design tokens (CSS variables), reset, and shared utilities (glass-panel, animations)
- **No Tailwind CSS:** utilities replaced by scoped CSS classes + design token variables

## Build & Deploy

```bash
npm run dev      # Development at localhost:5173
npm run build    # Generates build/ with static output
npm run preview  # Build preview locally
# Automatic deploy via GitHub Actions on push to main branch
# workflow: .github/workflows/deploy.yml
# Served from: ./build/
```

## File Structure

```
[project-name]/
├── src/
│   ├── lib/
│   │   ├── components/        # Svelte components with scoped <style>
│   │   │   ├── Header.svelte
│   │   │   ├── Hero.svelte
│   │   │   ├── Authority.svelte
│   │   │   ├── Solutions.svelte
│   │   │   ├── Differential.svelte
│   │   │   ├── Testimonials.svelte
│   │   │   ├── CTA.svelte
│   │   │   └── Footer.svelte
│   │   └── app.css            # Global CSS (design tokens + reset + utilities)
│   ├── routes/
│   │   ├── +layout.svelte     # Main layout (Header + Footer)
│   │   ├── +layout.js         # Config: prerender = true
│   │   └── +page.svelte       # Home page (assembles all components)
│   └── app.html               # HTML template (fonts, meta)
├── static/
│   └── assets/images/         # Local images
├── build/                     # Build output (generated, do not version)
├── .github/
│   ├── copilot-instructions.md  # Critical rules (askQuestions + todo)
│   ├── ISSUES.md
│   ├── agents/              # Specialized agents
│   │   ├── criador-conteudo.agent.md
│   │   ├── engenheiro-de-harness.agent.md
│   │   ├── implementador.agent.md
│   │   ├── orquestrador.agent.md
│   │   ├── performance-auditor.agent.md
│   │   ├── planejador.agent.md
│   │   ├── refactor-css.agent.md
│   │   └── revisor.agent.md
│   ├── instructions/         # Automatic rules (applyTo)
│   │   ├── css.instructions.md
│   │   ├── deploy.instructions.md
│   │   ├── html.instructions.md
│   │   ├── pipeline-workflow.instructions.md
│   │   ├── project-organization.instructions.md
│   │   ├── style-architecture.instructions.md
│   │   ├── svelte.instructions.md
│   │   ├── tool-usage.instructions.md
│   │   └── typescript.instructions.md
│   ├── hooks/                # Lifecycle hooks
│   │   └── validate-permissions.json
│   ├── prompts/              # Custom commands
│   │   ├── adicionar-depoimento.prompt.md
│   │   ├── adicionar-servico.prompt.md
│   │   ├── iniciar-bugfix.prompt.md
│   │   ├── iniciar-feature.prompt.md
│   │   ├── iniciar-melhoria.prompt.md
│   │   ├── otimizar-seo.prompt.md
│   │   └── revisar.prompt.md
│   ├── skills/               # Specialized knowledge
│   │   ├── criar-pagina-institucional/SKILL.md
│   │   ├── criar-section/SKILL.md
│   │   ├── css-comparison-workflow/SKILL.md
│   │   ├── otimizar-imagens/SKILL.md
│   │   ├── harness-engineering-reference/SKILL.md
│   │   ├── pipeline-orquestracao/SKILL.md
│   │   └── seo-otimization/SKILL.md
│   ├── plans/                # Implementation plans
│   │   └── README.md
│   └── workflows/
│       └── deploy.yml        # Build + Deploy GitHub Pages
├── svelte.config.js
├── vite.config.ts
├── package.json
└── AGENTS.md
```

## Code Conventions

- **Svelte Components:** Use Runes mode (`$state()`, `$effect()`, `$props()`)
- **Scoped CSS:** Each component has its own `<style>` — no global classes, no conflicts
- **Design Tokens:** Use CSS variables `--color-*`, `--font-*`, `--spacing-*` from `app.css`
- **Animations:** `@keyframes` defined in `app.css`; use `.animate-*` classes when needed
- **Icons:** `<span class="material-symbols-outlined">icon_name</span>`
- **Images:** Reference as `/assets/images/name.ext` (mapped from `static/assets/images/`)
- **Responsiveness:** Mobile breakpoint at 768px via media queries in components
- **Glass panels:** Global class `.glass-panel` with backdrop-filter and subtle border

## GitHub Workflow (Issues & PRs)

### Auto-close Issues
- Include `Closes #N` (or `Fixes`/`Resolves`) in the PR body — GitHub closes the issue automatically on merge.
- **Verify** if the issue was closed after merge. If not, close manually with `state=closed`, `reason=completed`.

### Recommended Flow
1. Create branch from `main` (`feat/descriptive-name`)
2. Commit with descriptive message (imperative, < 72 chars)
3. Include `Closes #N` in PR body
4. Open PR targeting `main`
5. After merge, confirm the issue was closed

## Contact Channels

- WhatsApp (main CTA): direct link to company number
- Footer: institutional links

---

## Agent Orchestration

The project uses a system of **5 pipeline agents + 3 support agents + 1 harness engineer** for automated quality-driven development:

| Agent | Role | Archetype | Tools |
|--------|-------|-----------|-------------|
| `orquestrador` | Coordinates the complete flow — issue → branch → plan → implement → review → commit → PR | Coordinator | All |
| `planejador` | Analyzes issues and codebase, generates detailed plans in `.github/plans/` | Worker (read-only) | read, search, web |
| `implementador` | Executes plans, makes surgical changes, verifies build | Worker | read, search, edit, execute |
| `revisor` | Analyzes diff vs plan, verifies 10 quality dimensions, classifies issues | Auditor (read-only) | read, search |
| `engenheiro-de-harness` | Audits, maintains, and iteratively refines all harness components. Applies agent taxonomy, KPIs, prompt engineering, context budget, and error recovery patterns. Uses Chronicle for data-driven decisions. | Specialist | read, search, edit, session_store_sql, memory/*, vscode/memory, agent |

### Support Agents

| Agent | Role | Archetype | Tools |
|--------|-------|-----------|-------------|
| `criador-conteudo` | Generates and updates institutional content (texts, descriptions, testimonials) | Specialist | read, search |
| `performance-auditor` | Audits Core Web Vitals, asset loading, blocking resources | Auditor | read, search, browser |
| `refactor-css` | Refactors scoped CSS, extracts patterns to `app.css`, audits design tokens | Specialist | read, search, edit |

### Pipeline Flow
```
User → /iniciar-bugfix|feature|improvement
  → Orchestrator analyzes → Creates Issue → HITL
  → Creates Branch → Planner → Implementer → Reviewer
  → [loop max. 3x if critical/major]
  → Commit → PR → HITL → Merge → Checkout main
```

### How to Start
- `/iniciar-bugfix` — for bug fixes
- `/iniciar-feature` — for new features
- `/iniciar-melhoria` — for improvements and refactoring

---

## Agent Taxonomy

Every agent in the Zan.IA harness fits exactly one archetype. This classification governs tool permissions, invocation patterns, and design constraints.

| Archetype | Purpose | Tool Profile | Invocation | Examples |
|-----------|---------|-------------|------------|----------|
| **Coordinator** | Orchestrates multi-agent workflows, manages HITL gates, tracks pipeline state | Full toolkit + `agent` | User-facing + subagent-invocable | `orquestrador` |
| **Worker** | Executes a specific pipeline phase — structured input → structured output | Domain-specific | Subagent-invocable | `planejador`, `implementador` |
| **Specialist** | Deep domain expertise, operates independently or on-demand | Domain-specific tools | User-invocable or on-demand | `criador-conteudo`, `refactor-css`, `engenheiro-de-harness` |
| **Auditor** | Read-only analysis, produces reports — NEVER modifies files | read, search | On-demand or post-cycle | `revisor`, `performance-auditor` |
| **Gatekeeper** | Enforces quality gates automatically | read, search, hooks | Automatic (hooks) | *(future)* |

### Agent Body Standards
All `.agent.md` bodies follow the **7-Point Prompt Engineering Template**:
1. **Role** — Identity, domain expertise, primary output
2. **Constraints** — NEVER rules first, ALWAYS rules second
3. **Context Sources** — Prioritized files/docs to consult before acting
4. **Procedure** — Numbered steps with decision branches and edge cases
5. **Patterns & Examples** — ✅ Correct / ❌ Wrong pairs
6. **Output Format** — File path, structure, success criteria
7. **Reference Files** — Canonical file list with descriptions

See `.github/skills/harness-engineering-reference/SKILL.md` for complete agent design reference.

### Harness Performance (KPIs)
| KPI | Healthy Range |
|-----|---------------|
| Pipeline Success Rate | > 80% |
| First-Pass Quality | > 60% |
| Review Loop Count | < 1.5 avg |
| Session Turns per Task | < 30 |
| Checkpoint Rate | > 70% |
| Quality Gate Compliance | 100% |

---

## Harness Review Workflow (Post-Issue Cycle)

After each issue→PR cycle, the `engenheiro-de-harness` agent performs a harness review:

```
Issue → Branch → Plan → Implement → Review → PR
                                                  │
                                                  ▼
                                         ┌──────────────────┐
                                         │ HARNESS REVIEW    │
                                         │ (engenheiro-de-  │
                                         │  harness)         │
                                         │                   │
                                         │ 1. Query Chronicle│
                                         │ 2. Static analysis│
                                         │ 3. Detect patterns│
                                         │ 4. If issues found│
                                         │    → create       │
                                         │    separate PR    │
                                         └──────────────────┘
```

### Trigger conditions for separate harness PR:
- Anti-pattern detected in >= 2 sessions for the same agent
- File thrashing detected (same file read >5 times across sessions)
- Static analysis violation — broken handoff/agents references
- Chronicle inverted check — tool used but not declared by agent
- Session took >30 turns without checkpoint
- `npm run check` or `npm run build` not run in session with edits
- Failure-side repetition — user frustration + same agent + same file in >= 2 sessions

### Separate Harness PR Convention
- **Branch:** `harness/fix-description` or `harness/improve-agent-name`
- **Commit:** `harness: fix tool permissions for agent X`
- **PR title:** `🔧 Harness: <description>`
- **PR body:** Include Chronicle evidence (query results, patterns found)

---

## Tool Usage

Strict rules for using each Copilot tool (defined in `.github/instructions/tool-usage.instructions.md`):

| Tool | Main Rule |
|-----------|----------------|
| **`vscode_askQuestions`** | MANDATORY for any ambiguity. Never assume user preferences. |
| **Browser** | Use for official documentation, DEV vs LIVE comparison. Always validate URLs. |
| **Terminal** | Non-destructive commands. Build and lint before concluding. |
| **Editing** | Read before editing. Minimal changes. Check errors after each edit. |
| **Search** | Search before assuming something doesn't exist. |
| **Memory** | Use `/memories/session/pipeline-state.md` to track pipeline state. |
| **Subagents** | Prefer specialized agents (`planejador`, `implementador`, `revisor`, `Explore`). |

---

## Harness Copilot — Customization Primitives

VS Code offers **7 primitives** that form the complete harness. All project agents MUST understand these primitives to make correct decisions.

### Primitives Table

| Primitive | File | Location | Activation |
|-----------|---------|------|----------|
| **Agent Instructions** | `AGENTS.md` | Root | Always-on (entire workspace) |
| **File Instructions** | `*.instructions.md` | `.github/instructions/` | `applyTo` (glob) or `description` (semantic) |
| **Custom Agents** | `*.agent.md` | `.github/agents/` | Agent selector or `runSubagent` |
| **Prompts** | `*.prompt.md` | `.github/prompts/` | Slash command (`/`) |
| **Skills** | `SKILL.md` | `.github/skills/<name>/` | Slash command or automatic detection |
| **Hooks** | `*.json` | `.github/hooks/` | Lifecycle events (`PreToolUse`, etc.) |
| **MCP Servers** | Config JSON | `.vscode/` or settings | MCP tools on demand |

### Loading Hierarchy and Priority

```
💡 Always-on: AGENTS.md → global guidelines
📁 Per-file: *.instructions.md → specific rules (applyTo)
🎯 On-demand: Skills, Prompts, Custom Agents, MCP
🤖 Automatic: Hooks → lifecycle events
```
**Priority (conflicts):** User profile > Workspace (`.github/`, `AGENTS.md`) > Organization.

### Pipeline Map on the Harness

```
AGENTS.md + tool-usage.instructions.md + pipeline-workflow.instructions.md
  → orquestrador.agent.md
    → planejador | implementador | revisor | Explore
```
Skills and Prompts loaded on demand according to context.

### Agent Invocation Control

Each `.agent.md` controls how it can be invoked via frontmatter:

| Field | Effect |
|-------|--------|
| `user-invocable: false` | Hidden from agent selector, but still subagent-invocable |
| `disable-model-invocation: true` | Prevents other agents from using as subagent |
| `agents: ["name1", "name2"]` | Explicit list of allowed subagents |
| `tools: ["read", "search"]` | Restricts agent tools |

**Rules:**
- Listing an agent in `agents` overrides `disable-model-invocation: true`.
- When `agents` is specified (even if empty), the `"agent"` tool MUST be included in `tools`. This is the tool that enables subagent invocation (`runSubagent`).

### Fork Mode (Skills)

Skills with `context: fork` execute in an isolated subagent — useful for tasks that read many files. Requires `github.copilot.chat.skillTool.enabled`.

### Orchestration Patterns

- **Coordinator + Worker:** `orquestrador` delegates to specialized workers with minimal tools (principle of least privilege). Workers return only final result; coordinator synthesizes.
- **Handoffs:** Interactive transitions between agents via UI buttons — `send: false` fills the prompt, `send: true` sends automatically.

---

## CI/CD Pipeline via Agents

### Plan → Implement → Review Cycle

1. **Plan (Planner):** Analyzes the issue, explores the codebase, generates plan in `.github/plans/issue-{N}-{slug}.md`
2. **Implement (Implementer):** Executes the plan step by step, surgical changes, verifies `npm run check` and `npm run build`
3. **Review (Reviewer):** Analyzes diff against plan, verifies 10 quality dimensions (code, architecture, design, readability, performance, maintainability, specificity, dependencies, tests, accessibility), classifies issues as critical/major/minor
4. **Loop:** If critical or major → re-plan (max. 3 iterations). If minor → document in PR as follow-up.
5. **Harness Review (Harness Engineer):** After PR creation, queries Chronicle for session patterns, runs static analysis, detects anti-patterns. If issues found → creates separate harness improvement PR.

### Human-in-the-Loop (HITL)
- 🛑 **After issue creation** — user reviews and approves
- 🛑 **After PR creation** — user reviews diff and merges

### Conventions

| Convention | Standard |
|-----------|--------|
| **Branch** | `fix/`, `feat/`, `improve/` + lowercase slug |
| **Commit** | [Conventional Commits](https://www.conventionalcommits.org/): `fix:`, `feat:`, `improve:` |
| **PR** | Include `Closes #N` in body |
| **Issue** | Type-specific template (bug/feature/improvement) |
| **Plan** | `.github/plans/issue-{N}-{slug}.md` |
| **State** | `/memories/session/pipeline-state.md` |
| **Review** | Max. 3 iterations; critical/major → re-plan; minor → document |
| **Harness PR** | `harness/` prefix, separate from feature PR |

### Pipeline Files
- `.github/instructions/pipeline-workflow.instructions.md` — complete flow
- `.github/instructions/tool-usage.instructions.md` — tool rules
- `.github/agents/orquestrador.agent.md` — coordinator agent
- `.github/agents/planejador.agent.md` — planning agent
- `.github/agents/implementador.agent.md` — implementation agent
- `.github/agents/revisor.agent.md` — review agent
- `.github/agents/engenheiro-de-harness.agent.md` — harness engineer agent
- `.github/prompts/iniciar-bugfix.prompt.md` — bugfix entry prompt
- `.github/prompts/iniciar-feature.prompt.md` — feature entry prompt
- `.github/prompts/iniciar-melhoria.prompt.md` — improvement entry prompt
- `.github/skills/harness-engineering-reference/SKILL.md` — harness engineering reference
- `.github/skills/pipeline-orquestracao/SKILL.md` — orchestration skill
