---
description: "Executa uma revisão de código na landing page Zan.IA seguindo as 10 dimensões de qualidade. Analisa diff, arquivos específicos ou mudanças staged/unstaged."
argument-hint: "[opcional] Escopo: 'diff', 'staged', 'unstaged', ou caminho do arquivo (ex: 'src/lib/components/Hero.svelte')"
agent: "revisor"
---

# Revisar Código — Zan.IA

Execute uma revisão de código completa seguindo o checklist de 10 dimensões de qualidade definido no pipeline Zan.IA.

## Escopos Suportados

| Escopo | Descrição |
|--------|-----------|
| `diff` (padrão) | Analisa as mudanças entre a branch atual e `main` |
| `staged` | Analisa apenas mudanças em staging (git diff --staged) |
| `unstaged` | Analisa mudanças não staged (git diff) |
| `<arquivo>` | Analisa um arquivo específico |

## Procedimento

### 1. Coletar o Diff

Dependendo do escopo:
- `diff`: `git diff main...HEAD`
- `staged`: `git diff --staged`
- `unstaged`: `git diff`
- Arquivo específico: ler o arquivo e comparar com `main`

### 2. Analisar 10 Dimensões

| # | Dimensão | O que Verificar |
|---|----------|----------------|
| 1 | **Código** | Scoped CSS, design tokens (`var(--color-*)`), BEM naming, Svelte 5 Runes (`$props()`, `$state()`), sem Tailwind, sem hex hardcoded |
| 2 | **Arquitetura** | Composição correta de componentes, imports organizados, layout (`+layout.svelte`) intacto, sem acoplamento indevido |
| 3 | **Design** | `.glass-panel` aplicado, tipografia (Space Grotesk/Geist/JetBrains Mono), paleta Material Design 3, gradientes consistentes |
| 4 | **Legibilidade** | Nomes descritivos em português, código limpo, comentários onde necessário, sem código morto |
| 5 | **Performance** | `will-change` só durante interação, `contain` onde aplicável, animações em `transform`/`opacity`, imagens com `loading="lazy"` |
| 6 | **Manutenibilidade** | Padrões consistentes com o restante do código, reutilização de tokens CSS, sem duplicação de estilos |
| 7 | **Especificidade** | Baixa especificidade nos seletores, sem `!important`, sem seletores de ID, sem aninhamento profundo |
| 8 | **Dependências** | Apenas Material Symbols + Google Fonts, sem novas CDN, sem novos pacotes npm não aprovados |
| 9 | **Build** | `npm run check` sem erros, `npm run build` sem warnings, sem alterações em `build/` |
| 10 | **Acessibilidade** | ARIA labels em seções, heading hierarchy (h1→h2→h3), alt text em imagens, `prefers-reduced-motion`, `html lang="pt-BR"` |

### 3. Classificar Issues

| Severidade | Critério | Ação |
|-----------|----------|------|
| 🔴 **Critical** | Bug funcional, build quebrado, regressão visual grave | Bloqueia merge — corrigir antes |
| 🟠 **Major** | Violação de padrão, design inconsistente, performance ruim | Deve ser corrigido |
| 🟡 **Minor** | Estilo, naming, pequenas melhorias | Documentar como follow-up |

### 4. Gerar Relatório

Formato do relatório:

```markdown
## Relatório de Revisão — [escopo]

### Resumo
- Arquivos analisados: N
- Issues encontradas: N (Critical: X, Major: Y, Minor: Z)

### Issues Critical 🔴
| # | Arquivo | Linha | Descrição |
|---|---------|-------|-----------|

### Issues Major 🟠
| # | Arquivo | Linha | Descrição |
|---|---------|-------|-----------|

### Issues Minor 🟡
| # | Arquivo | Linha | Descrição |
|---|---------|-------|-----------|

### Recomendação
[✅ Aprovado | ⚠️ Aprovado com ressalvas | 🔴 Não aprovado]
```

## Referências
- Convenções de código: `AGENTS.md`
- Design system: `src/lib/app.css`
- Pipeline workflow: `.github/instructions/pipeline-workflow.instructions.md`
- CSS conventions: `.github/instructions/css.instructions.md`
- HTML conventions: `.github/instructions/html.instructions.md`
- Svelte conventions: `.github/instructions/svelte.instructions.md`
- Tool usage rules: `.github/instructions/tool-usage.instructions.md`
