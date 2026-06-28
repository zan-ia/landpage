---
name: criar-section
description: "Cria uma nova seção como componente Svelte na landing page Zan.IA seguindo os padrões visuais existentes. Gera o .svelte completo com glass panels, gradientes, tipografia consistente e scoped CSS."
argument-hint: "Nome da seção e descrição do conteúdo (ex: 'pricing - Seção de preços com 3 planos')"
---

# Skill: Criar Nova Seção (SvelteKit)

Cria um novo componente Svelte em `src/lib/components/` mantendo consistência visual.

## Template de Componente

```svelte
<script lang="ts">
  // Dados da seção
  const items = [
    { icon: "smart_toy", title: "Item 1", desc: "Descrição do item 1." },
    { icon: "psychology", title: "Item 2", desc: "Descrição do item 2." },
    { icon: "auto_awesome", title: "Item 3", desc: "Descrição do item 3." }
  ];
</script>

<!-- ════════════════════════════════════════════ -->
<!-- NOME_DA_SEÇÃO -->
<!-- ════════════════════════════════════════════ -->
<section id="nome-secao" class="nome-secao" aria-label="Nome da Seção">
  <div class="nome-secao__inner">
    <!-- Header -->
    <div class="nome-secao__header">
      <h2 class="nome-secao__title">Título da Seção</h2>
      <p class="nome-secao__subtitle">Descrição ou subtítulo.</p>
    </div>

    <!-- Grid de cards -->
    <div class="nome-secao__grid">
      {#each items as item}
        <div class="nome-secao__card glass-panel">
          <span class="material-symbols-outlined nome-secao__icon">{item.icon}</span>
          <h3 class="nome-secao__card-title">{item.title}</h3>
          <p class="nome-secao__card-desc">{item.desc}</p>
        </div>
      {/each}
    </div>
  </div>
</section>

<style>
  .nome-secao {
    padding: 96px var(--spacing-margin-mobile);
  }
  .nome-secao__inner {
    max-width: var(--spacing-container-max);
    margin: 0 auto;
  }
  .nome-secao__header {
    text-align: center;
    margin-bottom: 64px;
    display: flex;
    flex-direction: column;
    gap: 16px;
  }
  .nome-secao__title {
    font-family: var(--font-display);
    font-size: var(--font-size-headline-lg);
    color: var(--color-on-surface);
  }
  .nome-secao__subtitle {
    font-family: var(--font-body);
    font-size: var(--font-size-body-md);
    color: var(--color-on-surface);
    opacity: 0.7;
  }
  .nome-secao__grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: var(--spacing-gutter);
  }
  @media (min-width: 768px) {
    .nome-secao__grid {
      grid-template-columns: repeat(3, 1fr);
    }
  }
  .nome-secao__card {
    display: flex;
    flex-direction: column;
    gap: 24px;
    padding: 32px;
    transition: box-shadow 0.5s ease, transform 0.5s ease;
  }
  .nome-secao__card:hover {
    box-shadow: 0 0 30px rgba(0, 224, 255, 0.15);
  }
  .nome-secao__card-title {
    font-family: var(--font-display);
    font-size: var(--font-size-headline-md);
    color: var(--color-on-surface);
  }
  .nome-secao__card-desc {
    font-family: var(--font-body);
    font-size: var(--font-size-body-md);
    color: var(--color-on-surface);
    opacity: 0.7;
  }
</style>
```

## Design Tokens

| Token | Uso |
|-------|-----|
| `--color-background` | Background da seção |
| `--color-surface-container` | Background de cards |
| `--color-primary` | Destaques, ícones |
| `--color-on-surface` | Texto principal |
| `--font-display` | Títulos |
| `--font-body` | Corpo, descrições |
| `--font-code` | Dados, badges |
| `--spacing-gutter` | Gap do grid |

## Procedimento

1. **Analise** componentes existentes em `src/lib/components/` para padrão visual
2. **Crie** o arquivo `src/lib/components/NomeSecao.svelte`
3. **Importe** no `+page.svelte`: `import NomeSecao from '$lib/components/NomeSecao.svelte';`
4. **Adicione** o componente na ordem desejada: `<NomeSecao />`
5. **Grid**: 1 col mobile, 2-3 cols desktop conforme densidade
6. **Glass panels**: Use a classe global `.glass-panel`

## Checklist de Consistência

- [ ] Usa `.glass-panel` para cards
- [ ] Tipografia com `var(--font-display)` / `var(--font-body)`
- [ ] Scoped `<style>` no próprio componente
- [ ] Design tokens (`var(--color-*)`, nunca hex)
- [ ] Responsivo (mobile-first, breakpoint 768px)
- [ ] Ícones Material Symbols
- [ ] Padding vertical 96px
- [ ] Max-width com `var(--spacing-container-max)`
