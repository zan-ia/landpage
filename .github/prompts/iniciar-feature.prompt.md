---
description: "Inicia o pipeline de nova funcionalidade na landing page Zan.IA. Cria issue, branch feat/, planeja, implementa, revisa e abre PR."
argument-hint: "Descreva a nova funcionalidade (ex: 'Adicionar uma seção de preços com 3 planos...')"
agent: "orquestrador"
---

# Iniciar Pipeline de Feature

Inicie o pipeline completo de nova funcionalidade seguindo o fluxo definido em `.github/instructions/pipeline-workflow.instructions.md`.

## Procedimento

### 1. Entender a Feature

Se a descrição do usuário estiver incompleta, use `vscode_askQuestions` para esclarecer:

```
- header: "Escopo da Feature"
  question: "Quais são os limites desta funcionalidade?"
- header: "Critérios de Aceitação"
  question: "Como saberemos que a feature está pronta?"
- header: "Prioridade"
  question: "Esta feature substitui algo existente ou é totalmente nova?"
  options:
    - label: "Feature totalmente nova"
    - label: "Substitui/estende algo existente"
    - label: "Variação de um componente existente"
```

### 2. Criar Issue de Feature

Crie uma issue no GitHub `zan-ia/landpage` com:

**Título:** `feat: [descrição curta da funcionalidade]`

**Corpo:**
```markdown
### Motivação
[Por que esta feature é necessária? Qual problema resolve?]

### Descrição
[O que será implementado, em detalhes]

### Critérios de Aceitação
- [ ] Critério 1
- [ ] Critério 2
- [ ] Critério 3

### Design de Referência
[Links para inspirações, mockups, ou componentes similares no site]

### Componentes Afetados
- [lista de componentes existentes que serão modificados]
```

### 3. HITL — Aprovação da Issue

🛑 **PARE e aguarde.** Apresente a issue ao usuário e aguarde aprovação explícita antes de prosseguir.

### 4. Seguir o Pipeline

Após aprovação, execute o pipeline completo:

1. Criar branch `feat/descricao-curta` a partir de `main`
2. Invocar `planejador` (subagente) — ele deve consultar skills `criar-section` ou `criar-pagina-institucional` se for novo componente
3. Invocar `implementador` (subagente) para construir a feature
4. Invocar `revisor` (subagente) para validar
5. Se critical/major → re-planejar (máx. 3x)
6. Commit com `feat:` + push
7. Criar PR com `Closes #N`
8. HITL — aguardar revisão do PR

---

## Template de Commit (Feature)
```
feat(Solutions): adicionar seção de planos e preços

Nova seção Precos.svelte com 3 cards de plano (Básico, Pro, Enterprise).
Cada card usa glass-panel com destaque diferenciado para o plano Pro.
Integrado ao +page.svelte entre Solutions e Differential.

Closes #43
```

---

## Referências
- Pipeline completo: `.github/instructions/pipeline-workflow.instructions.md`
- Uso de ferramentas: `.github/instructions/tool-usage.instructions.md`
- Skill de criação de seção: `criar-section`
- Skill de criação de página: `criar-pagina-institucional`
- Convenções de código: `AGENTS.md`
