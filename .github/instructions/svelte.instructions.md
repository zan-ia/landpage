---
name: "Svelte 5 Runes"
description: "Svelte 5 Runes mode conventions for .svelte files. Use when: writing or editing Svelte components, using reactivity, props, effects, or derived values."
applyTo: "src/**/*.svelte"
---

# Svelte 5 Runes — Convenções

Todos os componentes Svelte usam **Runes mode** (Svelte 5). Não use padrões de Svelte 4 (`export let`, `onMount`, `onDestroy`, reactive `$:`).

## Runes Obrigatórias

### `$props()` — Props do Componente

```svelte
<!-- ✅ Correto -->
<script lang="ts">
  let { title, items = [], variant = "default" } = $props();
</script>

<!-- ❌ Errado (Svelte 4 legacy) -->
<script lang="ts">
  export let title: string;
  export let items: any[] = [];
</script>
```

### `$state()` — Estado Reativo

```svelte
<!-- ✅ Correto -->
<script lang="ts">
  let isOpen = $state(false);
  let activeIndex = $state(0);
</script>

<!-- ❌ Errado -->
<script lang="ts">
  let isOpen = false;  // não é reativo sem $state
</script>
```

### `$derived()` — Valores Computados

```svelte
<!-- ✅ Correto -->
<script lang="ts">
  let count = $state(0);
  let doubled = $derived(count * 2);
</script>

<!-- ❌ Errado -->
<script lang="ts">
  let count = 0;
  $: doubled = count * 2;  // Svelte 4 legacy
</script>
```

### `$effect()` — Efeitos Colaterais

```svelte
<!-- ✅ Correto — sempre com cleanup quando necessário -->
<script lang="ts">
  $effect(() => {
    const handler = () => console.log('scroll');
    window.addEventListener('scroll', handler);
    return () => window.removeEventListener('scroll', handler);
  });
</script>

<!-- ❌ Errado (Svelte 4 legacy) -->
<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  onMount(() => {
    window.addEventListener('scroll', handler);
  });
  onDestroy(() => {
    window.removeEventListener('scroll', handler);
  });
</script>
```

## Regras de Ouro

1. **NUNCA** use `export let` — sempre `$props()`
2. **NUNCA** use `onMount` / `onDestroy` — use `$effect()` com return de cleanup
3. **NUNCA** use `$:` reactive statements — use `$derived()` ou `$effect()`
4. **SEMPRE** retorne a função de cleanup em `$effect()` quando registrar listeners, timers ou subscriptions
5. **SEMPRE** use `<script lang="ts">` — TypeScript é obrigatório em componentes
6. **SEMPRE** use `{base}` do `$app/paths` para referências a assets: `src="{base}/assets/images/..."`

## Importações

```svelte
<script lang="ts">
  import { base } from '$app/paths';           // para assets
  import { page } from '$app/stores';           // para dados de rota (se necessário)
</script>
```

## Scoped CSS

Cada componente tem seu próprio `<style>` com escopo automático do Svelte. Use:
- Design tokens: `var(--color-*)`, `var(--font-*)`, `var(--spacing-*)`
- BEM-like naming: `componente__elemento--modificador`
- Media queries para responsividade (breakpoint 768px)
