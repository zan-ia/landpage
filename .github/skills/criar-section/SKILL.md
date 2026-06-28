---
name: criar-section
description: "Creates a new section as a Svelte component on the landing page following existing visual standards. Generates the complete .svelte with glass panels, gradients, consistent typography, and scoped CSS."
argument-hint: "Section name and content description (e.g., 'pricing - Pricing section with 3 plans')"
user-invocable: true
disable-model-invocation: false
---

# Skill: Create New Section (SvelteKit)

Creates a new Svelte component in `src/lib/components/` maintaining visual consistency.

## Component Template

```svelte
<script lang="ts">
  // Section data
  const items = [
    { icon: "smart_toy", title: "Item 1", desc: "Description of item 1." },
    { icon: "psychology", title: "Item 2", desc: "Description of item 2." },
    { icon: "auto_awesome", title: "Item 3", desc: "Description of item 3." }
  ];
</script>

<!-- ════════════════════════════════════════════ -->
<!-- SECTION_NAME -->
<!-- ════════════════════════════════════════════ -->
<section id="section-name" class="section-name" aria-label="Section Name">
  <div class="section-name__inner">
    <!-- Header -->
    <div class="section-name__header">
      <h2 class="section-name__title">Section Title</h2>
      <p class="section-name__subtitle">Description or subtitle.</p>
    </div>

    <!-- Card Grid -->
    <div class="section-name__grid">
      {#each items as item}
        <div class="section-name__card glass-panel">
          <span class="material-symbols-outlined section-name__icon">{item.icon}</span>
          <h3 class="section-name__card-title">{item.title}</h3>
          <p class="section-name__card-desc">{item.desc}</p>
        </div>
      {/each}
    </div>
  </div>
</section>

<style>
  .section-name {
    padding: 96px var(--spacing-margin-mobile);
  }
  .section-name__inner {
    max-width: var(--spacing-container-max);
    margin: 0 auto;
  }
  .section-name__header {
    text-align: center;
    margin-bottom: 64px;
    display: flex;
    flex-direction: column;
    gap: 16px;
  }
  .section-name__title {
    font-family: var(--font-display);
    font-size: var(--font-size-headline-lg);
    color: var(--color-on-surface);
  }
  .section-name__subtitle {
    font-family: var(--font-body);
    font-size: var(--font-size-body-md);
    color: var(--color-on-surface);
    opacity: 0.7;
  }
  .section-name__grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: var(--spacing-gutter);
  }
  @media (min-width: 768px) {
    .section-name__grid {
      grid-template-columns: repeat(3, 1fr);
    }
  }
  .section-name__card {
    display: flex;
    flex-direction: column;
    gap: 24px;
    padding: 32px;
    transition: box-shadow 0.5s ease, transform 0.5s ease;
  }
  .section-name__card:hover {
    box-shadow: 0 0 30px rgba(0, 224, 255, 0.15);
  }
  .section-name__card-title {
    font-family: var(--font-display);
    font-size: var(--font-size-headline-md);
    color: var(--color-on-surface);
  }
  .section-name__card-desc {
    font-family: var(--font-body);
    font-size: var(--font-size-body-md);
    color: var(--color-on-surface);
    opacity: 0.7;
  }
</style>
```

## Design Tokens

| Token | Use |
|-------|-----|
| `--color-background` | Section background |
| `--color-surface-container` | Card backgrounds |
| `--color-primary` | Highlights, icons |
| `--color-on-surface` | Main text |
| `--font-display` | Titles |
| `--font-body` | Body, descriptions |
| `--font-code` | Data, badges |
| `--spacing-gutter` | Grid gap |

## Procedure

1. **Analyze** existing components in `src/lib/components/` for visual pattern
2. **Create** the file `src/lib/components/SectionName.svelte`
3. **Import** in `+page.svelte`: `import SectionName from '$lib/components/SectionName.svelte';`
4. **Add** the component in the desired order: `<SectionName />`
5. **Grid**: 1 col mobile, 2-3 cols desktop depending on density
6. **Glass panels**: Use the global `.glass-panel` class

## Consistency Checklist

- [ ] Uses `.glass-panel` for cards
- [ ] Typography with `var(--font-display)` / `var(--font-body)`
- [ ] Scoped `<style>` in the component itself
- [ ] Design tokens (`var(--color-*)`, never hex)
- [ ] Responsive (mobile-first, 768px breakpoint)
- [ ] Material Symbols icons
- [ ] 96px vertical padding
- [ ] Max-width with `var(--spacing-container-max)`
