---
description: "Initiates the new feature pipeline on the landing page. Creates issue, feat/ branch, plans, implements, reviews, and opens PR."
argument-hint: "Describe the new feature (e.g., 'Add a pricing section with 3 plans...')"
agent: "orquestrador"
---

# Start Feature Pipeline

Start the complete new feature pipeline following the flow defined in `.github/instructions/pipeline-workflow.instructions.md`.

## Procedure

### 1. Understand the Feature

If the user's description is incomplete, use `vscode_askQuestions` to clarify:

```
- header: "Feature Scope"
  question: "What are the boundaries of this feature?"
- header: "Acceptance Criteria"
  question: "How will we know the feature is ready?"
- header: "Priority"
  question: "Does this feature replace something existing or is it entirely new?"
  options:
    - label: "Entirely new feature"
    - label: "Replaces/extends something existing"
    - label: "Variation of an existing component"
```

### 2. Create Feature Issue

Create a GitHub issue in the project with:

**Title:** `feat: [short feature description]`

**Body:**
```markdown
### Motivation
[Why is this feature needed? What problem does it solve?]

### Description
[What will be implemented, in detail]

### Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

### Reference Design
[Links to inspirations, mockups, or similar components on the site]

### Affected Components
- [list of existing components that will be modified]
```

### 3. HITL — Issue Approval

🛑 **STOP and wait.** Present the issue to the user and wait for explicit approval before proceeding.

### 4. Follow the Pipeline

After approval, execute the complete pipeline:

1. Create branch `feat/short-description` from `main`
2. Invoke `planejador` (subagent) — it should consult `criar-section` or `criar-pagina-institucional` skills if it's a new component
3. Invoke `implementador` (subagent) to build the feature
4. Invoke `revisor` (subagent) to validate
5. If critical/major → re-plan (max. 3x)
6. Commit with `feat:` + push
7. Create PR with `Closes #N`
8. HITL — wait for PR review

---

## Commit Template (Feature)
```
feat(Solutions): add pricing plans section

New Precos.svelte section with 3 plan cards (Basic, Pro, Enterprise).
Each card uses glass-panel with differentiated highlight for the Pro plan.
Integrated into +page.svelte between Solutions and Differential.

Closes #43
```

---

## References
- Complete pipeline: `.github/instructions/pipeline-workflow.instructions.md`
- Tool usage: `.github/instructions/tool-usage.instructions.md`
- Section creation skill: `criar-section`
- Page creation skill: `criar-pagina-institucional`
- Code conventions: `AGENTS.md`
