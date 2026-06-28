---
name: criar-pagina-institucional
description: 'Cria novas páginas institucionais como componentes Svelte seguindo o padrão visual do site Zan.IA (SvelteKit). Use quando: precisar adicionar landing pages, páginas de serviço, páginas institucionais, ou seções que sigam o design system existente (glass panels, paleta Material Design 3, tipografia Space Grotesk/Geist/JetBrains Mono).'
argument-hint: 'Descrição da página a criar (ex: "página de preços", "landing page do serviço de chatbots")'
user-invocable: true
disable-model-invocation: false
---

# Criar Página Institucional — Zan.IA (SvelteKit)

Gera um novo componente Svelte ou rota seguindo o design system Zan.IA.

## Quando Usar
- Adicionar novas páginas ao site SvelteKit
- Criar landing pages de serviço
- Adicionar seções institucionais (ex: "Política de Privacidade")
- Qualquer página que precise seguir o padrão visual existente

## Design System (consulte `src/lib/app.css`)

### Design Tokens
```css
/* Cores - Material Design 3 Dark */
--color-surface: #0c1324;
--color-surface-container: #191f31;
--color-background: #0c1324;
--color-primary: #baf2ff;
--color-primary-container: #00e0ff;
--color-on-surface: #dce1fb;
--color-on-primary: #00363f;
--color-outline-variant: #3b494c;

/* Fontes */
--font-display: 'Space Grotesk', sans-serif;
--font-body: 'Geist', sans-serif;
--font-code: 'JetBrains Mono', monospace;

/* Espaçamentos */
--spacing-gutter: 24px;
--spacing-margin-mobile: 16px;
--spacing-margin-desktop: 64px;
```

### Componentes Reutilizáveis

| Componente | Classe | Descrição |
|---|---|---|
| **Glass Panel** | `.glass-panel` | Global em `app.css` — blur(24px), border sutil |
| **Ícone Material** | `<span class="material-symbols-outlined">nome</span>` | Google Material Symbols |
| **Grid de Cards** | CSS grid 3 cols (responsive) | `grid-template-columns: repeat(3, 1fr)` |

### Template de Componente Svelte

```svelte
<script lang="ts">
  // Props e estado
  interface Props {
    title: string;
  }
  let { title }: Props = $props();
</script>

<section class="nova-pagina" aria-label="{title}">
  <div class="nova-pagina__inner">
    <div class="nova-pagina__header">
      <h2 class="nova-pagina__title">{title}</h2>
      <p class="nova-pagina__subtitle">Subtítulo descritivo</p>
    </div>

    <div class="nova-pagina__grid">
      <div class="nova-pagina__card glass-panel">
        <span class="material-symbols-outlined nova-pagina__icon">icon_name</span>
        <h3 class="nova-pagina__card-title">Título do Card</h3>
        <p class="nova-pagina__card-desc">Descrição</p>
      </div>
    </div>
  </div>
</section>

<style>
  .nova-pagina {
    padding: 96px var(--spacing-margin-mobile);
  }
  .nova-pagina__inner {
    max-width: var(--spacing-container-max);
    margin: 0 auto;
  }
  .nova-pagina__header {
    text-align: center;
    margin-bottom: 64px;
    display: flex;
    flex-direction: column;
    gap: 16px;
  }
  .nova-pagina__title {
    font-family: var(--font-display);
    font-size: var(--font-size-headline-lg);
    color: var(--color-on-surface);
  }
  .nova-pagina__subtitle {
    font-family: var(--font-body);
    font-size: var(--font-size-body-md);
    color: var(--color-on-surface);
    opacity: 0.7;
  }
  .nova-pagina__grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: var(--spacing-gutter);
  }
  @media (min-width: 768px) {
    .nova-pagina__grid {
      grid-template-columns: repeat(3, 1fr);
    }
  }
  .nova-pagina__card {
    display: flex;
    flex-direction: column;
    gap: 24px;
    padding: 32px;
  }
  .nova-pagina__card-title {
    font-family: var(--font-display);
    font-size: var(--font-size-headline-md);
    color: var(--color-on-surface);
  }
  .nova-pagina__card-desc {
    font-family: var(--font-body);
    font-size: var(--font-size-body-md);
    color: var(--color-on-surface);
    opacity: 0.7;
  }
</style>
```

## Procedimento

1. **Briefing**: Entenda o propósito da página
2. **Crie o componente**: `src/lib/components/NomePagina.svelte`
3. **Registre a rota** (se for página separada):
   - Crie `src/routes/nome-pagina/+page.svelte`
   - Adicione `src/routes/nome-pagina/+page.js` com `export const prerender = true;`
4. **Use scoped CSS**: `<style>` no próprio componente
5. **Design tokens**: Sempre `var(--color-*)`, `var(--font-*)`, `var(--spacing-*)`
6. **Glass panels**: Use a classe global `.glass-panel`
7. **Responsividade**: Mobile-first, breakpoint 768px
8. **Ícones**: Material Symbols Outlined

## Arquivos de Referência
- `src/lib/components/*.svelte` — componentes existentes (padrão visual)
- `src/lib/app.css` — design tokens globais
- `AGENTS.md` — convenções de código, stack
- `docs/INSTITUCIONAL.md` — conteúdo institucional
