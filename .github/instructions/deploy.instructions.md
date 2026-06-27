---
description: "Use when: making changes to build/ output directory, modifying GitHub Actions workflows, preparing deployment to GitHub Pages, or ensuring files are correctly structured for production. Covers deploy structure and CI/CD conventions."
applyTo: ["build/**", ".github/workflows/**"]
---
# Deploy & GitHub Pages Guidelines — SvelteKit

## Estrutura de Build
- O site é gerado pelo **SvelteKit** com `@sveltejs/adapter-static`
- Build: `npm run build` (executa `vite build`)
- Output final em `build/` — HTML + JS + CSS + assets
- Build é **totalmente reproduzível** — nunca edite arquivos em `build/` manualmente
- Fonte da verdade: `src/` (componentes Svelte, CSS, assets estáticos)

## Processo de Build

```bash
npm run build      # Gera build/ com output estático
npm run preview    # Preview local do build (localhost:4173)
npm run dev        # Dev server com HMR (localhost:5173)
```

### O que acontece no build
1. Vite compila componentes Svelte (`.svelte` → JS/CSS)
2. Scoped CSS é extraído e otimizado
3. Assets estáticos de `static/` são copiados para `build/`
4. `@sveltejs/adapter-static` gera HTML pré-renderizado
5. Output final: `build/index.html`, `build/_app/immutable/...`

## Regras para Deploy

1. **Nunca editar `build/` manualmente** — sempre editar `src/` e rebuildar
2. **Nunca commitar `build/`** — está no `.gitignore`, gerado pelo CI
3. **GitHub Actions:** O workflow em `.github/workflows/deploy.yml` faz build + deploy automático
4. **Paths:** O `svelte.config.js` controla `paths.base` para GitHub Pages
5. **Assets estáticos:** Colocar em `static/assets/images/`, referenciar como `/assets/images/...`
6. **Teste local:** `npm run preview` para testar o build de produção

## Workflow CI/CD

```yaml
# .github/workflows/deploy.yml
# Trigger: push na branch main
# Passos:
#   1. Checkout do código
#   2. Setup Node.js
#   3. npm ci
#   4. npm run build
#   5. Deploy para GitHub Pages (actions/upload-pages-artifact)
# Ambiente: github-pages
# URL: https://www.zan.ia.br/ (ou zan-ia.github.io/landpage/)
```

- **Branch:** `main` (produção)
- **Build:** Automático via GitHub Actions
- **Deploy manual:** `workflow_dispatch` disponível

## Estrutura do `build/` após SvelteKit

```
build/
├── index.html              # Página principal (pré-renderizada)
├── 404.html                # Página de erro
├── robots.txt              # Copiado de static/
├── _app/
│   ├── version.json        # Versão do build (cache busting)
│   └── immutable/          # Assets com hash no nome (cache eterno)
│       ├── assets/         # CSS com hash (ex: 0.hb4XEmsu.css)
│       ├── chunks/         # JS chunks com hash
│       ├── entry/          # Entry points (app, start)
│       └── nodes/          # Lazy-loaded page nodes
└── assets/
    └── images/             # Assets copiados de static/
```

### Cache Strategy
- `build/index.html` — **não cachear** (sempre buscar versão mais recente)
- `build/_app/immutable/*` — **cache eterno** (nomes com hash de conteúdo)
- `build/assets/images/*` — cache de 1 dia (mudam raramente)
