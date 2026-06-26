---
description: "Use when: making changes to build/ output directory, modifying GitHub Actions workflows, preparing deployment to GitHub Pages, or ensuring files are correctly structured for production. Covers deploy structure and CI/CD conventions."
applyTo: ["build/**", ".github/workflows/**"]
---
# Deploy & GitHub Pages Guidelines

## Estrutura de Build
- O site é **100% estático** — sem build step, sem bundler
- Todo o conteúdo de produção fica em `build/`
- O HTML final deve estar em `build/index.html`
- Assets (imagens, CSS, JS) vão em `build/assets/`

## Regras para Deploy
1. **Não quebrar o path:** O site é servido de `build/` — paths relativos devem funcionar a partir dessa pasta
2. **GitHub Actions:** O workflow em `.github/workflows/static.yml` faz deploy automático ao push na `main`
3. **Nunca commit** `node_modules/`, `.env`, ou artefatos de build que não sejam do projeto
4. **Imagens:** Prefira assets locais em `build/assets/images/` em vez de CDNs externas
5. **Teste local:** Abra `build/index.html` diretamente no navegador para verificar antes do push

## Workflow CI/CD
- Trigger: push na branch `main`
- Ação: `actions/upload-pages-artifact` com `path: './build'`
- Ambiente: `github-pages`
- Deploy manual: possível via `workflow_dispatch` no Actions tab
