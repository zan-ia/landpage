# Task 12 — Harness Translation (PT→EN) + engenheiro-de-harness Agent

## Objective
Translate the entire landing page harness from Portuguese to English, and create a new `engenheiro-de-harness` agent inspired by the Continual Harness paper (arXiv:2605.09998) that manages the harness lifecycle autonomously.

## Scope: 31 files translated + 1 new agent = 32 files affected

### Phase 1 — Harness Translation (PT→EN)

| # | Directory | Files | Count |
|---|-----------|-------|-------|
| 1 | `.github/agents/` | `*.agent.md` | 7 |
| 2 | `.github/instructions/` | `*.instructions.md` | 9 |
| 3 | `.github/prompts/` | `*.prompt.md` | 7 |
| 4 | `.github/skills/*/` | `SKILL.md` | 6 |
| 5 | `.github/` | `copilot-instructions.md` | 1 |
| 6 | Root | `AGENTS.md` | 1 |

**Translation rules:**
- Frontmatter YAML: `name`, `description`, `label` fields → EN
- Body markdown: all headings, prose, tables, code comments → EN
- Code blocks (YAML, Svelte, CSS, JS): comments only → EN
- Portuguese word "todos" (= "all") in prose: translate correctly to "all" (not tool name)
- Tool name references: keep `"todo"`, `"read"`, `"search"`, etc. as-is
- File paths and technical identifiers: keep as-is

#### 1a. Agents (7 files)

| File | Key Content to Translate |
|------|--------------------------|
| `orquestrador.agent.md` | Pipeline coordinator — 12-step flow, constraints, handoffs |
| `planejador.agent.md` | Planner — analysis procedure, information sources |
| `implementador.agent.md` | Implementer — code patterns, constraints, Svelte 5 quick reference |
| `revisor.agent.md` | Reviewer — 10 quality dimensions, severity classification |
| `criador-conteudo.agent.md` | Content creator — tone/voice guide, content types, procedure |
| `performance-auditor.agent.md` | Performance auditor — CWV checklist, asset audit, procedure |
| `refactor-css.agent.md` | CSS refactor — audit procedure, architecture rules |

#### 1b. Instructions (9 files)

| File | Key Content |
|------|-------------|
| `css.instructions.md` | Design tokens, scoped CSS, BEM naming, breakpoints, animations |
| `deploy.instructions.md` | Build structure, CI/CD, GitHub Pages conventions |
| `html.instructions.md` | Semantic HTML5, ARIA, headings, images, links |
| `pipeline-workflow.instructions.md` | Plan→Implement→Review cycle, HITL, severity, conventions |
| `project-organization.instructions.md` | src/ structure, naming, dependency rules |
| `style-architecture.instructions.md` | Glass panels, gradients, grid, badges, typography |
| `svelte.instructions.md` | Svelte 5 Runes conventions |
| `tool-usage.instructions.md` | Tool usage rules (LARGEST file — ~700 lines) |
| `typescript.instructions.md` | TypeScript conventions for SvelteKit |

#### 1c. Prompts (7 files)

| File | Key Content |
|------|-------------|
| `adicionar-depoimento.prompt.md` | Add testimonial workflow |
| `adicionar-servico.prompt.md` | Add service workflow |
| `iniciar-bugfix.prompt.md` | Bugfix initiation |
| `iniciar-feature.prompt.md` | Feature initiation |
| `iniciar-melhoria.prompt.md` | Improvement initiation |
| `otimizar-seo.prompt.md` | SEO optimization |
| `revisar.prompt.md` | Code review prompt |

#### 1d. Skills (6 files)

| File | Key Content |
|------|-------------|
| `criar-pagina-institucional/SKILL.md` | Create institutional page |
| `criar-section/SKILL.md` | Create section component |
| `css-comparison-workflow/SKILL.md` | CSS comparison DEV vs LIVE |
| `otimizar-imagens/SKILL.md` | Image optimization |
| `pipeline-orquestracao/SKILL.md` | Pipeline orchestration knowledge |
| `seo-otimization/SKILL.md` | SEO optimization knowledge |

#### 1e. Root Harness Files (2 files)

