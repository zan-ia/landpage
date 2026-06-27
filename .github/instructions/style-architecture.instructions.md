---
description: "Use when: designing new visual components, defining CSS patterns, choosing design tokens, or ensuring visual consistency across Svelte components. Covers glass-panel, gradients, grid layouts, badges, section headers, and typography patterns."
applyTo: "src/**"
---

# Arquitetura de Estilos — Padrões SvelteKit

## 1. Sistema de Design Tokens

Definido em `src/lib/app.css`. Baseado em **Material Design 3** (dark mode).

### Paleta de Cores

> **Fonte única:** `src/lib/app.css` — todos os tokens são definidos lá. Não duplique valores.

Consulte `src/lib/app.css` para a lista completa. Tokens principais:

| Token | Uso típico |
|-------|-----------|
| `--color-primary` / `--color-primary-container` | Destaques, ícones, badges |
| `--color-surface` / `--color-surface-container` | Backgrounds de cards/seções |
| `--color-surface-container-lowest` | Background da página (`html`, `body`) |
| `--color-background` | Background alternativo |
| `--color-on-surface` | Texto principal |
| `--color-outline` / `--color-outline-variant` | Bordas |

### Como Usar nos Componentes

```svelte
<style>
  .solutions__card {
    background: var(--color-surface-container);
    border: 1px solid var(--color-outline-variant);
  }
  .solutions__card-title {
    color: var(--color-on-surface);
    font-family: var(--font-display);
  }
  .solutions__card-desc {
    color: var(--color-on-surface);
    opacity: 0.7;
  }
</style>
```

### Opacidades Padrão

| Uso | Opacidade | CSS |
|-----|-----------|-----|
| Texto principal | 100% | `color: var(--color-on-surface)` |
| Texto secundário | 70% | `opacity: 0.7` |
| Texto terciário | 50% | `opacity: 0.5` |
| Borda sutil | 10% | `border-color: rgba(186,242,255,0.1)` |
| Badge bg | 10-20% | `background: rgba(186,242,255,0.2)` |

## 2. Padrão Glass Panel

Definido globalmente em `src/lib/app.css`:

```css
.glass-panel {
  background: rgba(255, 255, 255, 0.03);
  backdrop-filter: blur(24px);
  -webkit-backdrop-filter: blur(24px);
  border: 1px solid rgba(186, 242, 255, 0.1);
  border-radius: var(--radius-lg);
}
```

### Uso nos componentes

```svelte
<!-- Card de serviço -->
<div class="solutions__card glass-panel">
  <span class="material-symbols-outlined solutions__icon">smart_toy</span>
  <h3 class="solutions__card-title">Título</h3>
  <p class="solutions__card-desc">Descrição</p>
</div>

<!-- Card de depoimento -->
<div class="testimonial__card glass-panel">
  <div class="testimonial__stars">...</div>
  <p class="testimonial__text">...</p>
</div>
```

## 3. Padrão de Gradientes

```css
/* Gradiente primário (ícones, badges) */
background: linear-gradient(135deg, var(--color-primary), var(--color-primary-container));

/* Gradiente de superfície (background de seções) */
background: linear-gradient(
  to bottom,
  var(--color-surface),
  var(--color-surface-container),
  var(--color-surface)
);

/* Gradiente de glow (hero, destaques) */
background: radial-gradient(
  ellipse at 50% 0%,
  rgba(186, 242, 255, 0.15) 0%,
  transparent 60%
);
```

## 4. Padrão de Grid Layouts (Scoped CSS)

```css
/* Mobile-first: 1 coluna */
.solutions__grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: var(--spacing-gutter);
}

/* Desktop: 3 colunas */
@media (min-width: 768px) {
  .solutions__grid {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

### Grids por Componente

| Componente | Mobile | Desktop |
|-----------|--------|---------|
| Solutions | 1 col | 3 cols |
| Authority | 1 col | 3 cols |
| Differential | 1 col | 2 cols |
| Testimonials | scroll horizontal | scroll horizontal |

## 5. Padrão de Badges / Tags

```svelte
<span class="solutions__badge">
  Agentes de IA
</span>

<style>
  .solutions__badge {
    display: inline-block;
    padding: 4px 16px;
    border-radius: var(--radius-full);
    background: rgba(186, 242, 255, 0.1);
    color: var(--color-primary);
    font-size: var(--font-size-label-sm);
    font-weight: var(--font-weight-medium);
    border: 1px solid rgba(186, 242, 255, 0.2);
  }
