---
applyTo: "build/**"
---

# Organização de Projeto — Estrutura e Convenções

## 1. Estrutura de Diretórios

```
zania-website/
├── build/                          # 🚀 Produção (deploy GitHub Pages)
│   ├── index.html                  # Landing page completa
│   └── assets/
│       ├── images/                 # Imagens otimizadas (WebP)
│       └── ...
├── docs/                           # 📄 Documentação institucional
│   └── INSTITUCIONAL.md            # Visão completa da empresa
├── .github/
│   ├── workflows/
│   │   └── static.yml              # CI/CD: GitHub Pages
│   ├── ISSUES.md                   # Plano de refatoração
│   ├── prompts/                    # Comandos customizados (/comando)
│   │   ├── adicionar-servico.prompt.md
│   │   ├── adicionar-depoimento.prompt.md
│   │   └── otimizar-seo.prompt.md
│   ├── agents/                     # Agentes customizados
│   │   ├── refactor-css.agent.md
│   │   ├── criador-conteudo.agent.md
│   │   └── performance-auditor.agent.md
│   ├── skills/                     # Skills (conhecimento especializado)
│   │   ├── criar-pagina-institucional/SKILL.md
│   │   ├── criar-section/SKILL.md
│   │   └── otimizar-imagens/SKILL.md
│   └── instructions/               # Instruções (regras automáticas)
│       ├── deploy.instructions.md
│       ├── css.instructions.md
│       ├── html.instructions.md
│       ├── style-architecture.instructions.md
│       └── project-organization.instructions.md  ← este arquivo
├── AGENTS.md                       # Diretrizes para AI agents
├── README.md
└── LICENSE
```

### Convenções de Diretório

| Caminho | Propósito | Quem usa |
|---------|-----------|----------|
| `build/` | Arquivos de produção deployados | GitHub Pages |
| `build/assets/` | Recursos estáticos (imagens, fontes, etc.) | HTML |
| `docs/` | Documentação não técnica (institucional) | Humanos + AI |
| `.github/workflows/` | Automação CI/CD | GitHub Actions |
| `.github/prompts/` | Comandos `/comando` no chat | AI agents |
| `.github/agents/` | Agentes especializados | AI agents |
| `.github/skills/` | Conhecimento especializado por domínio | AI agents |
| `.github/instructions/` | Regras aplicadas automaticamente a arquivos | AI agents |

## 2. Regras de Build e Deploy

### O que vai para produção
- ✅ `build/index.html` — único arquivo HTML completo
- ✅ `build/assets/` — imagens, fontes, etc.
- ❌ `docs/` — não vai para produção
- ❌ `.github/` — não vai para produção
- ❌ Arquivos de desenvolvimento (.md de docs, ISSUES, etc.)

### Processo de Deploy
```yaml
# .github/workflows/static.yml
# Trigger: push na branch main
# Ação: GitHub Pages deploy de ./build/
# URL: https://zanetti-lg.github.io/zania-website/
```

### Regras de Conteúdo em `build/`
- **Imagens:** WebP como formato principal, com fallback JPEG/PNG
- **HTML:** Monolítico (tudo em `index.html`) — sem arquivos parciais
- **CSS:** Inline no `<style>` do HTML (não usar arquivos `.css` externos além de CDNs)
- **JS:** Inline no `<script>` do HTML
- **CDNs:** Apenas Google Fonts, Google Material Symbols e Tailwind (enquanto não migrado)

## 3. Convenções de Responsividade

| Breakpoint | Largura | Alvo |
|------------|---------|------|
| Mobile | `< 768px` | Smartphones |
| Desktop | `>= 768px` | Tablets e acima |

**Regra:** Não introduzir novos breakpoints sem aprovação. O ponto de ruptura único mantém a base de código simples.

## 4. Convenções de Imagens

| Tipo | Formato | Tamanho máx. | Local |
|------|---------|--------------|-------|
| Hero / background | WebP | 200kB | `build/assets/images/` |
| Ícones / logos | SVG | 10kB | `build/assets/images/` |
| Fotos / ilustrações | WebP | 100kB | `build/assets/images/` |
| Open Graph | JPG | 200kB | `build/assets/images/og-image.jpg` |

### Nomenclatura
- Minúsculas, hífens para separar palavras
- Prefixo do contexto: `hero-`, `servico-`, `icone-`, `og-`
- Extensão do formato real (`.webp`, `.svg`, `.jpg`)

```
build/assets/images/
├── hero.webp
├── servico-sistemas.webp
├── servico-agentes.webp
├── servico-midia.webp
├── og-image.jpg
└── favicon.svg
```

## 5. Convenções de Código no HTML

### Seções
- Cada seção tem: `id` único, padding vertical `py-24`, `max-w-6xl mx-auto`
- Comentários delimitadores antes de cada seção:
  ```html
  <!-- ════════════════════════════════════════════ -->
  <!-- NOME_DA_SEÇÃO -->
  <!-- ════════════════════════════════════════════ -->
  ```

### IDs de Seção
| ID | Conteúdo |
|----|----------|
| `#hero` | Seção principal de entrada |
| `#solutions` | Grid de serviços |
| `#testimonials` | Carrossel de depoimentos |
| `#contact` | CTA / formulário de contato |
| `#footer` | Rodapé |

### Classes CSS
- Preferir classes Tailwind (`flex`, `grid`, `gap-*`, `p-*`, `text-*`)
- Classes customizadas apenas para padrões reutilizáveis (`.glass-panel`)
- Usar `var(--color-*)` para cores, nunca hex/rgb direto

## 6. Versionamento (Git)

### Branch
- **`main`** — branch de produção (deploy automático ao push)
- Commits diretos na `main` são aceitáveis para projetos single-page
- Para refatorações grandes, usar branch separada + PR

### Commits
- Preferir mensagens em português (consistente com o projeto)
- Prefixo opcional: `feat:`, `fix:`, `refactor:`, `docs:`, `chore:`

## 7. Dependências Externas (CDN)

Atualmente carregadas via CDN. **Meta:** remover dependências conforme plano de refatoração.

| Recurso | CDN | Prioridade de Remoção |
|---------|-----|-----------------------|
| Tailwind CSS | `cdn.tailwindcss.com` | 🔴 Alta (Issue #1) |
| Google Fonts | `fonts.googleapis.com` | 🟡 Média |
| Material Symbols | `fonts.googleapis.com` | 🟡 Média |

### Ao Adicionar Novo Recurso
1. Prefira inline (CSS/JS no próprio HTML)
2. Se CDN for inevitável, adicione `rel="preconnect"` no `<head>`
3. Documente no `AGENTS.md` ou neste arquivo

## 8. Limites e Restrições

| Aspecto | Limite | Motivo |
|---------|--------|--------|
| Tamanho do HTML | < 500kB | Performance em conexões lentas |
| Imagens | < 200kB cada | Core Web Vitals (LCP) |
| Dependências CDN | 0 (ideal), 3 (atual) | Autonomia do projeto |
| Breakpoints | 1 (768px) | Simplicidade de manutenção |
| Arquivos em build/ | index.html + assets/ | Monolítico por design |