| File | Key Content |
|------|-------------|
| `copilot-instructions.md` | Critical rules, stack, quality |
| `AGENTS.md` | Full project guidelines (~350 lines) |

---

### Phase 2 — Create `engenheiro-de-harness` Agent

**File:** `.github/agents/engenheiro-de-harness.agent.md`

#### 2a. Continual Harness Concepts (from arXiv:2605.09998)

Key principles to embed in the agent:

| Concept | Application to Landing Page Harness |
|---------|-------------------------------|
| **Self-improving harness** | Agent audits and iteratively refines prompts, sub-agents, skills, and memory |
| **Reset-free adaptation** | Agent improves harness online — no full rewrites, only targeted edits |
| **Acting + refining loop** | Agent both maintains harness AND uses it to validate improvements |
| **Start from existing** | Agent builds on current harness, doesn't start from scratch |
| **Process-reward co-learning** | Agent tracks what works/fails in pipeline execution, feeds back into harness |
| **Long-context memory** | Agent uses persistent memory (`/memories/repo/`) to track harness evolution |

#### 2b. Agent Design

```yaml
name: "engenheiro-de-harness"
description: "Harness engineer for the landing page project. Audits, maintains, and iteratively refines all harness components (agents, instructions, prompts, skills) following Continual Harness principles. Uses Chronicle (session_store_sql) to extract patterns, anti-patterns, and improvement points from historical sessions. Use when: auditing harness consistency, creating/modifying agents/instructions/prompts/skills, post-PR harness review, translating harness content, or optimizing the development pipeline."
tools:
  - "read"
  - "search"
  - "edit"
  - "todo"
  - "vscode/askQuestions"
  - "session_store_sql"
  - "memory"
agents:
  - "Explore"
  - "orquestrador"
user-invocable: true
disable-model-invocation: false
handoffs:
  - label: "🔍 Explore Codebase"
    agent: Explore
    prompt: "Explore the current harness state in .github/ — list all agents, instructions, prompts, and skills. Identify any inconsistencies in tool declarations, agent references, or applyTo patterns."
    send: false
  - label: "🚀 Trigger Pipeline Improvement"
    agent: orquestrador
    prompt: "Create a harness improvement issue based on the Chronicle analysis. Follow the Plan→Implement→Review cycle for harness changes."
    send: false
user-invocable: true
disable-model-invocation: false
```

**Language rule (CRITICAL):**
- ALL internal instructions, tool calls, and thinking → **ENGLISH**
- ALL user-facing output, responses, and explanations → **PORTUGUESE**
- Frontmatter: `description` in Portuguese (user-visible), body in English (internal)

#### 2c. Core Responsibilities

1. **Harness Audit** — Check all `.github/` files for consistency, broken references, tool name correctness
2. **Agent Lifecycle** — Create, update, deprecate agents following the project's agent template
3. **Instruction Maintenance** — Ensure `applyTo` patterns are correct, no conflicting rules
4. **Prompt & Skill Management** — Keep prompts and skills aligned with current agent capabilities
5. **Translation Guard** — Ensure all harness components remain in English; flag Portuguese content
6. **Chronicle Analysis** — Query `session_store_sql` to extract patterns, anti-patterns, repetition, and improvement points from historical sessions
7. **Cross-reference Integrity** — Verify all agent names, file paths, and tool names are consistent
8. **Post-PR Harness Review** — After each issue→PR cycle, analyze the session data and propose harness improvements; if needed, create a separate harness improvement PR

#### 2d. Continual Harness Loop

```
┌──────────────────────────────────────────────────────────┐
│              Continual Harness Loop (Chronicle-Enhanced)   │
│                                                            │
│  1. OBSERVE  → Query Chronicle for session patterns        │
│  2. DIAGNOSE → Identify anti-patterns, failures, gaps      │
│  3. PROPOSE  → Suggest harness improvements                │
│  4. IMPLEMENT → Edit harness files (agents, prompts, etc.) │
│  5. VALIDATE → Run npm run check, verify consistency       │
│  6. DOCUMENT → Log changes to /memories/repo/              │
│                                                            │
│  Trigger: after each issue→PR cycle, on user request,      │
│           or when Chronicle data reveals a pattern         │
└──────────────────────────────────────────────────────────┘
```

