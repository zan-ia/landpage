---
description: "Initiates the improvement/refactoring pipeline on the landing page. Creates issue, improve/ branch, plans, implements, reviews, and opens PR."
argument-hint: "Describe the desired improvement (e.g., 'Optimize Google Fonts loading...')"
agent: "orquestrador"
---

# Start Improvement Pipeline

Start the complete improvement pipeline following the flow defined in `.github/instructions/pipeline-workflow.instructions.md`.

## Procedure

### 1. Understand the Improvement

If the user's description is incomplete, use `vscode_askQuestions` to clarify:

```
- header: "Current Situation"
  question: "What is working poorly or could be better?"
- header: "Expected Improvement"
  question: "What is the desired result after the improvement?"
- header: "Impact"
  question: "Which areas of the site will be affected?"
  options:
    - label: "Only performance/build"
    - label: "Visual/UX"
    - label: "Code/architecture"
    - label: "SEO/accessibility"
```

### 2. Create Improvement Issue

Create a GitHub issue in the project with:

**Title:** `improve: [short improvement description]`

**Body:**
```markdown
### Current Situation
[How it is today — what can be improved]

### Proposed Improvement
[What will be done to improve]

### Expected Benefits
- [benefit 1]
- [benefit 2]

### Impact
- **Affected components:** [list]
- **Risk:** [low | medium | high]
- **Build/Deploy:** [yes | no] affects the build process
```

### 3. HITL — Issue Approval

🛑 **STOP and wait.** Present the issue to the user and wait for explicit approval before proceeding.

### 4. Follow the Pipeline

After approval, execute the complete pipeline:

1. Create branch `improve/short-description` from `main`
2. Invoke `planejador` (subagent) — for CSS refactoring, consider using the `refactor-css` agent; for performance, consider the `performance-auditor` agent
3. Invoke `implementador` (subagent) to execute the improvement
4. Invoke `revisor` (subagent) to validate
5. If critical/major → re-plan (max. 3x)
6. Commit with `improve:` + push
7. Create PR with `Closes #N`
8. HITL — wait for PR review

---

## Common Improvement Types

### Performance
- Image optimization (use `otimizar-imagens` skill)
- Fonts: check `display=swap` and `preconnect`
- CSS: check `will-change` and `contain`
- JS chunks: analyze `build/_app/immutable/chunks/`
- Recommended agent: `performance-auditor`

### CSS / Design
- Extract duplicated patterns to `app.css`
- Fix hardcoded colors → design tokens
- Adjust glass-panel, shadows, animations
- Recommended agent: `refactor-css`

### Code / Architecture
- Migrate `export let` → `$props()` (Svelte 5 Runes)
- Reorganize components
- Improve names and documentation
- Recommended agent: `planejador` + `implementador`

---

## Commit Template (Improvement)
```
improve(Testimonials): optimize carousel performance
```
