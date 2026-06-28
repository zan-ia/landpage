# Tarefa 10 — Criar Template de Plano de Implementação

## Objetivo
Criar um template reutilizável em `.github/plans/_template.md` que o agente `planejador` deve usar como base estrutural para gerar planos de implementação consistentes e completos.

## Responsabilidades Específicas

### 1. Criar arquivo `_template.md`

Local: `.github/plans/_template.md`

O template deve ser um **esqueleto preenchível** com placeholders `{...}` que o planejador substitui com dados concretos da issue.

### 2. Estrutura do template

```markdown
# Plano de Implementação — Issue #{N}: {título}

**Data:** {YYYY-MM-DD}
**Tipo:** {bug | feature | melhoria}
**Complexidade:** {Baixa | Média | Alta}
**Branch:** {fix|feat|improve}/{slug}

---

## 1. Resumo da Abordagem

{2-3 frases descrevendo a estratégia de implementação. O QUE será feito, não COMO.}

## 2. Arquivos a Modificar

| Arquivo | Ação | Justificativa |
|---------|------|---------------|
| `src/lib/components/{Nome}.svelte` | {criar | modificar | deletar} | {por que este arquivo} |

## 3. Padrões a Seguir

- **CSS:** {tokens específicos, glass-panel, BEM naming}
- **Svelte:** {runes, props, effects relevantes}
- **HTML:** {semântica, ARIA, headings}
- **Design:** {paleta, tipografia, badges, seções}

## 4. Ordem de Implementação

1. {Passo 1 — geralmente criar/modificar o que não tem dependências primeiro}
2. {Passo 2}
3. {Passo 3 — verificação final: npm run check && npm run build}

## 5. Riscos Identificados

| Risco | Probabilidade | Mitigação |
|-------|---------------|-----------|
| {descrição do risco} | {baixa | média | alta} | {como evitar ou contornar} |

## 6. Critérios de Aceitação

- [ ] {Critério 1 da issue}
- [ ] {Critério 2 da issue}
- [ ] `npm run check` passa sem erros
- [ ] `npm run build` gera build limpo

## 7. Referências

- Issue: [#{N}]({url})
- Docs: [`docs/INSTITUCIONAL.md`](../../docs/INSTITUCIONAL.md)
- Instruções: {css.instructions.md | html.instructions.md | svelte.instructions.md}
```

### 3. Atualizar README.md de planos

Adicionar ao `.github/plans/README.md` uma referência ao template:
```markdown
## Template
Use `_template.md` como base para novos planos. O agente `planejador` preenche
os placeholders `{...}` automaticamente com dados da issue.
```

### 4. Atualizar constraint do planejador

No `.github/agents/planejador.agent.md`, adicionar uma constraint:
```
- SEMPRE use o template `.github/plans/_template.md` como estrutura base para gerar planos
```

## Artefatos de Saída
- `.github/plans/_template.md` — criado
- `.github/plans/README.md` — atualizado (referência ao template)
- `.github/agents/planejador.agent.md` — constraint adicionada

## Dependências
- **Task 02** (planejador.agent.md) — constraint a adicionar
- **Task 07** (pipeline-workflow) — Fase 3 (planejamento)

## Critérios de Aceitação
- [ ] `_template.md` criado com 7 seções bem definidas
- [ ] Placeholders `{...}` são autoexplicativos
- [ ] README.md referencia o template
- [ ] planejador.agent.md tem constraint para usar o template
- [ ] Template é conciso (máx. 70 linhas)
