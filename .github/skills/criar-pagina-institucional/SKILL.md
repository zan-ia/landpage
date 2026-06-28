---
name: criar-pagina-institucional
description: 'Creates new institutional pages as Svelte components following the landing page visual standard (SvelteKit). Use when: needing to add landing pages, service pages, institutional pages, or sections that follow the existing design system (glass panels, Material Design 3 palette, Space Grotesk/Geist/JetBrains Mono typography).'
argument-hint: 'Description of the page to create (e.g., "pricing page", "chatbot service landing page")'
user-invocable: true
disable-model-invocation: false
---

# Create Institutional Page — Landing Page (SvelteKit)

Generates a new Svelte component or route following the landing page design system.

## When to Use
- Add new pages to the SvelteKit site
- Create service landing pages
- Add institutional sections (e.g., "Privacy Policy")
- Any page that needs to follow the existing visual pattern

## Design System (see `src/lib/app.css`)

### Design Tokens
```css
/* Colors - Material Design 3 Dark */
--color-surface: #0c1324;
--color-surface-container: #191f31;
--color-background: #0c1324;
--color-primary: #baf2ff;
--color-primary-container: #00e0ff;
--color-on-surface: #dce1fb;
--color-on-primary: #00363f;
--color-outline-variant: #3b494c;

/* Fonts */
--font-display: 'Space Grotesk', sans-serif;
--font-body: 'Geist', sans-serif;
--font-code: 'JetBrains Mono', monospace;

/* Spacing */
--spacing-gutter: 24px;
--spacing-margin-mobile: 16px;
--spacing-margin-desktop: 64px;
```

### Reusable Components

| Component | Class | Description |
|---|---|---|
| **Glass Panel** | `.glass-panel` | Global in `app.css` — blur(24px), subtle border |
| **Material Icon** | `<span class="material-symbols-outlined">name</span>` | Google Material Symbols |
| **Card Grid** | CSS grid 3 cols (responsive) | `grid-template-columns: repeat(3, 1fr)` |

### Svelte Component Template

```svelte
<script lang="ts">
  // Props and state
  interface Props {
    title: string;
  }
  let { title }: Props = $props();
</script>

<section class="new-page" aria-label="{title}">
  <div class="new-page__inner">
    <div class="new-page__header">
      <h2 class="new-page__title">{title}</h2>
      <p class="new-page__subtitle">Descriptive subtitle</p>
    </div>

    <div class="new-page__grid">
      <div class="new-page__card glass-panel">
        <span class="material-symbols-outlined new-page__icon">icon_name</span>
        <h3 class="new-page__card-title">Card Title</h3>
        <p class="new-page__card-desc">Description</p>
      </div>
    </div>
  </div>
</section>

<style>
  .new-page {
    padding: 96px var(--spacing-margin-mobile);
  }
  .new-page__inner {
    max-width: var(--spacing-container-max);
    margin: 0 auto;
  }
  .new-page__header {
    text-align: center;
    margin-bottom: 64px;
    display: flex;
    flex-direction: column;
    gap: 16px;
  }
  .new-page__title {
    font-family: var(--font-display);
    font-size: var(--font-size-headline-lg);
    color: var(--color-on-surface);
  }
  .new-page__subtitle {
    font-family: var(--font-body);
    font-size: var(--font-size-body-md);
    color: var(--color-on-surface);
    opacity: 0.7;
  }
  .new-page__grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: var(--spacing-gutter);
  }
  @media (min-width: 768px) {
    .new-page__grid {
      grid-template-columns: repeat(3, 1fr);
    }
  }
  .new-page__card {
    display: flex;
    flex-direction: column;
    gap: 24px;
    padding: 32px;
  }
  .new-page__card-title {
    font-family: var(--font-display);
    font-size: var(--font-size-headline-md);
    color: var(--color-on-surface);
  }
  .new-page__card-desc {
    font-family: var(--font-body);
    font-size: var(--font-size-body-md);
    color: var(--color-on-surface);
    opacity: 0.7;
  }
</style>
```

## Procedure

1. **Briefing**: Understand the page purpose
2. **Create the component**: `src/lib/components/PageName.svelte`
3. **Register the route** (if separate page):
   - Create `src/routes/page-name/+page.svelte`
   - Add `src/routes/page-name/+page.js` with `export const prerender = true;`
4. **Use scoped CSS**: `<style>` in the component itself
5. **Design tokens**: Always `var(--color-*)`, `var(--font-*)`, `var(--spacing-*)`
6. **Glass panels**: Use the global `.glass-panel` class
7. **Responsiveness**: Mobile-first, 768px breakpoint
8. **Icons**: Material Symbols Outlined

## Reference Files
- `src/lib/components/*.svelte` — existing components (visual pattern)
- `src/lib/app.css` — global design tokens
- `AGENTS.md` — code conventions, stack
- `docs/INSTITUCIONAL.md` — institutional content
