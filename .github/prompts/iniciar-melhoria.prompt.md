---
description: "Inicia o pipeline de melhoria/refatoração na landing page Zan.IA. Cria issue, branch improve/, planeja, implementa, revisa e abre PR."
argument-hint: "Descreva a melhoria desejada (ex: 'Otimizar o carregamento das fontes Google...')"
agent: "orquestrador"
---

# Iniciar Pipeline de Melhoria

Inicie o pipeline completo de melhoria seguindo o fluxo definido em `.github/instructions/pipeline-workflow.instructions.md`.

## Procedimento

### 1. Entender a Melhoria

Se a descrição do usuário estiver incompleta, use `vscode_askQuestions` para esclarecer:

```
- header: "Situação Atual"
  question: "O que está funcionando mal ou poderia ser melhor?"
- header: "Melhoria Esperada"
  question: "Qual o resultado desejado após a melhoria?"
- header: "Impacto"
  question: "Quais áreas do site serão afetadas?"
  options:
    - label: "Apenas performance/build"
    - label: "Visual/UX"
    - label: "Código/arquitetura"
    - label: "SEO/acessibilidade"
```

### 2. Criar Issue de Melhoria

Crie uma issue no GitHub `zan-ia/landpage` com:

**Título:** `improve: [descrição curta da melhoria]`

**Corpo:**
```markdown
### Situação Atual
[Como está hoje — o que pode ser melhorado]

### Melhoria Proposta
[O que será feito para melhorar]

### Benefícios Esperados
- [benefício 1]
- [benefício 2]

### Impacto
- **Componentes afetados:** [lista]
- **Risco:** [baixo | médio | alto]
- **Build/Deploy:** [sim | não] afeta o processo de build
```

### 3. HITL — Aprovação da Issue

🛑 **PARE e aguarde.** Apresente a issue ao usuário e aguarde aprovação explícita antes de prosseguir.

### 4. Seguir o Pipeline

Após aprovação, execute o pipeline completo:

1. Criar branch `improve/descricao-curta` a partir de `main`
2. Invocar `planejador` (subagente) — para refatorações CSS, considere usar o agente `refactor-css`; para performance, considere o agente `performance-auditor`
3. Invocar `implementador` (subagente) para executar a melhoria
4. Invocar `revisor` (subagente) para validar
5. Se critical/major → re-planejar (máx. 3x)
6. Commit com `improve:` + push
7. Criar PR com `Closes #N`
8. HITL — aguardar revisão do PR

---

## Tipos Comuns de Melhoria

### Performance
- Otimização de imagens (usar skill `otimizar-imagens`)
- Fontes: verificar `display=swap` e `preconnect`
- CSS: verificar `will-change` e `contain`
- JS chunks: analisar `build/_app/immutable/chunks/`
- Agente recomendado: `performance-auditor`

### CSS / Design
- Extrair padrões duplicados para `app.css`
- Corrigir cores hardcoded → design tokens
- Ajustar glass-panel, shadows, animações
- Agente recomendado: `refactor-css`

### Código / Arquitetura
- Migrar `export let` → `$props()` (Svelte 5 Runes)
- Reorganizar componentes
- Melhorar nomes e documentação
- Agente recomendado: `planejador` + `implementador`

---

## Template de Commit (Melhoria)
```
improve(Testimonials): otimizar performance do carrossel

Substitui requestAnimationFrame por CSS scroll-snap nativo
para reduzir consumo de CPU durante auto-play.
Adiciona contain: layout style paint nos cards.
Reduz will-change para apenas durante drag ativo.

Closes #44
```

---

## Referências
- Pipeline completo: `.github/instructions/pipeline-workflow.instructions.md`
- Uso de ferramentas: `.github/instructions/tool-usage.instructions.md`
- Skill de otimização de imagens: `otimizar-imagens`
- Skill de comparação CSS: `css-comparison-workflow`
- Agente de performance: `performance-auditor`
- Agente de refatoração CSS: `refactor-css`
- Convenções de código: `AGENTS.md`
