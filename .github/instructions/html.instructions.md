---
description: "Use when: creating or editing Svelte components, writing markup in .svelte files, adding ARIA attributes, ensuring semantic HTML5 structure, or updating src/app.html meta tags. Covers accessibility, headings hierarchy, images, and links."
applyTo: "src/**/*.svelte, src/app.html"
---

# Regras e Padrões de HTML — SvelteKit

## 1. Estrutura Semântica

Use elementos HTML5 semânticos nos componentes Svelte. Evite `<div>` quando um elemento semântico existir.

```svelte
<!-- ✅ Correto — +layout.svelte -->
<header>
  <nav aria-label="Navegação principal">...</nav>
</header>
<main>
  <slot />
</main>
<footer>...</footer>

<!-- ✅ Correto — +page.svelte -->
<section id="hero" aria-label="Hero principal">...</section>
<section id="solutions" aria-labelledby="solutions-title">...</section>

<!-- ❌ Errado -->
<div class="header">...</div>
<div class="main">...</div>
```

### Hierarquia de Elementos

| Elemento | Quando Usar | Componente |
|----------|-------------|------------|
| `<header>` | Topo da página | `Header.svelte` |
| `<nav>` | Navegação principal | `Header.svelte` |
| `<main>` | Conteúdo único (1 por página) | `+layout.svelte` |
| `<section>` | Agrupa conteúdo temático | Cada componente |
| `<article>` | Conteúdo independente | Cards de depoimento |
| `<footer>` | Rodapé | `Footer.svelte` |

## 2. Headings

- **Um único `<h1>`** por página (no `Hero.svelte`)
- Headings em ordem hierárquica, sem pular níveis

```svelte
<!-- ✅ Correto -->
<!-- Hero.svelte -->
<h1 class="hero__title">Zan.IA — Deep Tech Systems</h1>

<!-- Solutions.svelte -->
<h2 class="solutions__title">Nossas Soluções</h2>

<!-- Card de solução -->
<h3 class="solutions__card-title">Agentes de IA</h3>
```

## 3. Acessibilidade (ARIA)

```svelte
<!-- ✅ Navegação com role e label -->
<nav aria-label="Navegação principal">
  <a href="/#hero" aria-current="page">Home</a>
  <a href="/#solutions">Soluções</a>
</nav>

<!-- ✅ Botão com aria-label (sem texto visível) -->
<button aria-label="Abrir menu" class="header__menu-btn">
  <span class="material-symbols-outlined">menu</span>
</button>

<!-- ✅ Carrossel com ARIA -->
<div
  class="testimonials__carousel"
  role="group"
  aria-roledescription="carrossel"
  aria-label="Depoimentos"
  tabindex="0"
>
  <div
    class="testimonial__card"
    role="group"
    aria-roledescription="slide"
    aria-label="Depoimento 1 de 6"
  >
    ...
  </div>
</div>

<!-- ✅ Região aria-live para anúncios dinâmicos -->
<div class="sr-only" aria-live="polite" aria-atomic="true">
  {liveRegionText}
</div>
```

### Checklist de Acessibilidade

- [ ] `<html lang="pt-BR">` em `src/app.html`
- [ ] Elementos interativos focáveis via teclado
- [ ] Imagens com `alt` descritivo (`alt=""` para decorativas)
- [ ] Contraste mínimo 4.5:1 (texto) / 3:1 (texto grande)
- [ ] `aria-label` em botões sem texto visível
- [ ] `aria-current="page"` no link ativo
- [ ] Carrossel com `aria-roledescription`, `aria-label` por slide
- [ ] `aria-live` para conteúdo que muda dinamicamente

## 4. Meta Tags (src/app.html)

Ordem obrigatória no `<head>`:

```html
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>ZAN.IA | Deep Tech Systems</title>
<meta name="description" content="...">

<!-- Open Graph -->
<meta property="og:title" content="ZAN.IA">
<meta property="og:description" content="...">
<meta property="og:image" content="/assets/images/og-image.jpg">
<meta property="og:url" content="https://www.zan.ia.br/">
<meta property="og:type" content="website">
<meta property="og:locale" content="pt_BR">

<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image">

<!-- Ícones -->
<link rel="icon" type="image/svg+xml" href="/favicon.svg">
<link rel="apple-touch-icon" href="/apple-touch-icon.png">

<!-- Preconnects para CDNs -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>

<!-- SvelteKit head placeholder -->
%sveltekit.head%
```

## 5. Imagens

