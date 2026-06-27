# Zan.IA Website — Agent Guidelines

## Project Overview

Landing page institucional para **Zan.IA** — empresa de tecnologia focada em desenvolvimento web, agentes de IA, automação e criação de mídia assistida por inteligência artificial.

> Consulte [`docs/INSTITUCIONAL.md`](./docs/INSTITUCIONAL.md) para visão completa da empresa, serviços e diferenciais.

## Tech Stack

| Camada | Tecnologia |
|--------|-----------|
| **Framework** | SvelteKit 5 (Runes mode) |
| **Build** | Vite + `@sveltejs/adapter-static` |
| **Markup** | Componentes Svelte com scoped CSS |
| **Estilos** | Scoped `<style>` por componente + `app.css` global (design tokens) |
| **Ícones** | Google Material Symbols Outlined |
| **Tipografia** | Space Grotesk, Geist, JetBrains Mono (Google Fonts) |
| **Tema** | Dark mode (Material Design 3) |
| **Deploy** | GitHub Pages + GitHub Actions |

## Architecture

- **SvelteKit 5** com Runes mode (reatividade via `$state`, `$effect`, `$props`)
- **Static site generation** via `@sveltejs/adapter-static` — output em `build/`
- **Scoped CSS nativo:** cada componente Svelte tem seu próprio `<style>`, sem conflitos de classe
- **Único CSS global:** `src/lib/app.css` com design tokens (variáveis CSS), reset e utilitários compartilhados (glass-panel, animações)
- **Sem Tailwind CSS:** utilidades substituídas por classes CSS escopadas + variáveis de design token

## Build & Deploy

```bash
npm run dev      # Desenvolvimento em localhost:5173
npm run build    # Gera build/ com saída estática
npm run preview  # Preview do build local
# Deploy automático via GitHub Actions ao push na branch main
# workflow: .github/workflows/deploy.yml
# Servido de: ./build/
```

## Estrutura de Arquivos

```
zania-website/
├── src/
│   ├── lib/
│   │   ├── components/        # Componentes Svelte com <style> escopado
│   │   │   ├── Header.svelte
│   │   │   ├── Hero.svelte
│   │   │   ├── Authority.svelte
│   │   │   ├── Solutions.svelte
│   │   │   ├── Differential.svelte
│   │   │   ├── Testimonials.svelte
│   │   │   ├── CTA.svelte
│   │   │   └── Footer.svelte
│   │   └── app.css            # CSS global (design tokens + reset + utilitários)
│   ├── routes/
│   │   ├── +layout.svelte     # Layout principal (Header + Footer)
│   │   ├── +layout.js         # Config: prerender = true
│   │   └── +page.svelte       # Home page (monta todos os componentes)
│   └── app.html               # Template HTML (fontes, meta)
├── static/
│   └── assets/images/         # Imagens locais
├── build/                     # Output do build (gerado, não versionar)
├── .github/
│   ├── agents/              # Agentes especializados
│   │   ├── criador-conteudo.agent.md
│   │   ├── performance-auditor.agent.md
│   │   └── refactor-css.agent.md
│   ├── instructions/         # Regras automáticas (applyTo)
│   │   ├── css.instructions.md
│   │   ├── html.instructions.md
│   │   ├── deploy.instructions.md
│   │   ├── style-architecture.instructions.md
│   │   └── project-organization.instructions.md
│   ├── prompts/              # Comandos customizados
│   │   ├── adicionar-depoimento.prompt.md
│   │   ├── adicionar-servico.prompt.md
│   │   └── otimizar-seo.prompt.md
│   ├── skills/               # Conhecimento especializado
│   │   ├── criar-pagina-institucional/SKILL.md
│   │   ├── criar-section/SKILL.md
│   │   ├── css-comparison-workflow/SKILL.md
│   │   └── otimizar-imagens/SKILL.md
│   ├── ISSUES.md
│   └── workflows/
│       └── deploy.yml        # Build + Deploy GitHub Pages
├── svelte.config.js
├── vite.config.ts
├── package.json
└── AGENTS.md
```

## Convenções de Código

- **Componentes Svelte:** Usar Runes mode (`$state()`, `$effect()`, `$props()`)
- **Scoped CSS:** Cada componente tem `<style>` próprio — sem classes globais, sem conflitos
- **Design Tokens:** Usar variáveis CSS `--color-*`, `--font-*`, `--spacing-*` de `app.css`
- **Animações:** `@keyframes` definidos em `app.css`; usar classes `.animate-*` quando necessário
- **Ícones:** `<span class="material-symbols-outlined">icon_name</span>`
- **Imagens:** Referenciar como `/assets/images/nome.ext` (mapeado de `static/assets/images/`)
- **Responsividade:** Breakpoint mobile em 768px via media queries nos componentes
- **Glass panels:** Classe global `.glass-panel` com backdrop-filter e borda sutil

## GitHub Workflow (Issues & PRs)

### Auto-close de Issues
- Incluir `Closes #N` (ou `Fixes`/`Resolves`) no corpo do PR — GitHub fecha a issue automaticamente ao mergear.
- **Verificar** se a issue foi fechada após o merge. Se não, fechar manualmente com `state=closed`, `reason=completed`.

### Fluxo Recomendado
1. Criar branch a partir de `main` (`feat/nome-descritivo`)
2. Fazer commit com mensagem descritiva (imperativo, < 72 chars)
3. Incluir `Closes #N` no corpo do PR
4. Abrir PR apontando para `main`
5. Após merge, confirmar que a issue foi fechada

## Canais de Contato

- WhatsApp (CTA principal): link direto para número da empresa
- Footer: links institucionais
