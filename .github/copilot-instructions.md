# Zan.IA Landing Page — Copilot Instructions

## Stack
- **SvelteKit 5** (Runes mode) + `@sveltejs/adapter-static`
- **Vanilla CSS** with Svelte scoped `<style>` blocks — NO Tailwind
- **Material Design 3** dark theme via CSS custom properties in `src/lib/app.css`
- **Material Symbols Outlined** for icons
- **Google Fonts**: Space Grotesk (display), Geist (body), JetBrains Mono (code)

## Critical Rules

### CSS
- ALWAYS use design tokens: `var(--color-*)`, `var(--font-*)`, `var(--spacing-*)`, `var(--radius-*)`
- NEVER hardcode colors (hex, rgb) or font families
- Scoped CSS goes in each component's `<style>` — only shared utilities in `src/lib/app.css`
- BEM-like naming: `componente__elemento--modificador`

### HTML
- Semantic HTML5: `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>`
- Single `<h1>` per page (in Hero)
- All images need `width`/`height`, `alt`, and `loading="lazy"` (except hero: `fetchpriority="high"`)

### Svelte 5
- `$props()` not `export let`
- `$state()` for reactive state
- `$effect()` with cleanup return
- `$derived()` for computed values

### Architecture
- NEVER edit `build/` — it's generated output
- NEVER add Tailwind or any CSS framework
- NEVER add CDN dependencies without explicit approval
- Static assets in `static/assets/images/`, referenced as `/assets/images/...`

### Quality
- Run `npm run check` and `npm run build` before committing
- **MANDATORY: Use `vscode/askQuestions` tool for ANY user communication — NEVER ask questions in plain text**
- **MANDATORY: Use `todos` tool to track sequential execution for any task with 3+ steps**
- Consult `docs/INSTITUCIONAL.md` for company content

See [AGENTS.md](./AGENTS.md) for full project guidelines, agent pipeline, and skill documentation.
