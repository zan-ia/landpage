---
description: "Use when: navigating the project structure, adding new files/directories, understanding the tech stack layout, or refactoring folder organization. Covers src/ structure, build conventions, naming, and dependency rules."
applyTo: "src/**"
---

# Organização de Projeto — SvelteKit

## 1. Estrutura de Diretórios

```
zania-website/
├── src/                            # 🔧 Fonte (SvelteKit)
│   ├── app.html                    # Template HTML (fontes, meta tags)
│   ├── app.d.ts                    # Tipos TypeScript
│   ├── lib/
│   │   ├── app.css                 # CSS global (design tokens + reset + utilitários)
│   │   ├── index.ts                # Re-exports
│   │   └── components/             # Componentes Svelte (cada um com <style> escopado)
│   │       ├── Header.svelte
│   │       ├── Hero.svelte
│   │       ├── Authority.svelte
│   │       ├── Solutions.svelte
│   │       ├── Differential.svelte
│   │       ├── Testimonials.svelte
│   │       ├── CTA.svelte
│   │       └── Footer.svelte
│   └── routes/
│       ├── +layout.js              # Config: prerender = true
│       ├── +layout.svelte          # Layout principal (Header + Footer)
│       └── +page.svelte            # Home page (monta todos os componentes)
├── static/                         # Assets estáticos (copiados para build/)
│   ├── robots.txt
│   └── assets/
│       └── images/                 # Imagens (referenciar como /assets/images/...)
├── build/                          # 🚀 Output do build (gerado, NÃO versionar)
│   ├── index.html
│   ├── 404.html
│   └── _app/immutable/...          # JS/CSS com hash
├── docs/                           # 📄 Documentação institucional
│   └── INSTITUCIONAL.md
├── .github/                        # Agentes, skills, instruções, CI/CD
│   ├── agents/
│   ├── instructions/
│   ├── prompts/
│   ├── skills/
│   └── workflows/
│       └── deploy.yml              # Build + Deploy GitHub Pages
├── svelte.config.js                # Config SvelteKit + adapter-static
├── vite.config.ts                  # Config Vite
├── package.json                    # Dependências e scripts
├── AGENTS.md                       # Diretrizes para AI agents
└── README.md
```

### Convenções de Diretório

| Caminho | Propósito | Editar? |
|---------|-----------|---------|
| `src/lib/components/` | Componentes Svelte (fonte) | ✅ Sim |
| `src/lib/app.css` | CSS global (tokens + reset) | ✅ Sim |
| `src/routes/` | Rotas e layout SvelteKit | ✅ Sim |
| `src/app.html` | Template HTML (meta, fontes) | ✅ Sim |
| `static/` | Assets estáticos | ✅ Sim |
| `build/` | Output de produção (gerado) | ❌ Não |
| `docs/` | Documentação institucional | ✅ Sim |
| `.github/` | Sistema agentico + CI/CD | ✅ Sim |

## 2. Stack Tecnológica

| Camada | Tecnologia |
|--------|-----------|
| **Framework** | SvelteKit 5 (Runes mode) |
| **Build** | Vite + `@sveltejs/adapter-static` |
| **Markup** | Componentes Svelte com scoped CSS |
| **Estilos** | Scoped `<style>` por componente + `app.css` global |
| **Ícones** | Google Material Symbols Outlined |
| **Tipografia** | Space Grotesk, Geist, JetBrains Mono (Google Fonts) |
| **Tema** | Dark mode (Material Design 3) |
| **Deploy** | GitHub Pages + GitHub Actions |
| **Sem Tailwind** | CSS vanilla com design tokens |

## 3. Scripts

```bash
npm run dev       # Dev server em localhost:5173 (HMR)
npm run build     # Build de produção → build/
npm run preview   # Preview do build (localhost:4173)
npm run check     # Type-check com svelte-check
```

## 4. Convenções de Código

### Componentes Svelte (Runes Mode)
- `$state()` para variáveis reativas
- `$effect()` para efeitos colaterais
- `$props()` para props de componente
- `bind:this={elementRef}` para refs de DOM

### CSS
- Scoped `<style>` em cada componente (sem conflitos)
- Classes BEM-like: `componente__elemento--modificador`
- Design tokens via `var(--color-*)`, `var(--font-*)`, `var(--spacing-*)`
- Breakpoint único: 768px (`@media (min-width: 768px)`)

### Regras de Dependência (Direção dos Imports)

```
src/lib/components/  ──import──▶  src/lib/  (app.css, index.ts)
src/lib/components/  ──import──▶  $lib/components/  (outros componentes)
src/routes/          ──import──▶  $lib/components/  (✅ permitido)
src/lib/components/  ──import──▶  src/routes/       (❌ proibido)
```

- **Componentes em `src/lib/components/` NUNCA importam rotas (`src/routes/`)** — a dependência é unidirecional
- **Rotas importam componentes**, nunca o inverso
- `app.css` é importado pelo layout (`+layout.svelte`), não por componentes individuais
- Componentes podem importar outros componentes via `$lib/components/Nome.svelte`

### Imports
```typescript
import Header from '$lib/components/Header.svelte';
import type { PageData } from './$types';
```

## 5. IDs de Seção

| ID | Componente |
|----|-----------|
| `#hero` | `Hero.svelte` |
| `#solutions` | `Solutions.svelte` |
| `#authority` | `Authority.svelte` |
| `#differential` | `Differential.svelte` |
| `#testimonials` | `Testimonials.svelte` |
| `#contact` | `CTA.svelte` |

## 6. Dependências Externas (CDN)

Apenas Google Fonts e Material Symbols (carregados via `src/app.html`):

| Recurso | CDN | Justificativa |
|---------|-----|---------------|
| Google Fonts | `fonts.googleapis.com` | Space Grotesk, Geist, JetBrains Mono |
| Material Symbols | `fonts.googleapis.com` | Ícones (Outlined) |

### Ao Adicionar Novo Recurso
1. Prefira assets locais em `static/`
2. Se CDN for inevitável, adicione `rel="preconnect"` no `<head>`
3. Documente no `AGENTS.md`

## 7. Limites e Restrições

| Aspecto | Limite | Motivo |
|---------|--------|--------|
| Tamanho do build | < 200kB (HTML+CSS+JS inicial) | Core Web Vitals |
| Imagens | < 200kB cada | LCP |
| Dependências CDN | 2 (Google Fonts + Symbols) | Autonomia |
| Breakpoints | 1 (768px) | Simplicidade |
| Componentes | < 300 linhas cada | Manutenibilidade |
