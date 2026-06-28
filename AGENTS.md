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
│   │   ├── refactor-css.agent.md
│   │   ├── orquestrador.agent.md
│   │   ├── planejador.agent.md
│   │   ├── implementador.agent.md
│   │   └── revisor.agent.md
│   ├── instructions/         # Regras automáticas (applyTo)
│   │   ├── css.instructions.md
│   │   ├── html.instructions.md
│   │   ├── deploy.instructions.md
│   │   ├── style-architecture.instructions.md
│   │   ├── project-organization.instructions.md
│   │   ├── tool-usage.instructions.md
│   │   └── pipeline-workflow.instructions.md
│   ├── prompts/              # Comandos customizados
│   │   ├── adicionar-depoimento.prompt.md
│   │   ├── adicionar-servico.prompt.md
│   │   ├── otimizar-seo.prompt.md
│   │   ├── iniciar-bugfix.prompt.md
│   │   ├── iniciar-feature.prompt.md
│   │   └── iniciar-melhoria.prompt.md
│   ├── skills/               # Conhecimento especializado
│   │   ├── criar-pagina-institucional/SKILL.md
│   │   ├── criar-section/SKILL.md
│   │   ├── css-comparison-workflow/SKILL.md
│   │   ├── otimizar-imagens/SKILL.md
│   │   └── pipeline-orquestracao/SKILL.md
│   ├── plans/                # Planos de implementação
│   │   └── README.md
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

---

## Orquestração de Agentes

O projeto utiliza um sistema de **4 agentes de pipeline** para desenvolvimento automatizado com qualidade:

| Agente | Papel | Ferramentas |
|--------|-------|-------------|
| `orquestrador` | Coordena o fluxo completo — issue → branch → planejar → implementar → revisar → commit → PR | Todas |
| `planejador` | Analisa issues e codebase, gera planos detalhados em `.github/plans/` | read, search, web |
| `implementador` | Executa planos, faz alterações cirúrgicas, verifica build | read, search, edit, execute |
| `revisor` | Analisa diff vs plano, verifica 10 dimensões de qualidade, classifica issues | read, search |

### Fluxo do Pipeline
```
Usuário → /iniciar-bugfix|feature|melhoria
  → Orquestrador analisa → Cria Issue → HITL
  → Cria Branch → Planejador → Implementador → Revisor
  → [loop máx. 3x se critical/major]
  → Commit → PR → HITL → Merge → Checkout main
```

### Como Iniciar
- `/iniciar-bugfix` — para correção de bugs
- `/iniciar-feature` — para novas funcionalidades
- `/iniciar-melhoria` — para melhorias e refatorações

### Skill de Orquestração
O skill `pipeline-orquestracao` (em `.github/skills/pipeline-orquestracao/SKILL.md`) contém o conhecimento completo do pipeline e é carregado automaticamente quando o usuário menciona pipeline, bugfix, feature, melhoria, corrigir, ou implementar.

---

## Uso de Ferramentas

Regras estritas para uso de cada ferramenta do Copilot (definidas em `.github/instructions/tool-usage.instructions.md`):

| Ferramenta | Regra Principal |
|-----------|----------------|
| **`vscode_askQuestions`** | OBRIGATÓRIO para qualquer ambiguidade. Nunca assuma preferências do usuário. |
| **Browser** | Usar para documentação oficial, comparação DEV vs LIVE. Sempre validar URLs. |
| **Terminal** | Comandos não-destrutivos. Build e lint antes de concluir. |
| **Edição** | Ler antes de editar. Alterações mínimas. Verificar erros após cada edição. |
| **Busca** | Buscar antes de assumir que algo não existe. |
| **Memória** | Usar `/memories/session/pipeline-state.md` para rastrear estado do pipeline. |
| **Subagentes** | Preferir agentes especializados (`planejador`, `implementador`, `revisor`, `Explore`). |

---

## Harness Copilot — Primitivas de Customização

O VS Code oferece **7 primitivas** que formam o harness completo. Todos os agentes do projeto DEVM compreender estas primitivas para tomar decisões corretas.

### Tabela de Primitivas

| Primitiva | Arquivo | Onde | Ativação |
|-----------|---------|------|----------|
| **Agent Instructions** | `AGENTS.md` | Raiz | Sempre-on (todo o workspace) |
| **File Instructions** | `*.instructions.md` | `.github/instructions/` | `applyTo` (glob) ou `description` (semântico) |
| **Custom Agents** | `*.agent.md` | `.github/agents/` | Seletor de agente ou `runSubagent` |
| **Prompts** | `*.prompt.md` | `.github/prompts/` | Slash command (`/`) |
| **Skills** | `SKILL.md` | `.github/skills/<nome>/` | Slash command ou detecção automática |
| **Hooks** | `*.json` | `.github/hooks/` | Eventos de ciclo de vida (`PreToolUse`, etc.) |
| **MCP Servers** | Config JSON | `.vscode/` ou settings | Ferramentas MCP sob demanda |

### Hierarquia de Carregamento

```
💡 Sempre-on: AGENTS.md → diretrizes globais
📁 Por arquivo: *.instructions.md → regras específicas (applyTo)
🎯 Sob demanda: Skills, Prompts, Custom Agents, MCP
🤖 Automático: Hooks → eventos do ciclo de vida
```

