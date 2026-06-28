---
description: "Runs a complete SEO audit on the landing page (SvelteKit) and applies fixes. Checks meta tags, structured data, Open Graph, performance, and accessibility."
argument-hint: "[optional] Audit scope: 'complete', 'meta-tags', 'open-graph', 'structured-data'"
agent: "implementador"
---

# SEO Optimization — Landing Page (SvelteKit)

Run a complete SEO audit on the source files and fix all issues.

## Files to Audit

| File | What to check |
|---------|----------------|
| `src/app.html` | Meta tags, Open Graph, fonts, lang, charset |
| `src/routes/+layout.svelte` | Semantic structure, headings, ARIA |
| `src/lib/components/Hero.svelte` | Single `<h1>`, text, images |
| `src/lib/components/Footer.svelte` | Links, schema.org |
| `static/robots.txt` | Crawl configuration |

## Checklist

### Meta Tags (`src/app.html`)
- [ ] Descriptive `<title>` (50-60 chars) with main keyword
- [ ] `<meta name="description">` (150-160 chars) with call-to-action
- [ ] `<meta name="viewport" content="width=device-width, initial-scale=1">`
- [ ] `<html lang="pt-BR">`
- [ ] `<meta charset="utf-8">` as first element

### Open Graph / Social (`src/app.html`)
- [ ] `og:title` — same pattern as title
- [ ] `og:description` — engaging summary
- [ ] `og:image` — absolute URL (1200x630px)
- [ ] `og:url` — canonical production site URL
- [ ] `og:type` — `website`
- [ ] `og:locale` — `pt_BR`
- [ ] `twitter:card` — `summary_large_image`

### Structured Data (JSON-LD)
- [ ] `Organization` schema with name, logo, URL, description
- [ ] `WebSite` schema
- [ ] Add to `<head>` of `src/app.html` or `+layout.svelte`

### Technical (Components)
- [ ] Single `<h1>` in `Hero.svelte`
- [ ] Hierarchical headings (h1 → h2 → h3, no skipping)
- [ ] Images with descriptive `alt`
- [ ] External links with `rel="noopener noreferrer"`
- [ ] `aria-label` on interactive elements without visible text
- [ ] `aria-current="page"` on active link

### SEO Performance
- [ ] Images with explicit `width` and `height`
- [ ] Fonts with `font-display: swap` + `rel="preconnect"`
- [ ] `robots.txt` allows crawling
- [ ] Sitemap (if applicable)

## Procedure

1. Audit `src/app.html` first (meta tags)
2. Audit each component for headings and alt text
3. Fix issues directly in `.svelte` files
4. Run `npm run build && npm run preview` to verify

## Expected Output
Report with:
- Issues found (file + line)
- Fixes applied
- Items requiring manual action

---

## References
- SEO knowledge skill: `seo-otimization` (meta tag, Open Graph, JSON-LD templates)
- Pipeline workflow: `.github/instructions/pipeline-workflow.instructions.md`
- HTML conventions: `.github/instructions/html.instructions.md`
- Code conventions: `AGENTS.md`
