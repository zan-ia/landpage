# Tarefa 04 — Instruções de Código e Design

## Objetivo
Criar/validar as instruções específicas para cada domínio técnico do projeto (CSS, HTML, Deploy, Style Architecture, Project Organization), garantindo que os agentes sigam as convenções corretas ao modificar cada tipo de arquivo.

## Responsabilidades Específicas

1. **`css.instructions.md`** — Convenções de CSS:
   - `applyTo: "src/**/*.svelte, src/lib/app.css"`
   - Design tokens: usar `var(--color-*)`, `var(--font-*)`, `var(--spacing-*)`
   - Scoped CSS: `<style>` em cada componente, sem conflitos de classe
   - BEM naming para classes dentro de componentes
   - Responsive breakpoints: 768px mobile, media queries
   - Animações: `@keyframes` em `app.css`, `prefers-reduced-motion`
   - Performance: `will-change` só durante interação, `contain` onde aplicável
   - PROIBIDO: Tailwind CSS, hex hardcoded, `!important`

2. **`html.instructions.md`** — Convenções de HTML/Markup:
   - `applyTo: "src/**/*.svelte, src/app.html"`
   - HTML5 semântico: heading hierarchy (h1→h6), `<main>`, `<nav>`, `<section>`
   - Acessibilidade: ARIA labels, alt texts, `role` attributes
   - Imagens: `loading="lazy"`, `srcset`, formatos modernos
   - Links: `rel` attributes, external vs internal
   - Meta tags: viewport, description, og tags em `app.html`

3. **`deploy.instructions.md`** — Convenções de Deploy:
   - Output em `build/` (gerado, não versionar)
   - GitHub Actions: workflow em `.github/workflows/deploy.yml`
   - GitHub Pages: branch `gh-pages` ou deploy via Actions
   - NUNCA modificar `build/` diretamente
   - Build commands: `npm run build` → output estático

4. **`style-architecture.instructions.md`** — Arquitetura Visual:
   - `applyTo: "src/**"`
   - Glass panels: classe `.glass-panel` com `backdrop-filter` e borda sutil
   - Gradientes: padrões de gradiente do projeto
   - Grid layouts: padrões de grid reutilizáveis
   - Badges: estilo e posicionamento consistente
   - Section headers: tipografia e espaçamento padrão
   - Tipografia: Space Grotesk (headings), Geist (body), JetBrains Mono (code)
   - Material Design 3: paleta de cores, elevação, tokens

5. **`project-organization.instructions.md`** — Estrutura do Projeto:
   - `applyTo: "src/**"`
   - Estrutura de diretórios: `lib/components/`, `routes/`, `lib/app.css`
   - Regras de dependência: componentes não importam rotas
   - Naming: PascalCase para componentes, kebab-case para arquivos
   - Novos componentes: sempre em `src/lib/components/`
   - Assets: `static/assets/images/` → referenciar como `/assets/images/`

6. **`pipeline-workflow.instructions.md`** — Fluxo do Pipeline:
   - `applyTo: ".github/agents/**, .github/prompts/**, .github/skills/**"`
   - Fluxo completo: Plan → Implement → Review
   - Templates de issue por tipo (bug/feature/melhoria)
   - Convenções: branch, commit, PR
   - HITL obrigatório em 2 pontos
   - Loop de revisão: máx. 3 iterações

## Artefato de Saída
- 6 arquivos `.instructions.md` criados/atualizados em `.github/instructions/`

## Dependências
- **Tarefa 01** (pesquisa) — para validar formato de instructions
- **Tarefa 08** (critérios de revisão) — para alinhar dimensões de qualidade

## Critérios de Aceitação
- [ ] `css.instructions.md`: tokens, scoped, BEM, animações, proibições
- [ ] `html.instructions.md`: semântica, acessibilidade, meta tags
- [ ] `deploy.instructions.md`: build output, Actions, regras
- [ ] `style-architecture.instructions.md`: glass-panel, gradientes, tipografia, MD3
- [ ] `project-organization.instructions.md`: estrutura, dependências, naming
- [ ] `pipeline-workflow.instructions.md`: fluxo completo, HITL, convenções
- [ ] Todos com `applyTo` correto usando glob patterns
- [ ] Todos com `description` clara de quando são aplicados