#### 2e. Chronicle Integration — Session History Analysis

The `engenheiro-de-harness` agent uses the `session_store_sql` tool (Chronicle) to query historical session data and extract actionable insights for harness improvement.

**Chronicle Schema (relevant tables):**

| Table | Key Columns | What It Reveals |
|-------|------------|-----------------|
| `sessions` | id, cwd, repository, branch, agent_name, created_at | Project context, session timeline |
| `turns` | session_id, turn_index, user_message, assistant_response | Conversation flow, message patterns |
| `session_files` | session_id, file_path, tool_name | File access hotspots, tool usage patterns |
| `checkpoints` | session_id, work_done, technical_details, important_files, next_steps | Session summaries, completion state |
| `search_index` (FTS5) | content, session_id, source_type | Full-text search across all session content |

**2e.1 Pattern Extraction (what works well)**

```sql
-- Sessions that completed successfully (have checkpoints)
SELECT s.id, s.agent_name, COUNT(c.id) as checkpoint_count
FROM sessions s
JOIN checkpoints c ON c.session_id = s.id
GROUP BY s.id
ORDER BY checkpoint_count DESC;
```
→ Identifies which agent configurations lead to completed work.

```sql
-- Most productive sessions (many files + checkpoints)
SELECT s.id, 
       (SELECT COUNT(*) FROM session_files sf WHERE sf.session_id = s.id) as files_touched,
       (SELECT COUNT(*) FROM checkpoints c WHERE c.session_id = s.id) as checkpoints
FROM sessions s
WHERE checkpoints > 0
ORDER BY files_touched DESC;
```
→ Correlates file coverage with task completion.

**2e.2 Anti-Pattern Detection (what goes wrong)**

| Anti-Pattern | Detection Method | Harness Fix |
|-------------|-----------------|-------------|
| **Stuck sessions** — many turns, no checkpoints | `SELECT s.id, COUNT(t.id) as turns FROM sessions s JOIN turns t ON t.session_id = s.id LEFT JOIN checkpoints c ON c.session_id = s.id WHERE c.id IS NULL GROUP BY s.id HAVING turns > 10` | Agent instructions too vague → improve planning constraints |
| **File thrashing** — same file read repeatedly | `SELECT file_path, COUNT(*) as reads FROM session_files WHERE tool_name = 'read_file' GROUP BY file_path HAVING reads > 5` | Missing documentation → create skill or instruction for that file |

#### 2e.2a Tool & Subagent Permission Audit (Multi-Layer)

**⚠️ CRITICAL CORRECTION:** The original plan assumed Chronicle would capture blocked tool/subagent usage. This is FALSE. When the system blocks a tool or subagent invocation (because it's not in the agent's `tools:` or `agents:` list), the block happens BEFORE execution — nothing is written to `session_files`. Chronicle can only detect the OPPOSITE case: a tool/subagent that WAS used successfully but SHOULD NOT have been available.

**Three-Layer Audit Strategy:**

```
Layer 1: STATIC ANALYSIS (always available, 100% deterministic)
  → Read all .agent.md frontmatter
  → Cross-reference tools: and agents: declarations
  → Detect: agent A lists subagent B in agents: but B is not declared
  → Detect: tool X declared but doesn't exist in VS Code
  → Detect: agent declared with agents: [] but handoffs reference subagents

Layer 2: HOOKS — PreToolUse/PreSubagentUse (Preview, future)
  → JSON hook in .github/hooks/validate-permissions.json
  → Fires BEFORE tool/subagent execution
  → Logs BLOCKED attempts to /memories/repo/permission-violations.md
  → engenheiro-de-harness reads this log

Layer 3: CHRONICLE — Inverted check (runtime, detects over-permissive config)
  → If tool X appears in session_files for agent A's session,
    but agent A's tools: does NOT include X
  → Then the system ALLOWED it (config is too permissive OR tool was inherited)
  → This is the INVERSE of what we originally thought
```

**Layer 1: Static Analysis (Primary — always works)**

