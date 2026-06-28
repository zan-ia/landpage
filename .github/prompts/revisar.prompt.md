---
description: "Runs a code review on the landing page following the 10 quality dimensions. Analyzes diff, specific files, or staged/unstaged changes."
argument-hint: "[optional] Scope: 'diff', 'staged', 'unstaged', or file path (e.g., 'src/lib/components/Hero.svelte')"
agent: "revisor"
---

# Review Code

Run a complete code review following the 10-dimension quality checklist defined in the pipeline.

## Supported Scopes

| Scope | Description |
|--------|-----------|
| `diff` (default) | Analyzes changes between current branch and `main` |
| `staged` | Analyzes only staged changes (git diff --staged) |
| `unstaged` | Analyzes unstaged changes (git diff) |
| `<file>` | Analyzes a specific file |

## Procedure

### 1. Collect the Diff

Depending on scope:
- `diff`: `git diff main...HEAD`
- `staged`: `git diff --staged`
- `unstaged`: `git diff`
- Specific file: read the file and compare with `main`

### 2. Analyze 10 Dimensions

| # | Dimension | What to Verify |
|---|----------|----------------|
| 1 | **Code** | Scoped CSS, design tokens (`var(--color-*)`), BEM naming, Svelte 5 Runes (`$props()`, `$state()`), no Tailwind, no hex hardcoded |
| 2 | **Architecture** | Correct component composition, organized imports, layout (`+layout.svelte`) intact, no improper coupling |
| 3 | **Design** | `.glass-panel` applied, typography (Space Grotesk/Geist/JetBrains Mono), Material Design 3 palette, consistent gradients |
| 4 | **Readability** | Descriptive names in Portuguese, clean code, comments where needed, no dead code |
| 5 | **Performance** | `will-change` only during interaction, `contain` where applicable, `transform`/`opacity` animations, images with `loading="lazy"` |
| 6 | **Maintainability** | Patterns consistent with the rest of the code, CSS token reuse, no style duplication |
| 7 | **Specificity** | Low selector specificity, no `!important`, no ID selectors, no deep nesting |
| 8 | **Dependencies** | Only Material Symbols + Google Fonts, no new CDN, no unapproved npm packages |
| 9 | **Build** | `npm run check` without errors, `npm run build` without warnings, no changes in `build/` |
| 10 | **Accessibility** | ARIA labels on sections, heading hierarchy (h1→h2→h3), alt text on images, `prefers-reduced-motion`, `html lang="pt-BR"` |

### 3. Classify Issues

| Severity | Criterion | Action |
|-----------|----------|------|
| 🔴 **Critical** | Functional bug, broken build, severe visual regression | Blocks merge — fix before |
| 🟠 **Major** | Pattern violation, inconsistent design, poor performance | Should be fixed |
| 🟡 **Minor** | Style, naming, small improvements | Document as follow-up |

### 4. Generate Report

Report format:

```markdown
## Review Report — [scope]

### Summary
- Files analyzed: N
- Issues found: N (Critical: X, Major: Y, Minor: Z)

### Critical Issues 🔴
| # | File | Line | Description |
|---|---------|-------|-----------|

### Major Issues 🟠
| # | File | Line | Description |
|---|---------|-------|-----------|

### Minor Issues 🟡
| # | File | Line | Description |
|---|---------|-------|-----------|

### Recommendation
[✅ Approved | ⚠️ Approved with caveats | 🔴 Not approved]
```

## References
- Code conventions: `AGENTS.md`
- Design system: `src/lib/app.css`
- Pipeline workflow: `.github/instructions/pipeline-workflow.instructions.md`
- CSS conventions: `.github/instructions/css.instructions.md`
- HTML conventions: `.github/instructions/html.instructions.md`
- Svelte conventions: `.github/instructions/svelte.instructions.md`
- Tool usage rules: `.github/instructions/tool-usage.instructions.md`