### Prioridade (conflitos)

1. **Perfil do usuário** (maior prioridade)
2. **Workspace** (`.github/`, `AGENTS.md`)
3. **Organização** (GitHub org-level, menor prioridade)

### Mapa do Pipeline no Harness

```
AGENTS.md (diretrizes globais)
  + tool-usage.instructions.md (regras de ferramentas)
  + pipeline-workflow.instructions.md (fluxo do pipeline)
  + css.instructions.md, html.instructions.md, ... (regras específicas)
       │
       ▼
  orquestrador.agent.md  ← Agente coordenador
       │
       ├── planejador.agent.md   ← Subagente read-only
       ├── implementador.agent.md ← Subagente executor
       ├── revisor.agent.md       ← Subagente read-only
       └── Explore (built-in)     ← Subagente de exploração
       
  Skills (pipeline-orquestracao, criar-section, ...) ← Sob demanda
  Prompts (/iniciar-bugfix, /iniciar-feature, ...) ← Slash commands
```

### Controle de Invocação de Agentes

Cada `.agent.md` controla como pode ser invocado via frontmatter:

| Campo | Efeito |
|-------|--------|
| `user-invocable: false` | Oculta do seletor de agentes, mas ainda é subagentável |
| `disable-model-invocation: true` | Impede que outros agentes usem como subagente |
| `agents: ["nome1", "nome2"]` | Lista explícita de subagentes permitidos |
| `tools: ["read", "search"]` | Restringe ferramentas do agente |

**Regra:** Listar um agente em `agents` sobrescreve `disable-model-invocation: true`.

### Fork Mode (Skills)

Skills com `context: fork` no frontmatter executam em um **subagente dedicado**:
```yaml
---
name: css-comparison-workflow
context: fork  # Executa em subagente isolado
---
```

- Só o resultado final retorna ao agente pai
- Ideal para skills que leem muitos arquivos ou produzem relatórios
- Experimental: requer `github.copilot.chat.skillTool.enabled`

### Padrões de Orquestração

**Coordinator + Worker** (usado no pipeline):
- `orquestrador` coordena o fluxo, delega para workers especializados
- Cada worker tem ferramentas mínimas (princípio do menor privilégio)
- Workers retornam apenas resultado final; coordenador sintetiza

**Handoffs** (transições interativas entre agentes):
```yaml
handoffs:
  - label: "Iniciar Implementação"
    agent: implementador
    prompt: "Implemente o plano acima."
    send: false
```
- Botões interativos para o usuário avançar no fluxo
- `send: false` = só preenche o próximo prompt
- `send: true` = envia automaticamente

**Multi-Perspectiva** (revisão paralela):
- Múltiplos subagentes rodam em paralelo com perspectivas diferentes
- Resultados são sintetizados pelo coordenador

---

## Pipeline CI/CD via Agentes

### Ciclo Plan → Implement → Review

1. **Plan (Planejador):** Analisa a issue, explora a codebase, gera plano em `.github/plans/issue-{N}-{slug}.md`
2. **Implement (Implementador):** Executa o plano passo a passo, alterações cirúrgicas, verifica `npm run check` e `npm run build`
3. **Review (Revisor):** Analisa diff contra o plano, verifica 10 dimensões de qualidade (código, arquitetura, design, legibilidade, performance, manutenibilidade, especificidade, dependências, testes, acessibilidade), classifica issues como critical/major/minor
4. **Loop:** Se critical ou major → re-planejar (máx. 3 iterações). Se minor → documentar no PR como follow-up.

### Human-in-the-Loop (HITL)
- 🛑 **Após criação da issue** — usuário revisa e aprova
- 🛑 **Após criação do PR** — usuário revisa diff e faz merge

### Convenções

| Convenção | Padrão |
|-----------|--------|
| **Branch** | `fix/`, `feat/`, `improve/` + slug em minúsculas |
| **Commit** | [Conventional Commits](https://www.conventionalcommits.org/): `fix:`, `feat:`, `improve:` |
| **PR** | Incluir `Closes #N` no corpo |
| **Issue** | Template específico por tipo (bug/feature/melhoria) |
| **Plano** | `.github/plans/issue-{N}-{slug}.md` |
| **Estado** | `/memories/session/pipeline-state.md` |
| **Revisão** | Máx. 3 iterações; critical/major → re-planeja; minor → documenta |

### Arquivos do Pipeline
- `.github/instructions/pipeline-workflow.instructions.md` — fluxo completo
- `.github/instructions/tool-usage.instructions.md` — regras de ferramentas
- `.github/agents/orquestrador.agent.md` — agente coordenador
- `.github/agents/planejador.agent.md` — agente de planejamento
- `.github/agents/implementador.agent.md` — agente de implementação
- `.github/agents/revisor.agent.md` — agente de revisão
- `.github/prompts/iniciar-bugfix.prompt.md` — prompt de entrada para bugs
- `.github/prompts/iniciar-feature.prompt.md` — prompt de entrada para features
- `.github/prompts/iniciar-melhoria.prompt.md` — prompt de entrada para melhorias
- `.github/skills/pipeline-orquestracao/SKILL.md` — skill de orquestração
