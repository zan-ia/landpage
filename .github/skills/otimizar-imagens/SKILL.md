---
name: otimizar-imagens
description: "Optimizes landing page images (SvelteKit). Converts to modern formats (WebP/AVIF), generates srcset with multiple resolutions, applies lazy loading, and suggests compression."
argument-hint: "Image path or directory to optimize (e.g., 'static/assets/images/hero.jpg', 'all project images')"
user-invocable: true
disable-model-invocation: false
---

# Skill: Image Optimization (SvelteKit)

Skill to manage and optimize all project images.

## Asset Location

```
static/assets/images/     ← Place images here
  └── hero.webp           ← Reference as /assets/images/hero.webp
```

In Svelte components:
```svelte
<img src="/assets/images/hero.webp" alt="..." />
```

## Optimization Workflow

### 1. Conversion to Modern Formats

```bash
# WebP (quality 80)
cwebp -q 80 input.jpg -o static/assets/images/output.webp

# AVIF (quality 60)
avifenc -s 8 -q 60 input.jpg -o static/assets/images/output.avif
```

### 2. Image with Fallback (Svelte)

```svelte
<picture>
  <source srcset="/assets/images/hero.avif" type="image/avif">
  <source srcset="/assets/images/hero.webp" type="image/webp">
  <img
    src="/assets/images/hero.jpg"
    alt="[Company Name] — [Slogan]"
    loading="lazy"
    width="1200"
    height="630"
    decoding="async"
  >
</picture>
```

### 3. Critical Images (above the fold)

```svelte
<img
  src="/assets/images/hero.webp"
  alt="[Company Name]"
  fetchpriority="high"
  width="1200"
  height="630"
  decoding="async"
>
```

### 4. Naming Conventions

```
static/assets/images/
├── hero.webp          # Main hero (above the fold)
├── hero.jpg           # Fallback
├── service-systems.webp
├── service-agents.webp
├── service-media.webp
└── og-image.jpg       # Open Graph (1200x630px)
```

## Per-Image Checklist

- [ ] Location: `static/assets/images/` (not `build/assets/images/`)
- [ ] Reference: `/assets/images/name.ext` (absolute path from root)
- [ ] Format: WebP + JPEG/PNG fallback
- [ ] Size: < 100kB (ideal), < 200kB (acceptable)
- [ ] Dimensions: explicit `width` + `height`
- [ ] `loading="lazy"` on below-the-fold images
- [ ] `fetchpriority="high"` only on hero
- [ ] Descriptive `alt`

## Procedure

1. **Map** all images referenced in components `src/lib/components/*.svelte`
2. **Identify** which are on external CDN vs. local
3. **Convert** to WebP and place in `static/assets/images/`
4. **Update** `src` in Svelte components
5. **Remove** external CDN image dependencies
6. **Verify** visually with `npm run dev`

## Tolerances

| Scenario | Action |
|---------|------|
| Hero image > 200kB | Compress to < 100kB |
| Decorative image > 100kB | Resize + compress |
| PNG with transparency | WebP supports alpha, convert |
| Animated GIF | Convert to animated WebP or MP4 video |
| SVG | Keep SVG (vector), optimize with SVGO if > 10kB |