</style>
```

## 6. Padrão de Header de Seção

```svelte
<div class="testimonials__header">
  <h2 class="testimonials__title">O que nossos parceiros dizem</h2>
  <p class="testimonials__subtitle">Histórias de transformação digital.</p>
</div>

<style>
  .testimonials__header {
    text-align: center;
    margin-bottom: 64px;
    display: flex;
    flex-direction: column;
    gap: 16px;
  }
  .testimonials__title {
    font-family: var(--font-display);
    font-size: var(--font-size-headline-lg);
    color: var(--color-on-surface);
  }
  .testimonials__subtitle {
    font-family: var(--font-body);
    font-size: var(--font-size-body-md);
    color: var(--color-on-surface);
    opacity: 0.7;
  }
</style>
```

## 7. Padrão de Tipografia

| Elemento | Token Fonte | Token Tamanho | Peso |
|----------|-------------|---------------|------|
| Título hero (h1) | `--font-display` | `--font-size-display-lg` | 700 |
| Título seção (h2) | `--font-display` | `--font-size-headline-lg` | 600 |
| Subtítulo | `--font-body` | `--font-size-body-md` | 400 |
| Card title (h3) | `--font-display` | `--font-size-headline-md` | 500 |
| Corpo | `--font-body` | `--font-size-body-md` | 400 |
| Código/dados | `--font-code` | `--font-size-code-md` | 400 |
| Badge | `--font-code` | `--font-size-label-sm` | 500 |

## 8. Padrão de Ícones (Material Symbols)

```svelte
<span class="material-symbols-outlined">star</span>

<style>
  .material-symbols-outlined {
    font-variation-settings: 'FILL' 1, 'wght' 400, 'GRAD' 0, 'opsz' 24;
  }
</style>
```

<!-- Ícone em wrapper com gradiente -->
<div class="w-14 h-14 rounded-xl bg-gradient-to-br from-[var(--color-primary)] to-[var(--color-primary-container)] flex items-center justify-center">
  <span class="material-symbols-outlined text-3xl text-[var(--color-on-primary)]">icon_name</span>
</div>

<!-- Ícone como link social -->
<a href="..." target="_blank" rel="noopener noreferrer" class="glass-panel rounded-xl p-3 hover:scale-110 transition-transform">
  <span class="material-symbols-outlined text-2xl">lang</span>
</a>
```

### Tamanhos de Ícone

| Classe | Tamanho | Uso |
|--------|---------|-----|
| `text-xl` | 20px | Inline com texto |
| `text-2xl` | 24px | Wrapper de ícone |
| `text-3xl` | 30px | Wrapper grande (serviços) |
| `text-4xl` | 40px | Hero / destaque |

## 9. Composição de Seção (Template)

```
┌─────────────────────────────────────────┐
│  Gradient/Glow Background (absoluto)    │
├─────────────────────────────────────────┤
│  ┌─ max-w-6xl mx-auto ──────────────┐  │
│  │  ┌─ text-center ──────────────┐   │  │
│  │  │  Badge                      │   │  │
│  │  │  H2 Title                   │   │  │
│  │  │  P subtitle                 │   │  │
│  │  └─────────────────────────────┘   │  │
│  │                                     │  │
│  │  ┌─ grid grid-cols-1 md:grid-* ─┐  │  │
│  │  │  ┌─── glass-panel ───┐       │  │  │
│  │  │  │  Icon             │       │  │  │
│  │  │  │  H3 Title         │       │  │  │
│  │  │  │  P description    │       │  │  │
│  │  │  └───────────────────┘       │  │  │
│  │  │  ... (mais cards)            │  │  │
│  │  └───────────────────────────────┘  │  │
│  └─────────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

## 10. Checklist de Consistência Visual

- [ ] `glass-panel` usado para cards e containers
- [ ] Cores usam `var(--color-*)` — sem valores hard-coded
- [ ] Tipografia segue a tabela de fontes/ pesos/ tamanhos
- [ ] Badges seguem o padrão de `bg-primary/10` + `border-primary/20`
- [ ] Ícones Material Symbols com wrapper gradiente (serviços) ou glass (redes)
- [ ] Grids responsive: `grid-cols-1 md:grid-cols-N`
- [ ] Header de seção com badge + h2 + p, centralizado, `max-w-3xl`
- [ ] Animações de hover: `scale-[1.02]` + `shadow` em cards
- [ ] Padding vertical: `py-24` nas seções
- [ ] Conteúdo centralizado com `max-w-6xl mx-auto`
