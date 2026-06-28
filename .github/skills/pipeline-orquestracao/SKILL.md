---
name: pipeline-orquestracao
description: "Orquestra o pipeline completo de desenvolvimento da landing page Zan.IA: Plan→Implement→Review com HITL. Use quando: iniciar qualquer tarefa de desenvolvimento (bugfix, feature, melhoria) que precise passar pelo ciclo completo de qualidade — criação de issue, planejamento, implementação, revisão e PR. Também use quando mencionar: pipeline, bugfix, corrigir bug, nova feature, implementar funcionalidade, melhoria, refatoração, fluxo de trabalho, orquestração, code review automatizado."
argument-hint: "[bugfix | feature | melhoria] — descreva a tarefa..."
user-invocable: true
disable-model-invocation: false
---

# Pipeline de Orquestração — Zan.IA

Skill de orquestração do pipeline de desenvolvimento CI/CD gerenciado por agentes do Copilot.

## Visão Geral

O pipeline implementa um ciclo **Plan → Implement → Review** com **Human-in-the-Loop (HITL)** nos pontos críticos (aprovação da issue, revisão do PR). O fluxo é coordenado pelo agente `orquestrador` e executado por 3 subagentes especializados.

```
USUÁRIO → /iniciar-bugfix|feature|melhoria
    │
    ▼
ORQUESTRADOR
    ├─ (1) Analisa → Classifica → Cria Issue → 🛑 HITL
    ├─ (2) Cria Branch (fix|feat|improve/...)
    ├─ (3) PLANEJADOR (subagente) → Plano em .github/plans/
    ├─ (4) IMPLEMENTADOR (subagente) → Código + Build
    ├─ (5) REVISOR (subagente) → Relatório de qualidade
    │       └─ Loop máx. 3x se critical/major
    ├─ (6) Commit (Conventional) + Push
    ├─ (7) Cria PR (Closes #N) → 🛑 HITL
    └─ (8) Checkout main
```

---

## Como Usar

### Via Slash Command
Digite `/` no chat e selecione um dos prompts:
- `/iniciar-bugfix` — para correção de bugs
- `/iniciar-feature` — para novas funcionalidades
- `/iniciar-melhoria` — para melhorias e refatorações

### Via Menção Direta
Mencione o tipo de tarefa e a descrição:
- "Corrigir o bug das cores no header mobile"
- "Adicionar uma seção de depoimentos na página"
- "Melhorar o carregamento das fontes do Google"

O skill será carregado automaticamente e o fluxo do pipeline será iniciado.

---

## Agentes do Pipeline

| Agente | Papel | Ferramentas |
|--------|-------|-------------|
| `orquestrador` | Coordenador — gerencia o fluxo completo | Todas |
| `planejador` | Analista — explora codebase e cria plano | read, search, web |
| `implementador` | Desenvolvedor — executa o plano | read, search, edit, execute |
| `revisor` | QA — analisa diff e verifica qualidade | read, search |

---

## Convenções

### Branches
| Tipo | Prefixo | Exemplo |
|------|---------|---------|
| Bugfix | `fix/` | `fix/corrigir-cores-header` |
| Feature | `feat/` | `feat/adicionar-secao-precos` |
| Melhoria | `improve/` | `improve/otimizar-fontes` |

### Commits (Conventional Commits)
```
fix(Header): corrigir cores do glass-panel
feat(Solutions): adicionar seção de preços
improve(Testimonials): otimizar carrossel

Closes #N
```

### Issues
- Título com prefixo: `fix:`, `feat:`, `improve:`
- Template específico por tipo (bug/feature/melhoria)
- `Closes #N` no PR para auto-close

---

## Checklist de Qualidade (Revisor)

| Dimensão | Verifica |
|----------|----------|
| Código | Scoped CSS, design tokens, BEM, Svelte 5 Runes, sem Tailwind, sem hex |
| Arquitetura | Composição correta, imports, layout intacto |
| Design | Glass-panel, tipografia (Space Grotesk/Geist/JetBrains Mono), paleta MD3 |
| Legibilidade | Nomes descritivos, código limpo, português |
| Performance | `will-change` só durante interação, `contain`, animações `transform`/`opacity` |
| Manutenibilidade | Padrões consistentes, reutilização de tokens, sem duplicação |
| Especificidade | Baixa, sem `!important`, sem seletores de ID |
| Dependências | Material Symbols + Google Fonts apenas, sem novas CDN |
| Testes | `npm run check` e `npm run build` passam, critérios da issue atendidos |
| Acessibilidade | ARIA labels, heading hierarchy, alt texts, reduced motion |

---

## Pontos de HITL (Human-in-the-Loop)

🛑 O pipeline **SEMPRE** para e aguarda o usuário em:
1. **Após criação da issue** — usuário revisa título, descrição, e escopo
2. **Após criação do PR** — usuário revisa diff, comentários do revisor, e faz merge

---

## Regras do Loop de Revisão

- Issues **CRITICAL** (bug funcional, build quebrado) → re-planejar
- Issues **MAJOR** (violação de padrão, design inconsistente) → re-planejar
- Issues **MINOR** (estilo, naming) → documentar no PR como follow-up
- **Máximo 3 iterações** — se não passar, documentar riscos e forçar PR com ressalvas

---

## Arquivos de Plano

Planos são salvos em `.github/plans/issue-{N}-{slug}.md` com:
- Resumo da abordagem
- Arquivos a modificar/criar
- Padrões a seguir
- Ordem de implementação
- Riscos identificados
- Checklist de verificação

---

## Referências

- **Pipeline workflow detalhado:** `.github/instructions/pipeline-workflow.instructions.md`
- **Regras de uso de ferramentas:** `.github/instructions/tool-usage.instructions.md`
- **Convenções de código:** `AGENTS.md`
- **Design system:** `src/lib/app.css`
- **Informações institucionais:** `docs/INSTITUCIONAL.md`

### Prompts de Entrada
- `/iniciar-bugfix` — `.github/prompts/iniciar-bugfix.prompt.md`
- `/iniciar-feature` — `.github/prompts/iniciar-feature.prompt.md`
- `/iniciar-melhoria` — `.github/prompts/iniciar-melhoria.prompt.md`

### Agentes do Pipeline
- `orquestrador` — `.github/agents/orquestrador.agent.md`
- `planejador` — `.github/agents/planejador.agent.md`
- `implementador` — `.github/agents/implementador.agent.md`
- `revisor` — `.github/agents/revisor.agent.md`

### Skills Relacionados
- `criar-section` — criar novas seções
- `criar-pagina-institucional` — criar páginas institucionais
- `css-comparison-workflow` — comparar DEV vs LIVE
- `otimizar-imagens` — otimizar imagens
- `seo-otimization` — conhecimento técnico de SEO (meta tags, structured data)

### Prompts de Entrada
- `/iniciar-bugfix` — `.github/prompts/iniciar-bugfix.prompt.md`
- `/iniciar-feature` — `.github/prompts/iniciar-feature.prompt.md`
- `/iniciar-melhoria` — `.github/prompts/iniciar-melhoria.prompt.md`

### Prompts de Ação Direta
- `/adicionar-depoimento` — `.github/prompts/adicionar-depoimento.prompt.md`
- `/adicionar-servico` — `.github/prompts/adicionar-servico.prompt.md`
- `/otimizar-seo` — `.github/prompts/otimizar-seo.prompt.md`
- `/revisar` — `.github/prompts/revisar.prompt.md`
