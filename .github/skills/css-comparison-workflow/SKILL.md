---
context: fork
name: css-comparison-workflow
description: "Compara e corrige diferenças visuais entre o build local e o site LIVE (www.zan.ia.br). Use quando: precisar verificar equivalência visual, depurar estilos que divergem entre LOCAL e LIVE, ou garantir que o build local está 100% idêntico ao site de produção."
argument-hint: "Descreva qual elemento ou seção está diferente (ex: 'sombra do CTA WhatsApp', 'backdrop-filter dos cards')"
---

# Skill: CSS Comparison Workflow — Local vs LIVE

Workflow para identificar e corrigir diferenças de estilo computado entre o build local (`file:///home/.../build/index.html`) e o site LIVE (`https://www.zan.ia.br/`).

## Quando Usar
- Após migrar CSS (ex: Tailwind CDN → CSS vanilla)
- Quando um elemento visual parece diferente da produção
- Para verificação regressiva de equivalência visual
- Antes de fazer deploy

## Ferramentas
- **Playwright** (via VS Code browser tools) — para extrair `getComputedStyle` de ambos os lados
- **Variáveis CSS** em `build/assets/css/base/variables.css`

## CSS Load Order (CRÍTICO — causa raiz da maioria das divergências)

```
LOCAL:  tailwind-replacement.css (primeiro) → main.css (segundo, via @imports)
LIVE:   <style> custom CSS (primeiro) → Tailwind CDN utilities (segundo)
```

⚠️ **Consequência**: Em LOCAL, `main.css` (importa glass-panel.css, responsive.css, cta.css, etc.) carrega DEPOIS de `tailwind-replacement.css`. Isso significa que regras de mesma especificidade em `main.css` SOBRESCREVEM as utilitárias em `tailwind-replacement.css`.

### Armadilhas Comuns
| Problema | Sintoma | Por que acontece |
|----------|---------|------------------|
| `backdrop-filter` errado | Cards sem blur | `glass-panel.css` (via main.css) sobrescreve `backdrop-blur-xl` (via tailwind-replacement.css) |
| `box-shadow` errado | Sombra diferente | `responsive.css` ou `cta.css` (via main.css) tem regra duplicada que sobrescreve `tailwind-replacement.css` |
| `border` sumindo | Borda não aparece | `glass-panel.css` sem `border` ou ordem de carregamento invertida |
| `opacity` errada | Texto muito transparente | `typography.css` (via main.css) define `opacity` em `.text-on-surface-variant` |

## Fluxo de Verificação

### 1. Abrir ambas as páginas
```
LOCAL: file:///home/zanbook/Projects/ZanDevs/zania-website/build/index.html
LIVE:  https://www.zan.ia.br/
```

### 2. Extrair estilos computados
Use Playwright para comparar propriedades-chave:

```javascript
const props = ['boxShadow', 'backdropFilter', 'backgroundColor', 'border', 
               'letterSpacing', 'opacity', 'borderLeft',
               'fontFamily', 'fontSize', 'fontWeight', 'lineHeight'];
```

### 3. Propriedades mais divergentes (histórico)
| Propriedade | Elementos Afetados |
|-------------|-------------------|
| `boxShadow` | CTA WhatsApp (`shadow-[#25D366]/20`), cards (`shadow-lg`, `shadow-primary/10`) |
| `backdropFilter` | Todos `.glass-panel` (deve ser `blur(12px)`), testimonial cards com `backdrop-blur-xl` (deve ser `blur(24px)`) |
| `border` | Cards, CTA, authority counters |
| `letterSpacing` | `tracking-tighter` (-0.05em), `tracking-widest` (0.1em) |
| `opacity` | Elementos com `.opacity-80` (0.8) ou `.opacity-90` (0.9) |
| `backgroundColor` | Sections (devem ser `rgba(0,0,0,0)`), footer |

### 4. Técnica do `:where()` para backdrop-filter
Para que `backdrop-blur-xl` (blur 24px) possa sobrescrever o `backdrop-filter` base do `.glass-panel` (blur 12px):

```css
/* Em glass-panel.css — especificidade ZERO */
:where(.glass-panel) {
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
}
```

`:where()` tem especificidade 0, então qualquer classe `.backdrop-blur-*` (especificidade 0,1,0) vence naturalmente.

### 5. Cache Busting
Ao testar localmente com `file://`, o navegador pode cachear CSS. Adicione `?v=N` aos links:
```html
<link rel="stylesheet" href="assets/css/tailwind-replacement.css?v=2">
<link rel="stylesheet" href="assets/css/main.css?v=2">
```

## Correções Comuns

### Sombra do WhatsApp CTA
O valor correto (Tailwind `shadow-lg` + cor `#25D366` a 20%):
```css
box-shadow: 0 10px 15px -3px rgba(37, 211, 102, 0.2),
            0 4px 6px -4px rgba(37, 211, 102, 0.2);
```

**Verificar sempre em**: `tailwind-replacement.css`, `responsive.css`, `cta.css`, `variables.css`

### Glass-panel backdrop
Valor base: `blur(12px)` (`.glass-panel`)
Override: `blur(24px)` (`.backdrop-blur-xl` nos testimonial cards)

## Referências
- `build/assets/css/tailwind-replacement.css` — utilitárias Tailwind em CSS puro
- `build/assets/css/main.css` — entry point dos módulos (carregado por último!)
- `build/assets/css/utilities/glass-panel.css` — componente glass panel
- `build/assets/css/utilities/responsive.css` — utilities extras (pode sobrescrever!)
- `build/assets/css/components/cta.css` — componente CTA (pode sobrescrever shadows!)
- `build/assets/css/base/variables.css` — design tokens
