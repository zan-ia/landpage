---
name: css-comparison-workflow
description: "Compares and fixes visual differences between local dev (localhost:5173) and the LIVE site. Use when: needing to check visual equivalence, debug styles that diverge between DEV and LIVE, or ensure the local build is 100% identical to the production site."
argument-hint: "Describe which element or section is different (e.g., 'CTA WhatsApp shadow', 'card backdrop-filter')"
user-invocable: true
disable-model-invocation: false
---

# Skill: CSS Comparison Workflow — DEV vs LIVE (SvelteKit)

Workflow to identify and fix computed style differences between the development environment (`localhost:5173`) and the LIVE production site.

## When to Use
- After modifying CSS in Svelte components
- When a visual element appears different from production
- For regression verification before deploy
- After updating `src/lib/app.css`

## Tools
- **Playwright** (via VS Code browser tools) — to extract `getComputedStyle`
- **Vite dev server**: `npm run dev` → `localhost:5173`
- **Preview build**: `npm run build && npm run preview` → `localhost:4173`

## CSS Architecture (SvelteKit)

```
GLOBAL:  src/lib/app.css → design tokens, reset, .glass-panel, @keyframes
SCOPED:  each *.svelte → <style> with unique hash (no conflicts)
BUILD:   Vite extracts CSS → build/_app/immutable/assets/*.css (hashed)
```

⚠️ **Scoped CSS**: Each component has its own `<style>` with automatic namespacing. There are no class conflicts between components.

### Common Pitfalls
| Problem | Symptom | Likely Cause |
|----------|---------|----------------|
| Wrong `backdrop-filter` | Cards without blur | `.glass-panel` not applied or overridden in scoped CSS |
| Wrong `box-shadow` | Different shadow | `--shadow-*` tokens not used or hard-coded value |
| Wrong `font-family` | Inconsistent font | Not using `var(--font-body)` / `var(--font-display)` |
| Wrong `opacity` | Text too light/dark | Hard-coded value instead of using standard opacity (0.7) |

## Verification Flow

### 1. Open both pages
```
DEV:  http://localhost:5173  (dev server with HMR)
LIVE: https://www.example.com/
```

### 2. Extract computed styles

```javascript
const props = ['boxShadow', 'backdropFilter', 'backgroundColor', 'border',
               'opacity', 'fontFamily', 'fontSize', 'fontWeight', 'lineHeight',
               'color', 'gap', 'padding', 'borderRadius'];
```

### 3. Properties most sensitive to divergence
| Property | Where to check |
|-------------|---------------|
| `backdropFilter` | All `.glass-panel` — should be `blur(24px)` |
| `boxShadow` | Cards, WhatsApp CTA (`--shadow-whatsapp`) |
| `fontFamily` | `.hero__title` → `Space Grotesk`, body → `Geist` |
| `color` | Text → `rgb(220, 225, 251)` (`--color-on-surface`) |
| `opacity` | Secondary text → `0.7` |

### 4. Svelte Scoped CSS Specifics

Svelte adds a unique hash to each scoped class:
```css
/* Source code */
.testimonial__card { color: red; }

/* Compiled */
.testimonial__card.svelte-1jhcrt0 { color: red; }
```

This means selectors **never** conflict between components. But global styles from `app.css` can be overridden by scoped CSS with the same specificity.

## Common Fixes

### Check if global `.glass-panel` is being applied
1. Inspect the element in devtools
2. Confirm the `glass-panel` class appears
3. If `backdrop-filter` is wrong, the component's scoped CSS may be overriding

### Check tokens
- `var(--color-on-surface)` should resolve to `#dce1fb`
- `var(--font-body)` should resolve to `'Geist', sans-serif`
- If not resolving, check if `app.css` is being loaded (`+layout.svelte`)

## References
- `src/lib/app.css` — Global CSS (design tokens + reset + glass-panel)
- `src/lib/components/*.svelte` — Components with scoped CSS
- `build/_app/immutable/assets/*.css` — Compiled CSS (post-Vite)