```yaml
# No Chronicle needed. Just read all .agent.md files and cross-reference.
```

The `engenheiro-de-harness` reads all agent files and builds a permission matrix:

| Agent | Declared Tools | Declared Subagents | Issues Found |
|-------|---------------|-------------------|--------------|
| `orquestrador` | read, search, edit, execute, web, todo, vscode/askQuestions | planejador, implementador, revisor, Explore | ✅ All subagents exist |
| `planejador` | read, search, web, todo, vscode/askQuestions | Explore | ✅ No edit tool (correct) |
| `revisor` | read, search, todo, vscode/askQuestions | (none) | ⚠️ Handoff → orquestrador, but orquestrador not in `agents:` |

Static analysis catches:
- Agent A's `handoffs:` → agent B, but B not in A's `agents:` list → **handoff will fail silently**
- Agent A's `agents:` → agent C, but C.agent.md doesn't exist → **subagent will fail at runtime**
- Agent declares `edit` but shouldn't (policy violation, not system-blocked)
- Agent has `agents: []` but has `handoffs:` defined → **contradictory config**

**Layer 2: Hooks (Future — when `.github/hooks/` is supported)**

Hooks are JSON files in `.github/hooks/` that fire on lifecycle events. The AGENTS.md documents `PreToolUse` and `PostToolUse` as available events.

**Proposed hook file:** `.github/hooks/validate-permissions.json`

```json
{
  "name": "validate-tool-permissions",
  "events": ["PreToolUse", "PreSubagentUse"],
  "script": "validate-permissions.js",
  "description": "Logs blocked tool/subagent attempts to /memories/repo/permission-violations.md for harness audit"
}
```

The hook script would:
1. On `PreToolUse`: check if the tool is in the active agent's `tools:` list
2. On `PreSubagentUse`: check if the subagent is in the active agent's `agents:` list
3. If blocked → append to `/memories/repo/permission-violations.md`:
   ```markdown
   | Timestamp | Agent | Blocked Tool/Subagent | Session ID |
   |-----------|-------|----------------------|------------|
   | 2026-06-27 | planejador | edit | abc-123 |
   ```
4. The `engenheiro-de-harness` reads this file during audit

> **Note:** Hooks are listed as "Preview" in the harness-validation-report.md. The `.github/hooks/` directory does not yet exist in this project. This is a **future enhancement** — the static analysis (Layer 1) covers the same checks deterministically today.

**Layer 3: Chronicle Inverted Check (detects over-permissive config)**

The ONLY thing Chronicle CAN detect for tool/subagent permissions is the INVERSE case:

```sql
-- Find tools that WERE used successfully but agent doesn't declare them
-- This means the system allowed it → config is too permissive
SELECT DISTINCT sf.tool_name, s.agent_name, sf.file_path
FROM session_files sf
JOIN sessions s ON s.id = sf.session_id
WHERE sf.tool_name NOT IN ('read_file','search_file','list_dir','grep_search','semantic_search')
  AND s.agent_name IS NOT NULL
ORDER BY s.agent_name, sf.tool_name;
```

→ Manually compare results against each agent's declared `tools:` list.
→ If a tool appears here that the agent DOESN'T declare → the system allowed it anyway (inherited from default agent, or config error).

**2e.3 Repetition Detection — Two Distinct Causes**

Repetition in user prompts is a signal, but the **root cause** determines the harness fix. The same repeated message can mean two completely different things:

| Type | What it looks like | Root Cause | Harness Fix |
|------|-------------------|------------|-------------|
| **Demand-side** — user keeps requesting the same task | "Add a testimonial", "Create a new section" across different sessions | Missing prompt template or skill | Create `.prompt.md` or `SKILL.md` |
| **Failure-side** — user keeps complaining about the same agent mistake | "You forgot X again", "Still not working", "I already told you to Y" in same or adjacent sessions | Agent instructions are incomplete, ambiguous, or conflicting | Fix the agent's `.agent.md` constraints or `.instructions.md` rules |

**Why this distinction matters:**

