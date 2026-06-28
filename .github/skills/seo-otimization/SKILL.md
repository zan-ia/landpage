---
name: seo-otimization
description: "Conhecimento técnico de SEO para a landing page Zan.IA (SvelteKit). Fornece templates de meta tags, Open Graph, Twitter Card, Structured Data (JSON-LD), hierarquia de headings e checklist de performance SEO. Use quando: otimizar SEO, configurar meta tags, adicionar structured data, ou auditar acessibilidade semântica."
argument-hint: "[opcional] Escopo: 'meta-tags', 'open-graph', 'structured-data', 'headings', 'completo'"
user-invocable: true
disable-model-invocation: false
---

# Skill: SEO Optimization — Zan.IA (SvelteKit)

Conhecimento técnico reutilizável para otimização de SEO na landing page Zan.IA.

## Templates de Meta Tags

### Meta Tags Essenciais (`src/app.html`)

```html
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Zan.IA — Deep Tech Systems | Agentes de IA, Automação e Mídia</title>
  <meta name="description" content="A Zan.IA desenvolve sistemas web inteligentes, agentes de IA e automação para transformar seu negócio. Conheça nossas soluções em desenvolvimento web, chatbots e criação de mídia.">
  <meta name="robots" content="index, follow">
  <link rel="canonical" href="https://www.zan.ia.br/">
</head>
```

### Open Graph (`src/app.html`)

```html
<meta property="og:title" content="Zan.IA — Deep Tech Systems">
<meta property="og:description" content="Sistemas web inteligentes, agentes de IA e automação para transformar seu negócio.">
<meta property="og:image" content="https://www.zan.ia.br/assets/images/og-image.jpg">
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">
<meta property="og:url" content="https://www.zan.ia.br/">
<meta property="og:type" content="website">
<meta property="og:locale" content="pt_BR">
<meta property="og:site_name" content="Zan.IA">
```

### Twitter Card

```html
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Zan.IA — Deep Tech Systems">
<meta name="twitter:description" content="Sistemas web inteligentes, agentes de IA e automação.">
<meta name="twitter:image" content="https://www.zan.ia.br/assets/images/og-image.jpg">
```

## Structured Data (JSON-LD)

### Organization

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Zan.IA",
  "alternateName": "Zan IA",
  "url": "https://www.zan.ia.br/",
  "description": "Deep Tech Systems — desenvolvimento web, agentes de IA, automação e criação de mídia.",
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
  "name": "Zan.IA",
  "url": "https://www.zan.ia.br/",
  "description": "Deep Tech Systems — desenvolvimento web, agentes de IA, automação e criação de mídia.",
  "inLanguage": "pt-BR"
}
</script>
```

## Hierarquia de Headings

| Nível | Componente | Exemplo |
|-------|-----------|---------|
| `<h1>` | `Hero.svelte` | Único por página — título principal |
| `<h2>` | `Solutions.svelte`, `Differential.svelte`, `Authority.svelte`, `Testimonials.svelte` | Títulos de seção |
| `<h3>` | Cards dentro de cada seção | Títulos de cards/serviços |

### Regras
- **NUNCA** pular níveis (h1 → h3 sem h2)
- **NUNCA** usar heading para estilo visual (usar classes CSS)
- **SEMPRE** usar `aria-label` no `<section>` correspondente
- Headings devem conter palavras-chave relevantes naturalmente

## Performance SEO

### Fontes
```html
<!-- Preconnect às origens de fontes -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
```
- Usar `font-display: swap` nas URLs do Google Fonts (`&display=swap`)

### Imagens
- `fetchpriority="high"` na imagem hero (acima da dobra)
- `loading="lazy"` em todas as imagens abaixo da dobra
- `width` e `height` explícitos para evitar CLS (Cumulative Layout Shift)
- `decoding="async"` para imagens não-críticas

### Robots.txt (`static/robots.txt`)
```
User-agent: *
Allow: /
Sitemap: https://www.zan.ia.br/sitemap.xml
```

## Checklist de Acessibilidade Semântica

- [ ] `<html lang="pt-BR">`
- [ ] `<h1>` único na página
- [ ] Headings em ordem hierárquica (sem pular níveis)
- [ ] Todas as imagens com `alt` descritivo (ou `alt=""` para decorativas)
- [ ] Links externos com `rel="noopener noreferrer"`
- [ ] `aria-label` em elementos interativos sem texto visível
- [ ] `aria-current="page"` no link de navegação ativo
- [ ] Contraste de cor adequado (texto sobre fundo escuro)
- [ ] `prefers-reduced-motion` respeitado nas animações

## Referências

- Meta tags: `src/app.html`
- Estrutura semântica: `src/routes/+layout.svelte`, `src/routes/+page.svelte`
- Componentes: `src/lib/components/*.svelte`
- Prompt de ação: `.github/prompts/otimizar-seo.prompt.md`
- Conteúdo institucional: `docs/INSTITUCIONAL.md`
