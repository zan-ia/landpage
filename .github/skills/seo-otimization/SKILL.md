---
name: seo-otimization
description: "Technical SEO knowledge for landing pages (SvelteKit). Provides templates for meta tags, Open Graph, Twitter Card, Structured Data (JSON-LD), heading hierarchy, and SEO performance checklist. Use when: optimizing SEO, configuring meta tags, adding structured data, or auditing semantic accessibility."
argument-hint: "[optional] Scope: 'meta-tags', 'open-graph', 'structured-data', 'headings', 'complete'"
user-invocable: true
disable-model-invocation: false
---

# Skill: SEO Optimization — Landing Page (SvelteKit)

Reusable technical knowledge for SEO optimization on landing pages.

## Meta Tag Templates

### Essential Meta Tags (`src/app.html`)

```html
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>[Company Name] — [Slogan]</title>
  <meta name="description" content="[Company description with keyword and call-to-action.]">
  <meta name="robots" content="index, follow">
  <link rel="canonical" href="[SITE_URL]">
</head>
```

### Open Graph (`src/app.html`)

```html
<meta property="og:title" content="[Company Name] — [Slogan]">
<meta property="og:description" content="[Company description.]">
<meta property="og:image" content="[SITE_URL]/assets/images/og-image.jpg">
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">
<meta property="og:url" content="[SITE_URL]">
<meta property="og:type" content="website">
<meta property="og:locale" content="pt_BR">
<meta property="og:site_name" content="[Company Name]">
```

### Twitter Card

```html
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="[Company Name] — [Slogan]">
<meta name="twitter:description" content="[Short description.]">
<meta name="twitter:image" content="[SITE_URL]/assets/images/og-image.jpg">
```

## Structured Data (JSON-LD)

### Organization

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "[Company Name]",
  "alternateName": "[Alternative Name]",
  "url": "[SITE_URL]",
  "description": "Deep Tech Systems — web development, AI agents, automation and media creation.",
  "contactPoint": {
    "@type": "ContactPoint",
    "contactType": "sales",
    "availableLanguage": ["Portuguese"]
  }
}
</script>
```

### WebSite

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "WebSite",
  "name": "[Company Name]",
  "url": "[SITE_URL]",
  "description": "Deep Tech Systems — web development, AI agents, automation and media creation.",
  "inLanguage": "pt-BR"
}
</script>
```

## Heading Hierarchy

| Level | Component | Example |
|-------|-----------|---------|
| `<h1>` | `Hero.svelte` | Single per page — main title |
| `<h2>` | `Solutions.svelte`, `Differential.svelte`, `Authority.svelte`, `Testimonials.svelte` | Section titles |
| `<h3>` | Cards within each section | Card/service titles |

### Rules
- **NEVER** skip levels (h1 → h3 without h2)
- **NEVER** use heading for visual style (use CSS classes)
- **ALWAYS** use `aria-label` on the corresponding `<section>`
- Headings should naturally contain relevant keywords

## SEO Performance

### Fonts
```html
<!-- Preconnect to font origins -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
```
- Use `font-display: swap` in Google Fonts URLs (`&display=swap`)

### Images
- `fetchpriority="high"` on hero image (above the fold)
- `loading="lazy"` on all below-the-fold images
- Explicit `width` and `height` to prevent CLS (Cumulative Layout Shift)
- `decoding="async"` for non-critical images

### Robots.txt (`static/robots.txt`)
```
User-agent: *
Allow: /
Sitemap: [SITE_URL]/sitemap.xml
```

## Semantic Accessibility Checklist

- [ ] `<html lang="pt-BR">`
- [ ] Single `<h1>` on the page
- [ ] Headings in hierarchical order (no skipping levels)
- [ ] All images with descriptive `alt` (or `alt=""` for decorative)
- [ ] External links with `rel="noopener noreferrer"`
- [ ] `aria-label` on interactive elements without visible text
- [ ] `aria-current="page"` on active navigation link
- [ ] Adequate color contrast (text on dark background)
- [ ] `prefers-reduced-motion` respected in animations

## References

- Meta tags: `src/app.html`
- Semantic structure: `src/routes/+layout.svelte`, `src/routes/+page.svelte`
- Components: `src/lib/components/*.svelte`
- Action prompt: `.github/prompts/otimizar-seo.prompt.md`
- Institutional content: `docs/INSTITUCIONAL.md`