A prompt template for "add testimonial" won't help if the real problem is that `criador-conteudo` keeps generating off-tone copy. The symptom (user asks again) looks the same, but the cure is different: fix the agent, not create a prompt.

**Failure-side detection query (frustration patterns):**

```sql
-- Find user messages that suggest the agent keeps making the same mistake
-- Keywords: "again", "still", "I already told you", "not working", "same problem", "you forgot"
SELECT t.session_id, t.turn_index, t.user_message,
       LAG(t.user_message) OVER (PARTITION BY t.session_id ORDER BY t.turn_index) as prev_user_msg
FROM turns t
WHERE t.user_message != ''
  AND (t.user_message LIKE '%again%'
    OR t.user_message LIKE '%still%'
    OR t.user_message LIKE '%já%'
    OR t.user_message LIKE '%ainda%'
    OR t.user_message LIKE '%esqueci%'
    OR t.user_message LIKE '%mesmo problema%'
    OR t.user_message LIKE '%I already%'
    OR t.user_message LIKE '%you forgot%'
    OR t.user_message LIKE '%not working%')
ORDER BY t.session_id, t.turn_index;
```

**Failure-side analysis — connect complaint to agent:**

```sql
-- For sessions with frustration patterns, check which agent was active
-- and what files were being edited when the complaint occurred
SELECT s.id, s.agent_name,
       (SELECT COUNT(*) FROM turns t WHERE t.session_id = s.id
        AND (t.user_message LIKE '%again%' OR t.user_message LIKE '%still%'
          OR t.user_message LIKE '%já%' OR t.user_message LIKE '%ainda%')) as frustration_turns,
       (SELECT GROUP_CONCAT(DISTINCT sf.file_path) FROM session_files sf WHERE sf.session_id = s.id) as files_touched
FROM sessions s
WHERE s.id IN (
  SELECT DISTINCT t.session_id FROM turns t
  WHERE t.user_message LIKE '%again%' OR t.user_message LIKE '%still%'
     OR t.user_message LIKE '%já%' OR t.user_message LIKE '%ainda%'
     OR t.user_message LIKE '%mesmo problema%'
)
ORDER BY frustration_turns DESC;
```

→ If the same agent + same file appears repeatedly with frustration patterns, the agent's instructions for that file/domain need fixing.

**Demand-side detection query (task repetition):**

```sql
-- Repeated task-oriented messages across DIFFERENT sessions
-- (as opposed to frustration within same session)
SELECT t.user_message, COUNT(DISTINCT t.session_id) as session_count,
       GROUP_CONCAT(DISTINCT s.agent_name) as agents_involved
FROM turns t
JOIN sessions s ON s.id = t.session_id
WHERE t.user_message != ''
  AND t.user_message NOT LIKE '%again%'
  AND t.user_message NOT LIKE '%still%'
  AND t.user_message NOT LIKE '%já%'
  AND t.user_message NOT LIKE '%ainda%'
GROUP BY t.user_message
HAVING COUNT(DISTINCT t.session_id) > 1
ORDER BY session_count DESC;
```

→ Task-oriented messages appearing across multiple sessions = candidate for `.prompt.md` or `SKILL.md`.

**Decision tree for repetition:**

```
Repeated user message detected
│
├── Contains frustration keywords?
│   ├── YES → FAILURE-SIDE
│   │   ├── Same agent always involved? → Fix that agent's instructions
│   │   ├── Same file always being edited? → Add/improve instruction with applyTo for that file
│   │   └── Same tool being misused? → Fix tool permissions or tool-usage instructions
│   │
│   └── NO → DEMAND-SIDE
│       ├── Task-oriented, >3 sessions → Create .prompt.md
│       ├── Knowledge-oriented, >3 sessions → Create SKILL.md
│       └── Rare (<3 sessions) → No action, monitor
```

**2e.4 Improvement Point Detection**

