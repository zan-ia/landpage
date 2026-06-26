---
applyTo: "build/**/*.css, build/index.html"
---

# Regras e Padrões de CSS

## 1. Design Tokens (Variáveis CSS)

Sempre use as variáveis CSS definidas em `:root`. Nunca use valores hard-coded para cores, fontes ou espaçamentos.

```css
/* ✅ Correto */
color: var(--color-on-surface);
background: var(--color-surface-container);
font-family: var(--font-geist);

/* ❌ Errado */
color: #e0e0e0;
background: #1e1e2e;
font-family: 'Geist', sans-serif;
```

### Tokens Disponíveis

| Categoria | Tokens |
|-----------|--------|
| **Cores** | `--color-primary`, `--color-primary-container`, `--color-on-primary`, `--color-secondary`, `--color-surface`, `--color-surface-container`, `--color-on-surface`, `--color-outline` |
| **Tipografia** | `--font-space-grotesk` (displays), `--font-geist` (corpo), `--font-jetbrains-mono` (código) |
| **Espaçamento** | `--spacing-xs` (4px), `--spacing-sm` (8px), `--spacing-md` (16px), `--spacing-lg` (24px), `--spacing-xl` (32px), `--spacing-2xl` (48px) |
| **Bordas** | `--radius-sm` (8px), `--radius-md` (12px), `--radius-lg` (16px), `--radius-xl` (24px) |
| **Sombras** | `--shadow-sm`, `--shadow-md`, `--shadow-lg`, `--shadow-xl` |

## 2. Nomenclatura de Classes

Use **kebab-case** para classes CSS customizadas. Classes Tailwind são utilitárias e seguem a convenção do framework.

```css
/* ✅ Correto */
.hero-section { }
.glass-panel { }
.scanning-line { }
.animate-fade-in-up { }

/* ❌ Errado */
.heroSection { }
.glassPanel { }
.scanning_line { }
```

### Prefixos Semânticos

| Prefixo | Uso | Exemplo |
|---------|-----|---------|
| `.hero-*` | Seção hero principal | `.hero-title`, `.hero-cta` |
| `.section-*` | Seções genéricas | `.section-header`, `.section-grid` |
| `.glass-*` | Efeitos glassmorphism | `.glass-panel`, `.glass-nav` |
| `.animate-*` | Animações | `.animate-fadeInUp`, `.animate-glow` |
| `.icon-*` | Ícones ou wrappers de ícone | `.icon-wrapper`, `.icon-large` |

## 3. Composição de Estilos

### Classes Utilitárias vs CSS Customizado

```html
<!-- ✅ Preferir classes Tailwind para layout e espaçamento simples -->
<section class="relative py-24 px-4 max-w-6xl mx-auto">

<!-- ✅ CSS customizado apenas para efeitos complexos ou reutilizáveis -->
<style>
  .glass-panel {
    background: rgba(255, 255, 255, 0.05);
    backdrop-filter: blur(12px);
    border: 1px solid rgba(255, 255, 255, 0.1);
  }
</style>
```

### Regra de Decisão

| Situação | Abordagem |
|----------|-----------|
| Layout (flex, grid, padding, margin) | Tailwind CDN |
| Cores, tipografia | Variáveis CSS via Tailwind ou inline |
| Efeitos complexos (glass, blur, gradientes) | CSS customizado |
| Animações / keyframes | CSS customizado |
| Estado único (hover específico) | Tailwind com `hover:` |
| Padrão reutilizável em múltiplos elementos | Classe CSS customizada |

## 4. Media Queries e Responsividade

```css
/* ✅ Breakpoint único em 768px para mobile */
@media (max-width: 768px) {
  /* ajustes mobile */
}

/* ✅ Mobile-first sempre que possível */
.grid-cols-1 md:grid-cols-3 /* Tailwind já faz mobile-first */
```

- **Breakpoint único:** 768px (mobile)
- **Mobile-first:** Comece do layout mobile e expanda para desktop
- **Evite** múltiplos breakpoints customizados — use as classes `md:*` do Tailwind

## 5. Animações

Defina animações reutilizáveis com `@keyframes`. Prefira animações sutis (300-500ms).

```css
@keyframes fade-in-up {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes pulse-glow {
  0%, 100% { opacity: 0.4; }
  50% { opacity: 0.8; }
}

@keyframes scanning-line {
  0% { top: -10%; }
  100% { top: 110%; }
}

.animate-fade-in-up {
  animation: fade-in-up 0.5s ease-out forwards;
}

.animate-pulse-glow {
  animation: pulse-glow 3s ease-in-out infinite;
}
```

### Diretrizes de Animação
- Duração: 300ms (micro-interações), 500ms (entrada), 3-6s (contínuas)
- Timing function: `ease-out` para entradas, `ease-in-out` para loops
- `prefers-reduced-motion`: Sempre respeitar com `@media (prefers-reduced-motion: reduce)`
- Não animar `width`, `height`, `top`, `left` — prefira `transform` e `opacity`

## 6. Organização de Estilos no HTML

Mantenha os estilos em ordem dentro do `<style>`:

```css
/* 1. Design Tokens / Variáveis */
:root { /* ... */ }

/* 2. Estilos Base (body, tipografia) */
body { /* ... */ }

/* 3. Componentes Reutilizáveis (.glass-panel, .animate-*) */
.glass-panel { /* ... */ }

/* 4. Seções Específicas (na ordem da página) */
/* --- Hero --- */
/* --- Solutions --- */
/* --- Testimonials --- */
/* --- Footer --- */

/* 5. Media Queries */
@media (max-width: 768px) { /* ... */ }

/* 6. Animações */
@keyframes fade-in-up { /* ... */ }
```

## 7. Especificidade

Mantenha a especificidade baixa. Prefira classes únicas em vez de seletores aninhados.

```css
/* ✅ Correto — classe direta */
.btn-primary { }

/* ❌ Evitar — alta especificidade */
section div button.primary { }

/* ❌ Evitar — aninhamento desnecessário */
.glass-panel .glass-panel-content .glass-panel-title { }
```

## 8. Validação

- [ ] Sem valores hard-coded de cores (sempre usar `var(--color-*)`)
- [ ] Sem `!important` (exceto em utilitários de override intencional)
- [ ] Animações respeitam `prefers-reduced-motion`
- [ ] Imagens com `width`/`height` explícitos (evitar CLS)
- [ ] Fontes com `font-display: swap`
