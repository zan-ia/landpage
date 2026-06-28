---
description: "Use when: writing or reviewing CSS in Svelte components, creating new components with scoped styles, or modifying src/lib/app.css. Covers design tokens, scoped CSS conventions, BEM naming, responsive breakpoints, and animation best practices."
applyTo:
  - "src/**/*.svelte"
  - "src/lib/app.css"
---

# Regras e Padrões de CSS — SvelteKit

## 1. Design Tokens (Variáveis CSS)

Sempre use as variáveis CSS definidas em `src/lib/app.css`. Nunca use valores hard-coded para cores, fontes ou espaçamentos.

```css
/* ✅ Correto */
color: var(--color-on-surface);
background: var(--color-surface-container);
font-family: var(--font-body);

/* ❌ Errado */
color: #e0e0e0;
background: #1e1e2e;
font-family: 'Geist', sans-serif;
```

### Tokens Disponíveis (Material Design 3 — Dark Mode)

| Categoria | Tokens |
|-----------|--------|
| **Cores Primárias** | `--color-primary`, `--color-primary-container`, `--color-on-primary`, `--color-on-primary-container` |
| **Cores de Superfície** | `--color-surface`, `--color-surface-dim`, `--color-surface-container`, `--color-surface-container-lowest`, `--color-background`, `--color-on-surface` |
| **Cores Secundárias** | `--color-secondary`, `--color-secondary-container`, `--color-on-secondary` |
| **Cores Terciárias** | `--color-tertiary`, `--color-tertiary-container`, `--color-on-tertiary-container` |
| **Cores de Erro** | `--color-error`, `--color-on-error` |
| **Cores de Borda** | `--color-outline`, `--color-outline-variant` |
| **Tipografia** | `--font-display` (Space Grotesk), `--font-body` (Geist), `--font-code` (JetBrains Mono) |
| **Tamanhos de Fonte** | `--font-size-display-lg`, `--font-size-headline-lg`, `--font-size-headline-md`, `--font-size-body-lg`, `--font-size-body-md`, `--font-size-label-sm`, `--font-size-code-md` |
| **Line Heights** | `--line-height-display-lg`, `--line-height-headline-lg`, `--line-height-body-md` |
| **Espaçamento** | `--spacing-xs` (4px), `--spacing-sm` (8px), `--spacing-md` (16px), `--spacing-lg` (24px), `--spacing-xl` (32px), `--spacing-2xl` (48px), `--spacing-gutter` (24px), `--spacing-margin-mobile` (16px), `--spacing-margin-desktop` (64px) |
| **Bordas** | `--radius-sm` (8px), `--radius-md` (12px), `--radius-lg` (16px), `--radius-xl` (24px), `--radius-full` (9999px) |
| **Sombras** | `--shadow-sm`, `--shadow-md`, `--shadow-lg`, `--shadow-xl`, `--shadow-glow-primary`, `--shadow-glow-primary-hover`, `--shadow-whatsapp` |

## 2. Scoped CSS (Estilo por Componente)

Cada componente Svelte tem seu próprio `<style>` com escopo automático. **Não** coloque estilos de um componente no arquivo de outro componente.

```svelte
<!-- ✅ Correto — styles dentro do próprio componente -->
<script lang="ts">
  // lógica do componente
</script>

<div class="hero">
  <h1 class="hero__title">Título</h1>
</div>

<style>
  .hero {
    padding: 96px var(--spacing-margin-mobile);
  }
  .hero__title {
    font-family: var(--font-display);
    font-size: var(--font-size-display-lg);
    color: var(--color-on-surface);
  }
</style>
```

### O que vai em `src/lib/app.css` (global)
- Design tokens (`:root { ... }`)
- Reset básico (`*`, `html`, `body`)
- Classes utilitárias reutilizáveis (`.glass-panel`, animações `@keyframes`)

### O que vai no `<style>` do componente
- Estilos específicos do componente
- Layout, cores, tipografia daquele componente
- Animações exclusivas do componente

## 3. Nomenclatura de Classes (BEM-like)

Use o padrão **componente__elemento--modificador** com kebab-case.

```css
/* ✅ Correto */
.testimonials__carousel { }
.testimonial__card { }
.testimonial__star { }
.testimonials__dot--active { }
.hero__title { }
.solutions__grid { }

/* ❌ Errado */
.testimonialsCarousel { }
.testimonial-card { }
.heroTitle { }
```