| Signal | Chronicle Query | Action |
|--------|----------------|--------|
| **Uncovered hotspots** — files frequently accessed but no instruction `applyTo` matches | Compare `session_files.file_path` against all `applyTo` patterns in instructions | Create or update `applyTo` patterns |
| **Long sessions** — >50 turns | `SELECT s.id, COUNT(t.id) as turns FROM sessions s JOIN turns t ON t.session_id = s.id GROUP BY s.id HAVING turns > 50` | Agent may need better planning constraints or skill support |
| **Tool underuse** — `todo` tool never used in sessions with 3+ file edits | Check if `manage_todo_list` appears in session with >3 file operations | Reinforce `todo` usage rule in agent constraints |
| **Failure-side repetition** — user frustration with same agent/file combo across sessions | Frustration keywords + same agent + same file pattern (see 2e.3 queries) | Fix agent's instructions for that specific domain/file |

#### 2f. Harness Review at PR Time (Post-Issue Cycle)

The ideal workflow integrates harness review into the development cycle:

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
                                         │    for this cycle │
                                         │ 2. Extract patterns│
                                         │ 3. Detect anti-   │
                                         │    patterns       │
                                         │ 4. If improvements│
                                         │    needed → create│
                                         │    separate PR    │
                                         └──────────────────┘
                                                  │
                                    ┌─────────────┴─────────────┐
                                    │                           │
                              No issues found          Issues found
                                    │                           │
                              ✅ Done              Create harness
                                                   improvement PR
                                                   (separate from
                                                   feature PR)
