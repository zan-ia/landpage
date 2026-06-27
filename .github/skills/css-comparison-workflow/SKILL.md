---
name: css-comparison-workflow
description: "Compara e corrige diferenças visuais entre o dev local (localhost:5173) e o site LIVE (www.zan.ia.br). Use quando: precisar verificar equivalência visual, depurar estilos que divergem entre DEV e LIVE, ou garantir que o build local está 100% idêntico ao site de produção."
argument-hint: "Descreva qual elemento ou seção está diferente (ex: 'sombra do CTA WhatsApp', 'backdrop-filter dos cards')"
---

# Skill: CSS Comparison Workflow — DEV vs LIVE (SvelteKit)

Workflow para identificar e corrigir diferenças de estilo computado entre o ambiente de desenvolvimento (`localhost:5173`) e o site LIVE (`https://www.zan.ia.br/`).

## Quando Usar
- Após modificar CSS em componentes Svelte
- Quando um elemento visual parece diferente da produção
- Para verificação regressiva antes de deploy
- Após atualizar `src/lib/app.css`

## Ferramentas
- **Playwright** (via VS Code browser tools) — para extrair `getComputedStyle`
- **Vite dev server**: `npm run dev` → `localhost:5173`
- **Preview build**: `npm run build && npm run preview` → `localhost:4173`

## Arquitetura CSS (SvelteKit)

```
GLOBAL:  src/lib/app.css → design tokens, reset, .glass-panel, @keyframes
SCOPED:  cada *.svelte → <style> com hash único (sem conflitos)
BUILD:   Vite extrai CSS → build/_app/immutable/assets/*.css (com hash)
```

⚠️ **Scoped CSS**: Cada componente tem seu próprio `<style>` com namespacing automático. Não há conflitos de classes entre componentes.

### Armadilhas Comuns
| Problema | Sintoma | Causa Provável |
|----------|---------|----------------|
| `backdrop-filter` errado | Cards sem blur | `.glass-panel` não aplicado ou sobrescrito no scoped CSS |
| `box-shadow` errado | Sombra diferente | Tokens `--shadow-*` não usados ou valor hard-coded |
| `font-family` errada | Fonte inconsistente | Não usando `var(--font-body)` / `var(--font-display)` |
| `opacity` errada | Texto muito claro/escuro | Valor hard-coded em vez de usar opacidade padrão (0.7) |

## Fluxo de Verificação

### 1. Abrir ambas as páginas
```
DEV:  http://localhost:5173  (dev server com HMR)
LIVE: https://www.zan.ia.br/
```

### 2. Extrair estilos computados

```javascript
const props = ['boxShadow', 'backdropFilter', 'backgroundColor', 'border',
               'opacity', 'fontFamily', 'fontSize', 'fontWeight', 'lineHeight',
               'color', 'gap', 'padding', 'borderRadius'];
```

### 3. Propriedades mais sensíveis a divergência
| Propriedade | Onde verificar |
|-------------|---------------|
| `backdropFilter` | Todos `.glass-panel` — deve ser `blur(24px)` |
| `boxShadow` | Cards, CTA WhatsApp (`--shadow-whatsapp`) |
| `fontFamily` | `.hero__title` → `Space Grotesk`, corpo → `Geist` |
| `color` | Texto → `rgb(220, 225, 251)` (`--color-on-surface`) |
| `opacity` | Texto secundário → `0.7` |

### 4. Svelte Scoped CSS Específico

O Svelte adiciona um hash único a cada classe com escopo:
```css
/* Código fonte */
.testimonial__card { color: red; }

/* Compilado */
.testimonial__card.svelte-1jhcrt0 { color: red; }
```

Isso significa que seletores **nunca** conflitam entre componentes. Mas estilos globais de `app.css` podem ser sobrescritos por scoped CSS com mesma especificidade.

## Correções Comuns

### Verificar se `.glass-panel` global está sendo aplicado
1. Inspecione o elemento no devtools
2. Confirme que a classe `glass-panel` aparece
3. Se o `backdrop-filter` estiver errado, o scoped CSS do componente pode estar sobrescrevendo

### Verificar tokens
- `var(--color-on-surface)` deve resolver para `#dce1fb`
- `var(--font-body)` deve resolver para `'Geist', sans-serif`
- Se não resolver, verifique se `app.css` está sendo carregado (`+layout.svelte`)

## Referências
- `src/lib/app.css` — CSS global (design tokens + reset + glass-panel)
- `src/lib/components/*.svelte` — componentes com scoped CSS
- `build/_app/immutable/assets/*.css` — CSS compilado (pós-Vite)
