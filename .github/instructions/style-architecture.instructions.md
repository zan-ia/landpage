---
applyTo: "build/**"
---

# Arquitetura de Estilos — Padrões de Composição

## 1. Sistema de Design Tokens

O tema visual é baseado em **Material Design 3** (dark mode). Todos os tokens são definidos em `:root` no CSS.

### Paleta de Cores

```css
:root {
  /* Primária — tons de azul/púrpura para destaque */
  --color-primary: #7c5cfc;
  --color-primary-container: #4a2fb3;
  --color-on-primary: #ffffff;

  /* Superfície — tons escuros para backgrounds */
  --color-surface: #0a0a14;
  --color-surface-container: #12121f;
  --color-on-surface: #e8e8ed;

  /* Secundária — tons ciano para acentos */
  --color-secondary: #00d4b8;

  /* Borda/Divisores */
  --color-outline: rgba(255, 255, 255, 0.08);
}
```

### Como Usar

```html
<!-- ✅ Background de cards -->
<div class="bg-[var(--color-surface-container)]">

<!-- ✅ Texto com opacidade para secundário -->
<p class="text-[var(--color-on-surface)]/70">

<!-- ✅ Badges com primary + transparência -->
<span class="bg-[var(--color-primary)]/10 text-[var(--color-primary)]">
```

### Opacidades Padrão

| Uso | Opacidade | Exemplo |
|-----|-----------|---------|
| Texto principal | 100% | `var(--color-on-surface)` |
| Texto secundário | 70% | `var(--color-on-surface)/70` |
| Texto terciário | 50% | `var(--color-on-surface)/50` |
| Borda sutil | 8-10% | `var(--color-outline)` ou `var(--color-on-surface)/10` |
| Badge bg | 10% | `var(--color-primary)/10` |
| Badge border | 20% | `var(--color-primary)/20` |

## 2. Padrão Glass Panel

O componente visual mais usado no projeto.

```html
<div class="
  glass-panel
  rounded-2xl            /* bordas arredondadas */
  p-8                    /* padding interno */
  transition-all         /* hover suave */
  duration-500
  hover:scale-[1.02]     /* leve zoom ao hover */
  hover:shadow-2xl
  flex flex-col          /* layout interno flex */
  items-start
  gap-4
">
```

```css
.glass-panel {
  background: rgba(255, 255, 255, 0.03);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
  border: 1px solid rgba(255, 255, 255, 0.06);
}
```

### Variações do Glass Panel

| Variação | Quando Usar | Modificações |
|----------|-------------|--------------|
| **Card de serviço** | Grid de soluções | `hover:scale-[1.02]`, ícone gradiente no topo |
| **Depoimento** | Carrossel | Largura fixa (`min-w-[340px]`), snap scroll |
| **Navbar** | Header fixo | `backdrop-filter: blur(20px)`, sem hover |
| **Footer** | Rodapé | Sem hover, opacidade reduzida |

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
  rgba(124, 92, 252, 0.15) 0%,
  transparent 60%
);
```

## 4. Padrão de Grid Layouts

```html
<!-- Grid de 3 colunas (serviços) -->
<div class="grid grid-cols-1 md:grid-cols-3 gap-6">
  <!-- card 1 -->
  <!-- card 2 -->
  <!-- card 3 -->
</div>

<!-- Grid de 2 colunas (sobre, contato) -->
<div class="grid grid-cols-1 md:grid-cols-2 gap-12">
  <!-- texto -->
  <!-- imagem/card -->
</div>

<!-- Grid de 4 colunas (diferenciais, métricas) -->
<div class="grid grid-cols-2 md:grid-cols-4 gap-6">
  <!-- item -->
</div>
```

### Responsividade do Grid

| Telas | Comportamento |
|-------|---------------|
| `< 768px` | `grid-cols-1` (mobile) |
| `>= 768px` | `grid-cols-2`, `3` ou `4` conforme densidade |

## 5. Padrão de Badges / Tags

```html
<span class="
  inline-block
  px-4 py-1.5
  rounded-full
  bg-[var(--color-primary)]/10
  text-[var(--color-primary)]
  text-sm font-medium
  border border-[var(--color-primary)]/20
">
  badge_label
</span>
```

## 6. Padrão de Header de Seção

Toda seção deve ter este cabeçalho consistente:

```html
<div class="text-center max-w-3xl mx-auto mb-16">
  <!-- Badge opcional -->
  <span class="inline-block px-4 py-1.5 rounded-full ...">
    badge
  </span>

  <!-- Título -->
  <h2 class="text-3xl md:text-4xl lg:text-5xl font-bold text-[var(--color-on-surface)]">
    Título da Seção
  </h2>

  <!-- Subtítulo -->
  <p class="mt-4 text-lg text-[var(--color-on-surface)]/70 max-w-2xl mx-auto">
    Descrição ou subtítulo.
  </p>
</div>
```

## 7. Padrão de Tipografia

| Elemento | Fonte | Peso | Tamanho |
|----------|-------|------|---------|
| Título hero (h1) | `font-space-grotesk` | 700 (bold) | `text-5xl` mobile, `text-7xl` desktop |
| Título seção (h2) | `font-geist` | 700 (bold) | `text-3xl` mobile, `text-5xl` desktop |
| Subtítulo | `font-geist` | 400 (regular) | `text-lg` |
| Card title (h3) | `font-geist` | 600 (semibold) | `text-xl` |
| Corpo | `font-geist` | 400 (regular) | `text-base` ou `text-sm` |
| Código | `font-jetbrains-mono` | 400 | `text-sm` |
| Badge | `font-geist` | 500 (medium) | `text-sm` |

## 8. Padrão de Ícones (Material Symbols)

```html
<!-- Ícone standalone -->
<span class="material-symbols-outlined text-2xl">icon_name</span>

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