### Prefixos por Componente

| Componente | Prefixo | Exemplo |
|-----------|---------|---------|
| **Header** | `.header__*` | `.header__nav`, `.header__logo` |
| **Hero** | `.hero__*` | `.hero__title`, `.hero__cta` |
| **Solutions** | `.solutions__*` | `.solutions__grid`, `.solutions__card` |
| **Authority** | `.authority__*` | `.authority__counter` |
| **Differential** | `.differential__*` | `.differential__item` |
| **Testimonials** | `.testimonials__*` / `.testimonial__*` | `.testimonial__card` |
| **CTA** | `.cta__*` | `.cta__button` |
| **Footer** | `.footer__*` | `.footer__link` |

## 4. Glass Panel (Componente Global)

Definido em `src/lib/app.css`:

```css
.glass-panel {
  background: rgba(255, 255, 255, 0.03);
  backdrop-filter: blur(24px);
  -webkit-backdrop-filter: blur(24px);
  border: 1px solid rgba(186, 242, 255, 0.1);
  border-radius: var(--radius-lg);
}
```

Use a classe `.glass-panel` nos componentes Svelte:

```svelte
<div class="testimonial__card glass-panel">
  <!-- conteúdo -->
</div>
```

## 5. Media Queries e Responsividade

Breakpoint único em **768px** (mobile). Mobile-first: escreva para mobile e use `@media (min-width: 768px)` para desktop.

```css
/* Mobile (default) */
.solutions__grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: var(--spacing-gutter);
}

/* Desktop */
@media (min-width: 768px) {
  .solutions__grid {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

## 6. Animações

Defina `@keyframes` globais em `src/lib/app.css`. Animações específicas no `<style>` do componente.

```css
/* Em app.css — animações globais */
@keyframes fade-in-up {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes pulse-glow {
  0%, 100% { opacity: 0.4; }
  50% { opacity: 0.8; }
}
```

### Diretrizes
- Duração: 300ms (micro), 500ms (entrada), 2-6s (loops)
- Timing: `ease-out` para entradas, `ease-in-out` para loops
- **Sempre** respeitar `prefers-reduced-motion`:
  ```css
  @media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
      animation-duration: 0.01ms !important;
      animation-iteration-count: 1 !important;
      transition-duration: 0.01ms !important;
    }
  }
  ```
- Animar apenas `transform` e `opacity` (propriedades composited)
- `will-change` usar com moderação, apenas durante interação ativa
- `contain` para isolar subárvores visuais em grids/cards: `contain: layout style paint` (otimiza renderização)
- `content-visibility: auto` para seções abaixo da dobra (acelera paint inicial)

## 7. Especificidade

Mantenha especificidade baixa. O scoped CSS do Svelte já adiciona um hash único, evitando conflitos.

```css
/* ✅ Correto — classe direta */
.hero__cta { }

/* ❌ Evitar — aninhamento desnecessário */
section .hero .hero__container .hero__cta { }
```

### Proibições

| ❌ Prática | Motivo | Alternativa |
|-----------|--------|-------------|
| **Tailwind CSS** | O projeto usa CSS vanilla com design tokens | Use classes BEM + `var(--color-*)`, `var(--spacing-*)` |
| **`!important`** | Viola o cascade e dificulta manutenção | Aumente especificidade com seletores de classe. Única exceção: `prefers-reduced-motion` |
| **Hex hardcoded** | Desalinha do tema MD3 e quebra dark mode | Use `var(--color-*)` sempre. Exceção: `rgba()` para opacidade em glass/overlays |

## 8. Ícones (Material Symbols)

```svelte
<span class="material-symbols-outlined">star</span>
```

Use `font-variation-settings` para controlar peso/preenchimento:
```css
.material-symbols-outlined {
  font-variation-settings: 'FILL' 1, 'wght' 400, 'GRAD' 0, 'opsz' 24;
}
```

## 9. Validação

- [ ] Sem valores hard-coded de cores (sempre usar `var(--color-*)`)
- [ ] Sem `!important` (exceto em utility overrides)
- [ ] Animações respeitam `prefers-reduced-motion`
- [ ] Imagens com `width`/`height` explícitos
- [ ] Fontes com `font-display: swap`
- [ ] Estilos no componente correto (não vazar entre componentes)
- [ ] `@keyframes` globais apenas em `app.css`