```

**Trigger conditions for separate harness PR:**
- Anti-pattern detected in >= 2 sessions for the same agent
- File thrashing detected (same file read >5 times across sessions)
- **Static analysis violation** — agent declares `handoffs:` → agent B but B not in `agents:` list (will fail silently at runtime)
- **Static analysis violation** — agent declares `agents:` → agent C but C.agent.md doesn't exist (will fail at runtime)
- **Static analysis violation** — agent has `agents: []` but has `handoffs:` defined (contradictory config)
- **Chronicle inverted check** — tool appears in `session_files` for agent that DOESN'T declare it → system is over-permissive
- Session took >30 turns without checkpoint → planning failure
- `npm run check` or `npm run build` not run in session with edits
- **Failure-side repetition** — user frustration keywords + same agent + same file in >= 2 sessions (agent keeps causing the same problem; fix the agent, not create a prompt)
- **Hook log (future)** — `/memories/repo/permission-violations.md` shows blocked tool/subagent attempts

#### 3c. Cross-reference Updates
- Update any PT references in translated files that point to other files
- Ensure `applyTo` patterns are preserved
- Verify all `agents:` lists reference correct agent names

#### 3d. Quality Gates
- [ ] `npm run check` — 0 errors, 0 warnings
- [ ] `npm run build` — successful static generation
- [ ] No Portuguese remaining in `.github/` harness files (except user-visible descriptions)
- [ ] All 8 agents have consistent frontmatter (tools, agents, handoffs)
- [ ] `engenheiro-de-harness` responds in Portuguese when invoked
- [ ] `engenheiro-de-harness` can query Chronicle (`session_store_sql`) successfully
- [ ] Post-PR harness review workflow documented in AGENTS.md

---

## Execution Order

### Sub-task 12.1: Instructions Translation (9 files)
Largest and most critical — defines rules all agents follow.

### Sub-task 12.2: Skills Translation (6 files)
Domain knowledge — referenced by agents and prompts.

### Sub-task 12.3: Agents Translation (7 files)
Agent definitions — frontmatter + body.

### Sub-task 12.4: Prompts Translation (7 files)
User-facing entry points.

### Sub-task 12.5: Root Files Translation (2 files)
`copilot-instructions.md` + `AGENTS.md`.

### Sub-task 12.6: Create engenheiro-de-harness
New agent with Continual Harness principles + Chronicle integration.

### Sub-task 12.7: Integration & Validation
Cross-references, build, check, Chronicle query test.

### Sub-task 12.8: Create Hooks Directory & Permission Validation Hook
Create `.github/hooks/` directory with permission validation hook for future use.

---

## Phase 4 — Chronicle-Enhanced Harness Review Workflow

### 4a. Post-Issue Harness Review Checklist

After every issue is resolved and PR is created, the `engenheiro-de-harness` agent should:

1. **Query Chronicle** for the just-completed session:
   ```sql
   -- Get the most recent session for this repository
   SELECT s.id, s.created_at,
          (SELECT COUNT(*) FROM turns t WHERE t.session_id = s.id) as turns,
          (SELECT COUNT(*) FROM session_files sf WHERE sf.session_id = s.id) as files_touched
   FROM sessions s
   WHERE s.cwd LIKE '%landpage%'
   ORDER BY s.created_at DESC LIMIT 1;
   ```

2. **Run Static Analysis** (no Chronicle needed — reads agent frontmatter directly):
   - [ ] All `handoffs:` agents are declared in the parent's `agents:` list?
   - [ ] All `agents:` entries correspond to existing `.agent.md` files?
   - [ ] No agent has `agents: []` with `handoffs:` defined?
   - [ ] Read-only agents (planejador, revisor, criador-conteudo) don't declare `edit` or `execute`?
   - [ ] All tool names in `tools:` are valid Copilot tool names?

3. **Check for anti-patterns** in the session (Chronicle):
   - [ ] Was `npm run check` executed after file edits?
   - [ ] Was `todo` tool used for tasks with 3+ steps?
   - [ ] Did any file get read >3 times in the same session?
   - [ ] **Failure-side repetition**: Did the user express frustration ("again", "still", "já falei", "ainda")? If yes → is this the same agent+file as a previous frustrated session?

4. **Chronicle Inverted Check** (over-permissive detection):
   - [ ] Does `session_files` show a tool used by an agent that DOESN'T declare it in `tools:`? → System allowed it → tighten config
   - [ ] Does `session_files` show a subagent invoked that ISN'T in the parent's `agents:`? → System allowed it → add to `agents:` or investigate

5. **Cross-reference with historical patterns**:
   - [ ] Is this file a recurring hotspot across sessions?
   - [ ] Has this same error/anti-pattern appeared before?
   - [ ] Has this same agent+file+frustration combo appeared in >= 2 sessions? → **Fix agent, not create prompt**
   - [ ] Is there a missing instruction `applyTo` pattern for frequently accessed files?
   - [ ] Does a repeated user request exist WITHOUT frustration? → Candidate for `.prompt.md` (demand-side, not failure-side)

4. **Decision tree**:
   ```
   Issues found?
   ├── NO → Document: "Harness healthy for this cycle" ✅
   └── YES → Severity?
       ├── Minor (typo, wording) → Fix directly in current branch
       ├── Major (missing tool, broken reference) → Create harness-fix PR
       └── Critical (agent can't function) → Block PR, fix immediately
   ```

### 4b. Separate Harness PR Convention

When harness improvements are needed, create a separate PR:

- **Branch:** `harness/fix-description` or `harness/improve-agent-name`
- **Commit:** `harness: fix tool permissions for agent X`
- **PR title:** `🔧 Harness: <description>`
- **PR body:** Include Chronicle evidence (query results, patterns found)

### 4c. Chronicle Analysis Templates

The `engenheiro-de-harness` agent should maintain a set of reusable Chronicle queries:

**Template 1: Session Health Check**
```sql
SELECT s.id, s.created_at,
       COUNT(DISTINCT t.id) as turns,
       COUNT(DISTINCT sf.file_path) as files_touched,
       COUNT(DISTINCT c.id) as checkpoints,
       CASE WHEN COUNT(DISTINCT c.id) = 0 THEN '⚠️ No checkpoints' ELSE '✅' END as status
FROM sessions s
LEFT JOIN turns t ON t.session_id = s.id
LEFT JOIN session_files sf ON sf.session_id = s.id
LEFT JOIN checkpoints c ON c.session_id = s.id
WHERE s.created_at > datetime('now', '-7 days')
GROUP BY s.id
ORDER BY s.created_at DESC;
```

**Template 2: Static Permission Matrix (no Chronicle — reads .agent.md files)**

Instead of querying Chronicle (which can't see blocked attempts), the `engenheiro-de-harness` reads all `.agent.md` frontmatter and builds a permission matrix:

```
1. Read all .github/agents/*.agent.md
2. Extract tools:, agents:, handoffs: from each
3. Cross-reference:
   - For each handoff → agent B: is B in agents: list?
   - For each agents: → agent C: does C.agent.md exist?
   - agents: [] + handoffs: defined → contradiction
   - tools: list → are all tool names valid?
```

**Template 3: Chronicle Inverted Check (over-permissive detection)**

```sql
-- Tools that WERE used but agent DOESN'T declare → system is over-permissive
SELECT DISTINCT sf.tool_name, s.agent_name
FROM session_files sf
JOIN sessions s ON s.id = sf.session_id
WHERE s.agent_name IS NOT NULL
  AND sf.tool_name IS NOT NULL
ORDER BY s.agent_name, sf.tool_name;
```
→ Manually compare each (agent_name, tool_name) pair against the agent's `tools:` declaration.
→ If tool is in Chronicle but NOT in agent's `tools:` → system allowed it (inherited or config error).
→ If tool IS in agent's `tools:` but NEVER appears in Chronicle → tool is declared but unused (candidate for removal).

**Template 4: File Hotspot Analysis**
```sql
SELECT sf.file_path, COUNT(*) as access_count, sf.tool_name
FROM session_files sf
JOIN sessions s ON s.id = sf.session_id
WHERE s.cwd LIKE '%landpage%'
  AND sf.file_path NOT LIKE '%\.github%'
GROUP BY sf.file_path
ORDER BY access_count DESC
LIMIT 10;
```
→ Files accessed most often should have dedicated instructions.

**Template 4: Anti-Pattern — Missing Quality Gate**
```sql
-- Find sessions with edits but no npm run check
SELECT s.id, s.created_at
FROM sessions s
WHERE s.id IN (
  SELECT DISTINCT sf.session_id FROM session_files sf
  WHERE sf.tool_name IN ('replace_string_in_file','create_file','multi_replace_string_in_file')
)
AND s.id NOT IN (
  SELECT DISTINCT si.session_id FROM search_index si
  WHERE si.content MATCH 'npm run check'
)
AND s.cwd LIKE '%landpage%';
```

---

## Dependencies
- **Task 11** (frontmatter audit) — completed, `"todos"` → `"todo"` fix applied
- **arXiv:2605.09998** — Continual Harness paper concepts
- **Chronicle** (`session_store_sql`) — session history database available in VS Code

## Acceptance Criteria
- [ ] All 31 harness files translated to English (body + frontmatter)
- [ ] `engenheiro-de-harness.agent.md` created with Continual Harness + Chronicle principles
- [ ] Agent has `session_store_sql` and `memory` tools for Chronicle analysis
- [ ] Agent performs **static permission analysis** (Layer 1) — reads agent frontmatter, cross-references tools/agents/handoffs
- [ ] Agent understands the **3-layer audit model**: Static Analysis → Hooks (future) → Chronicle Inverted Check
- [ ] Agent responds in Portuguese to user, internal instructions in English
- [ ] `AGENTS.md` updated with new agent, harness review workflow, and English content
- [ ] Post-PR harness review workflow documented (with corrected non-false logic)
- [ ] `.github/hooks/` directory created with `validate-permissions.json` hook template
- [ ] Chronicle query templates defined in agent body (corrected: inverted check only)
- [ ] `npm run check` passes (0 errors, 0 warnings)
- [ ] `npm run build` succeeds
- [ ] No Portuguese prose remaining in `.github/` harness files (technical PT words in code samples are OK)
- [ ] All cross-references between harness files are consistent

---

## Dependencies
- **Task 11** (frontmatter audit) — completed, `"todos"` → `"todo"` fix applied
- **arXiv:2605.09998** — Continual Harness paper concepts

## Acceptance Criteria
- [ ] All 31 harness files translated to English (body + frontmatter)
- [ ] `engenheiro-de-harness.agent.md` created with Continual Harness principles
- [ ] Agent responds in Portuguese to user, internal instructions in English
- [ ] `AGENTS.md` updated with new agent and English content
- [ ] `npm run check` passes (0 errors, 0 warnings)
- [ ] `npm run build` succeeds
- [ ] No Portuguese prose remaining in `.github/` harness files (technical PT words in code samples are OK)
- [ ] All cross-references between harness files are consistent
