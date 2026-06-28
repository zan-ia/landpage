# Tarefa 08 — Critérios de Revisão e Quality Gates

## Objetivo
Definir as 10 dimensões de qualidade que o revisor deve verificar, a classificação de severidade (critical/major/minor), as regras do loop de revisão e os critérios de aprovação/rejeição — garantindo qualidade consistente em cada ciclo.

## Responsabilidades Específicas

### 1. As 10 Dimensões de Qualidade

O revisor deve analisar o diff contra o plano original verificando:

| # | Dimensão | O que Verificar |
|---|----------|----------------|
| 1 | **Código** | Scoped CSS, design tokens (`var(--color-*)`), BEM naming, Svelte 5 Runes (`$state`, `$effect`, `$props`), sem Tailwind, sem hex hardcoded, sem `!important` |
| 2 | **Arquitetura** | Composição correta de componentes (pais/filhos), sem quebrar layout existente, imports corretos, sem dependências circulares, componentes no diretório correto |
| 3 | **Design** | Glass-panel aplicado corretamente, tipografia correta (Space Grotesk/Geist/JetBrains Mono), paleta MD3 respeitada, badges e seções padronizados, consistência visual com o resto do site |
| 4 | **Legibilidade** | Nomes descritivos de variáveis/funções/classes, código limpo e bem estruturado, comentários em português onde necessário, sem código morto ou comentado |
| 5 | **Performance** | `will-change` só durante interação (não permanente), `contain` onde aplicável, sem animações de `width`/`height`/`top`/`left` (preferir `transform`/`opacity`), imagens com `loading="lazy"`, sem blocking resources |
| 6 | **Manutenibilidade** | Padrões consistentes com o resto do projeto, reutilização de design tokens, sem duplicação de código/estilos, componente genérico vs específico bem balanceado |
| 7 | **Especificidade** | CSS com baixa especificidade, sem `!important`, sem seletores aninhados profundos (>3 níveis), scoped CSS não vaza para outros componentes |
| 8 | **Dependências** | Material Symbols correto (ícones existem na fonte), Google Fonts com `display=swap` + `preconnect`, sem novas dependências CDN desnecessárias, sem imports não utilizados |
| 9 | **Testes e Build** | `npm run check` sem erros, `npm run build` limpo (sem warnings), critérios de aceitação da issue atendidos, sem regressões visuais |
| 10 | **Acessibilidade** | ARIA labels em elementos interativos, heading hierarchy correta (h1→h6 sem pular níveis), alt text em imagens, `prefers-reduced-motion` respeitado, contraste adequado, navegação por teclado |

### 2. Classificação de Severidade

| Nível | Definição | Exemplos | Ação |
|-------|-----------|----------|------|
| 🔴 **CRITICAL** | Bug funcional, quebra de build, violação grave de arquitetura, segurança | Cor hardcoded `#1a1a2e` em vez de token, animação usa `height`, componente quebra layout em mobile, `npm run build` falha | **Força re-planejamento** |
| 🟡 **MAJOR** | Violação de padrão de código/design, design inconsistente, performance degradada, dependência incorreta | Novo componente não usa glass-panel, sem ARIA label em botão, `will-change` permanente, tipografia errada | **Força re-planejamento** |
| 🟢 **MINOR** | Estilo de código, documentação, naming, melhorias cosméticas, otimizações não-críticas | Comentário em inglês (padrão português), variável com nome pouco descritivo, espaçamento ligeiramente inconsistente | **Documentar no PR** como follow-up |

### 3. Regras do Loop de Revisão

```
1. SE há issues CRITICAL → RE-PLANEJAR (volta para Fase 3)
2. SE há issues MAJOR → RE-PLANEJAR (volta para Fase 3)
3. SE apenas MINOR → APROVAR com ressalvas, documentar no PR
4. SE sem issues → APROVAR totalmente
5. MÁXIMO 3 ITERAÇÕES do ciclo Planejar→Implementar→Revisar
   - Iteração 1: normal
   - Iteração 2: foco nos issues apontados
   - Iteração 3: última chance
   - Se não passar na 3ª: documentar riscos conhecidos e forçar PR com ressalvas
```

### 4. Formato do Relatório de Revisão

O revisor deve retornar no seguinte formato padronizado:

```
📊 Relatório de Revisão — Issue #N

Status: APROVADO | ALTERACOES | REJEITADO

Issues Encontrados:
🔴 CRITICAL (X):
  - arquivo: descrição do problema

🟡 MAJOR (X):
  - arquivo: descrição do problema

🟢 MINOR (X):
  - arquivo: descrição do problema

Resumo:
- Total de arquivos analisados: X
- Issues críticos: X
- Issues major: X
- Issues minor: X

Recomendação: MERGE | RE-PLANEJAR | AJUSTES
```

### 5. Dimensões Específicas por Tecnologia

**Svelte/SvelteKit:**
- Runes mode correto (`$state()`, `$effect()`, `$props()`)
- Sem Svelte 4 legacy (`let:`, `export let`, `on:click`)
- `+layout.js` com `prerender = true`
- adapter-static configurado corretamente

**CSS:**
- Scoped CSS não vaza (sem `:global()` desnecessário)
- Design tokens usados consistentemente
- Media queries no padrão do projeto (768px)
- Sem animações de propriedades de layout

**HTML:**
- Semantic HTML5
- ARIA labels onde necessário
- Heading hierarchy sem pular níveis

## Artefato de Saída
- Seção de revisão no `.github/agents/revisor.agent.md`
- Referência no `.github/instructions/pipeline-workflow.instructions.md`

## Dependências
- **Tarefa 02** (agentes) — revisor.agent.md
- **Tarefa 07** (pipeline) — Fase 5 e Fase 6

## Critérios de Aceitação
- [ ] 10 dimensões de qualidade definidas com exemplos concretos
- [ ] Classificação de severidade (critical/major/minor) com critérios claros
- [ ] Regras do loop de revisão documentadas
- [ ] Formato de relatório padronizado
- [ ] Regra das 3 iterações com fallback documentado
- [ ] Dimensões específicas por tecnologia (Svelte, CSS, HTML)
- [ ] Alinhamento com as instruções de código/design (Tarefa 04)