```svelte
<!-- ✅ Imagem decorativa com lazy loading -->
<img
  src="/assets/images/solucoes.webp"
  alt=""
  loading="lazy"
  width="800"
  height="600"
  decoding="async"
>

<!-- ✅ Hero (acima da dobra) com fetchpriority -->
<img
  src="/assets/images/hero.webp"
  alt="Zan.IA — Deep Tech Systems"
  fetchpriority="high"
  width="1200"
  height="630"
  decoding="async"
>
```

### Regras
- `width` + `height` sempre explícitos (previne CLS)
- `loading="lazy"` para imagens abaixo da dobra
- `fetchpriority="high"` apenas na hero (1 por página)
- `alt` descritivo para imagens funcionais, `alt=""` para decorativas
- Assets em `static/assets/images/`, referenciados como `/assets/images/...`

## 6. Links e Navegação

```svelte
<!-- ✅ Link interno com âncora -->
<a href="/#solutions">Soluções</a>

<!-- ✅ Link externo com security attributes -->
<a href="https://wa.me/5511999999999" target="_blank" rel="noopener noreferrer">
  Fale Conosco
</a>

<!-- ✅ CTA como link, não como div clicável -->
<a href="https://wa.me/5511999999999" class="cta__button glass-panel">
  Iniciar Projeto
</a>
```

### Regras
- Links externos `target="_blank"` **devem** ter `rel="noopener noreferrer"`
- CTA principal deve ser `<a>` com `href`, não `<button>` ou `<div>`
- Nav links usam `href` para seções (`/#id`), não `javascript:void(0)`

## 7. Svelte Templates Específicos

### Componente com slot (layout)
```svelte
<!-- +layout.svelte -->
<script>
  import Header from '$lib/components/Header.svelte';
  import Footer from '$lib/components/Footer.svelte';
</script>

<Header />
<main>
  <slot />
</main>
<Footer />
```

### Página que monta componentes
```svelte
<!-- +page.svelte -->
<script>
  import Hero from '$lib/components/Hero.svelte';
  import Solutions from '$lib/components/Solutions.svelte';
</script>

<Hero />
<Solutions />
```

### bind:this para refs
```svelte
<script lang="ts">
  let carouselEl = $state<HTMLElement>();
</script>

<div bind:this={carouselEl}>
  <!-- conteúdo -->
</div>
```

### Each com itens
```svelte
{#each testimonials as t, i}
  <div class="testimonial__card glass-panel"
       role="group"
       aria-roledescription="slide"
       aria-label={`Depoimento ${i + 1} de ${total}`}
  >
    <p>{t.text}</p>
  </div>
{/each}
```

## 8. Comentários e Organização

```svelte
<!-- ════════════════════════════════════════════ -->
<!-- NOME_DA_SEÇÃO — Descrição breve -->
<!-- ════════════════════════════════════════════ -->
<section class="nome-secao" aria-label="...">
  ...
</section>
```
    placeholder="seu@email.com"
  >

  <button type="submit">Enviar</button>
</form>
```

### Regras
- Todo `<input>` precisa de `<label>` associado (via `for`/`id`)
- Use `autocomplete` para campos comuns (name, email, tel)
- `required` para campos obrigatórios
- `type="email"`, `type="tel"`, `type="url"` para validação nativa
- `novalidate` no form + validação JS customizada (se aplicável)

## 8. Injeção de CSS e JS no HTML

```html
<!-- ✅ CSS: tags <style> no <head> -->
<style>
  /* estilos */
</style>

<!-- ✅ Fontes e CDNs no <head> -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600;700&display=swap" rel="stylesheet">

<!-- ✅ JS: scripts no final do <body> -->
<script>
  /* JavaScript */
</script>
```

### Regras
- `<style>` no `<head>` (performance)
- `<script>` no final do `<body>` (não bloqueia renderização)
- `preconnect` para origins de terceiros (Google Fonts, CDNs)
- `display=swap` em Google Fonts
- Evite CSS inline em elementos (`style="..."`) — prefira classes ou `<style>`

## 9. Estrutura de Página (Ordem)

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>...</title>
  <meta name="description" content="...">
  <!-- Open Graph -->
  <!-- Twitter Card -->
  <!-- Preconnects -->
  <!-- Google Fonts / CDN CSS -->
  <link rel="stylesheet" href="...">
  <!-- Estilos inline -->
  <style>/* ... */</style>
</head>
<body>
  <header><!-- Nav --></header>
  <main>
    <section id="hero"><!-- ... --></section>
    <section id="solutions"><!-- ... --></section>
    <section id="testimonials"><!-- ... --></section>
  </main>
  <footer><!-- ... --></footer>
  <script>/* JS */</script>
</body>
</html>
```
