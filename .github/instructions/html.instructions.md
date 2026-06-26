---
applyTo: "build/**/*.html"
---

# Regras e Padrões de HTML

## 1. Estrutura Semântica

Use elementos HTML5 semânticos para definir a estrutura da página. Evite `div` quando um elemento semântico existir.

```html
<!-- ✅ Correto -->
<header>
  <nav>...</nav>
</header>
<main>
  <section id="hero">...</section>
  <section id="solutions">...</section>
  <section id="testimonials">...</section>
</main>
<footer>...</footer>

<!-- ❌ Errado -->
<div class="header">
  <div class="nav">...</div>
</div>
<div class="main">
  <div class="hero">...</div>
</div>
```

### Hierarquia de Seções

| Elemento | Quando Usar |
|----------|-------------|
| `<header>` | Topo da página ou de uma seção |
| `<nav>` | Navegação principal |
| `<main>` | Conteúdo único da página (1 por página) |
| `<section>` | Agrupa conteúdo temático (com `id` e `heading`) |
| `<article>` | Conteúdo independente (cards, depoimentos) |
| `<aside>` | Conteúdo complementar (sidebar, callout) |
| `<footer>` | Rodapé da página ou de uma seção |
| `<figure>` / `<figcaption>` | Imagens com legenda |

## 2. Headings

- **Um único `<h1>`** por página (geralmente o título hero)
- Headings seguem ordem hierárquica sem pular níveis

```html
<!-- ✅ Correto -->
<h1>Zan.IA — Deep Tech Systems</h1>
<section>
  <h2>Soluções</h2>
  <article>
    <h3>Sistemas & Sites de IA</h3>
  </article>
</section>

<!-- ❌ Errado: h1 ausente, h3 depois de h1 -->
<div class="hero-title">Zan.IA</div>
<h3>Sistemas & Sites de IA</h3>
```

## 3. Acessibilidade (ARIA)

```html
<!-- ✅ Navegação com role e label -->
<nav aria-label="Navegação principal">
  <a href="#hero" aria-current="page">Home</a>
  <a href="#solutions">Soluções</a>
</nav>

<!-- ✅ Botão comaria-label quando sem texto visível -->
<button aria-label="Abrir menu" class="mobile-menu-btn">
  <span class="material-symbols-outlined">menu</span>
</button>

<!-- ✅ Seção com aria-labelledby -->
<section aria-labelledby="solutions-title">
  <h2 id="solutions-title">Nossas Soluções</h2>
</section>
```

### Checklist de Acessibilidade

- [ ] `<html lang="pt-BR">`
- [ ] Todos os elementos interativos são focáveis via teclado
- [ ] Imagens têm `alt` descritivo (nunca vazio para imagens decorativas use `alt=""`)
- [ ] Contraste de cores mínimo 4.5:1 (texto normal) / 3:1 (texto grande)
- [ ] Links distinguíveis do texto (underline ou contraste)
- [ ] Formulários com `<label>` associado
- [ ] `aria-label` em botões sem texto visível
- [ ] `aria-current="page"` no link ativo de navegação

## 4. Meta Tags

Ordem obrigatória no `<head>`:

```html
<meta charset="utf-8">           <!-- 1º: sempre primeiro -->
<meta name="viewport" content="width=device-width, initial-scale=1">  <!-- 2º -->
<title>ZAN.IA | Deep Tech Systems</title>  <!-- 3º -->
<meta name="description" content="...">    <!-- 4º -->

<!-- Open Graph -->
<meta property="og:title" content="ZAN.IA">
<meta property="og:description" content="...">
<meta property="og:image" content="...">
<meta property="og:url" content="https://zanetti-lg.github.io/zania-website/">
<meta property="og:type" content="website">
<meta property="og:locale" content="pt_BR">

<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image">

<!-- Ícones -->
<link rel="icon" type="image/svg+xml" href="/assets/images/favicon.svg">
<link rel="apple-touch-icon" href="/assets/images/apple-touch-icon.png">
```

## 5. Imagens Responsivas

```html
<!-- ✅ Imagem decorativa com lazy loading -->
<img
  src="/assets/images/solucoes.webp"
  alt=""
  loading="lazy"
  width="800"
  height="600"
  class="w-full h-auto"
  decoding="async"
>

<!-- ✅ Hero (acima da dobra) com fetchpriority -->
<img
  src="/assets/images/hero.webp"
  alt="Zan.IA — Deep Tech Systems"
  fetchpriority="high"
  width="1200"
  height="630"
  class="w-full h-auto"
  decoding="async"
>

<!-- ❌ Errado: sem dimensões, sem alt -->
<img src="hero.jpg">
```

### Regras para Imagens
- `width` + `height` sempre explícitos (previne CLS)
- `loading="lazy"` para imagens abaixo da dobra
- `fetchpriority="high"` apenas na imagem hero (1 por página)
- `alt` descritivo para imagens funcionais, `alt=""` para decorativas
- Formato WebP com fallback (ou AVIF se suporte for aceitável)

## 6. Links e Navegação

```html
<!-- ✅ Link interno com âncora -->
<a href="#solutions">Soluções</a>

<!-- ✅ Link externo com security attributes -->
<a href="https://wa.me/5511999999999" target="_blank" rel="noopener noreferrer">
  Fale Conosco
</a>

<!-- ✅ CTA principal como link, não div clicável -->
<a href="https://wa.me/5511999999999" class="glass-panel ...">
  Iniciar Projeto
</a>
```

### Regras
- Links externos com `target="_blank"` **devem** ter `rel="noopener noreferrer"`
- CTA principal deve ser um `<a>` com `href`, não um `button` ou `div`
- Nav links com `href` para seções (`#id`), não usar `javascript:void(0)`

## 7. Formulários

```html
<form id="contato" action="#" method="POST" novalidate>
  <label for="nome">Nome</label>
  <input
    type="text"
    id="nome"
    name="nome"
    required
    autocomplete="name"
    class="..."
  >

  <label for="email">E-mail</label>
  <input
    type="email"
    id="email"
    name="email"
    required
    autocomplete="email"
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
