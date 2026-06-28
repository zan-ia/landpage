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
