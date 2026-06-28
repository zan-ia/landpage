# Plano de Implementação — Issue #5: Melhorar Carrossel de Depoimentos: Data Layer + Infinite Scroll Circular

**Data:** 2026-06-28
**Tipo:** melhoria
**Complexidade:** Alta
**Branch:** improve/melhorar-carrossel-depoimentos

---

## 1. Resumo da Abordagem

Reescrita completa do componente `Testimonials.svelte` com 4 fases incrementais: (1) extrair dados para JSON externo com type safety e guard clause, (2) implementar sistema de blocos determinístico baseado em viewport visível com `$derived displayBlock` e `tripleBlock`, (3) substituir o modelo de clones manuais + `scrollTo` instant-boundary por scroll infinito verdadeiro sem jumps visíveis, (4) corrigir acessibilidade e polimento final. O DOM é construído via template que itera sobre `tripleBlock` com `aria-hidden` nos blocos 1 e 3, e a lógica de navegação opera exclusivamente no bloco do meio (índice 2). O `realIndex` é derivado da posição de scroll no bloco do meio, eliminando os problemas de double-jump, flicker de `scroll-snap` e perda de posição no resize.

## 2. Arquivos a Modificar

| Arquivo | Ação | Justificativa |
|---------|------|---------------|
| `src/lib/data/testimonials.json` | **CRIAR** | Novo arquivo de dados JSON com 6 depoimentos, substituindo o array hardcoded no componente |
| `src/lib/components/Testimonials.svelte` | **REESCREVER** | Reescrita completa do componente (~900 linhas) para implementar as 4 fases do carrossel infinito |

### Não Modificar
| Arquivo | Justificativa |
|---------|---------------|
| `src/lib/app.css` | Sem alterações — tokens existentes são suficientes |
| `src/routes/+page.svelte` | O import do componente permanece igual |
| `src/lib/index.ts` | Sem necessidade de re-export — o JSON é importado diretamente |

## 3. Padrões a Seguir

### Svelte (Runes Mode)
- `$state()` para estado reativo: `cardsPerView`, `currentIndex`, `realIndex`, `isDragging`, `isHovering`, `prefersReducedMotion`, `isInViewport`, `autoPlayActive`
- `$derived()` para valores computados: `displayBlock` (com DOM growth cap), `tripleBlock`, `liveRegionText`
- `$effect()` para side effects com cleanup: IntersectionObserver, ResizeObserver único, `prefers-reduced-motion` listener, autoplay `requestAnimationFrame`, scroll tracking
- `$effect()` de inicialização: posicionar scroll no início do bloco do meio via `tick()`
- `$props()` — sem props (componente autônomo)
- `<script lang="ts">` obrigatório
- Tipos: `interface Testimonial { stars: number; text: string; initials: string; name: string; role: string; }`
- Import do JSON: `import testimonials from '$lib/data/testimonials.json'`
- Guard clause: `if (!testimonials || testimonials.length === 0) { ... }` — renderizar fallback ou fragmento vazio

### CSS (Scoped + Design Tokens)
- BEM-like: `testimonials__*`, `testimonial__card`, `testimonials__dot--active`, `testimonials__carousel--reduced`
- Tokens obrigatórios: `--color-primary`, `--color-on-surface`, `--color-background`, `--font-display`, `--font-body`, `--font-code`, `--spacing-gutter`, `--spacing-margin-mobile`, `--radius-lg`, `--radius-full`
- `.glass-panel` global para cards
- `contain: layout style paint` no carrossel e nos cards
- `scroll-snap-type: x mandatory` desabilitado temporariamente durante jumps (via classe condicional)
- `scroll-behavior: auto` (JS controla smooth/instant)
- `touch-action: pan-x` para scroll horizontal
- `scrollbar-width: none` no container
- `@media (prefers-reduced-motion: reduce)` preservado: desabilita `scroll-snap-type`, animações e transições

### HTML (Semântica + Acessibilidade)
- `<section aria-label="Depoimentos de parceiros" aria-roledescription="carrossel">`
- Bloco 2 (middle): `role="group" aria-roledescription="slide" aria-label="Depoimento {realIndex + 1} de {total}"` — apenas no bloco do meio
- Blocos 1 e 3: `aria-hidden="true"` obrigatório
- Container carrossel: `role="group" aria-label="Depoimentos" tabindex="0"`
- Dots: `<button aria-label="Depoimento {i + 1}" class:testimonials__dot--active={i === realIndex}>`
- Região `aria-live="polite" aria-atomic="true"` para anunciar `liveRegionText`
- Fade gradients: `aria-hidden="true"`

### Arquitetura Técnica (Carrossel Infinito)
- `cardsPerView` como `$state<number>(1)`, atualizado via **único** ResizeObserver no container do carrossel
- `$derived displayBlock`: repete itens até largura total ≥ `(cardsPerView + 2) * cardWidth`; DOM growth cap: `displayBlock.length ≤ testimonials.length × 2`
- `$derived tripleBlock`: `[...displayBlock, ...displayBlock, ...displayBlock]`
- Template: `{#each tripleBlock as t, i}` — sem clones manuais
- Inicialização: scroll no início do bloco do meio (índice `displayBlock.length`)
- Boundary detection: se scroll ≤ limite do bloco 1 ou ≥ limite do bloco 3 → jump instantâneo (`behavior: 'instant'`) para posição equivalente no bloco do meio
- Durante jumps: remover `scroll-snap-type` do container (classe CSS condicional)
- `realIndex = (Math.round(scrollLeft / step) % displayBlock.length) % total` — derivado da posição no bloco do meio
- `goToReal(index)`: navega para `(displayBlock.length + index) * step` no bloco do meio
- `goNext()`: incrementa `realIndex` com wrap, scroll smooth no bloco do meio
- `finishDrag()`: normaliza snap para bloco do meio
- Dots: `total` dots mapeados via `realIndex`

## 4. Ordem de Implementação

### Phase 1: Data Layer
1. **Criar `src/lib/data/testimonials.json`** — array com 6 objetos idênticos aos atuais (manter textos, nomes, etc.)
2. **No `Testimonials.svelte`**: substituir `const testimonials: Testimonial[] = [...]` por `import testimonials from '$lib/data/testimonials.json'`
3. **Adicionar guard clause**: se array vazio → fragmento vazio no template
4. **Rodar `npm run check`** para verificar tipo do JSON importado

### Phase 2: Viewport-Aware Block Builder
5. **Adicionar `let cardsPerView = $state<number>(1)`**
6. **Criar `$derived displayBlock`**: função que repete `testimonials` até `totalWidth ≥ (cardsPerView + 2) * cardWidth`, com cap `≤ testimonials.length × 2`
7. **Criar `$derived tripleBlock`**: `[...displayBlock, ...displayBlock, ...displayBlock]`
8. **Substituir template manual** (clone do último + each real + clone do primeiro) por `{#each tripleBlock as t, idx}...{/each}`
9. **Configurar único ResizeObserver** no container do carrossel para atualizar `cardsPerView`

### Phase 3: Infinite Scroll Engine
10. **Inicializar scroll** no início do bloco do meio: `displayBlock.length * step` via `$effect` com `tick()`
11. **Implementar `scrollHandler`** com boundary detection
12. **Implementar `$derived realIndex`**: `(Math.round(scrollLeft / step) % displayBlock.length) % total`
13. **Adaptar `goToReal(index)`**: navegar para `const target = (displayBlock.length + index) * step` no bloco do meio
14. **Adaptar `goNext()`**: `realIndex = (realIndex + 1) % total` com scroll smooth no bloco do meio
15. **Adaptar `finishDrag()`**: calcular `realIndex` a partir da posição atual e normalizar snap para bloco do meio
16. **Preservar scroll position no resize**: salvar `realIndex` antes do resize, restaurar via `tick()` depois
17. **Preservar `contain: layout style paint`** e `scroll-snap-stop: always` nos cards

### Phase 4: Acessibilidade & Polish
18. **Adicionar `aria-hidden="true"`** nos slots de `tripleBlock` que correspondem aos blocos 1 e 3
19. **Apenas bloco do meio** recebe atributos ARIA de slide
20. **Mergear** os dois `ResizeObserver` atuais em **um único observer** no container
21. **Preservar `prefers-reduced-motion`**
22. **Verificar dots**: `{#each testimonials as _, i}` mapeado via `realIndex`
23. **Rodar `npm run check && npm run build`**

## 5. Riscos Identificados

| Risco | Probabilidade | Mitigação |
|-------|---------------|-----------|
| **Import de JSON falha no build** (tipo não reconhecido) | Média | Configurar `resolveJsonModule: true` no `tsconfig.json` se necessário |
| **Scroll position perdido no resize com novo modelo de blocos** | Alta | Salvar `realIndex` e proporção de scroll antes do resize, restaurar via `tick()` no `$effect` de cleanup do ResizeObserver |
| **Performance com `tripleBlock` em viewport grande** | Baixa | DOM growth cap (`displayBlock.length ≤ testimonials.length × 2`). `contain: layout style paint` isola repaints |
| **Scroll flicker durante jump instantâneo** | Média | Desabilitar `scroll-snap-type` durante o jump via classe condicional + `behavior: 'instant'`. Usar `requestAnimationFrame` para reativar snap após o jump |
| **`goNext()` duplo no boundary** | Média | `realIndex` derivado do scroll garante que `goNext()` sempre navega no bloco do meio |
| **Empty array crash** | Baixa | Guard clause no início: se array vazio → renderizar fallback silencioso |

## 6. Critérios de Aceitação

- [ ] **P1. Data Layer**: dados extraídos para `src/lib/data/testimonials.json`, import substituído no componente
- [ ] **P1. Guard clause**: carrossel renderiza fallback se array vazio (sem crash)
- [ ] **P2. `cardsPerView`**: `$state<number>(1)` atualizado via ResizeObserver no container
- [ ] **P2. DOM growth cap**: `displayBlock.length ≤ testimonials.length × 2`
- [ ] **P2. `$derived displayBlock`**: repete itens até largura ≥ `(cardsPerView + 2) × cardWidth`
- [ ] **P2. `$derived tripleBlock`**: `[...displayBlock, ...displayBlock, ...displayBlock]`
- [ ] **P3. Scroll init no bloco do meio**: sem `scrollTo(0)` visível
- [ ] **P3. Boundary detection com jump instantâneo**: `behavior: 'instant'`, sem flicker de scroll-snap
- [ ] **P3. `scroll-snap-type` desabilitado durante jumps**: classe condicional `.no-snap`
- [ ] **P3. `realIndex`**: derivado de `(Math.round(scrollLeft / step) % displayBlock.length) % total`
- [ ] **P3. `goToReal(index)`**: navega no bloco do meio
- [ ] **P3. `goNext()`**: smooth wrap no bloco do meio, sem double-jump
- [ ] **P3. `finishDrag()`**: snap normalizado para bloco do meio
- [ ] **P3. Dots**: `total` dots mapeados via `realIndex`
- [ ] **P4. `aria-hidden="true"` nos blocos 1 e 3**
- [ ] **P4. `aria-label` reflete `realIndex + 1` de `total` (apenas bloco do meio)**
- [ ] **P4. `prefers-reduced-motion` preservado**
- [ ] **P4. `contain: layout style paint` e `scroll-snap-stop: always` preservados**
- [ ] **P4. ResizeObservers merged em único observer**
- [ ] **P4. Posição preservada no resize via `tick()`**
- [ ] `npm run check` passa sem erros
- [ ] `npm run build` gera build limpo
- [ ] Visualmente consistente com o resto da página (glass-panel, cores, tipografia)
- [ ] Responsivo em mobile (768px) — cards com `width: min(350px, 80vw)`

## 7. Referências

- Issue: [#5](https://github.com/zan-ia/landpage/issues/5)
- Docs: `docs/INSTITUCIONAL.md`
- Instruções: `svelte.instructions.md`, `css.instructions.md`, `html.instructions.md`, `style-architecture.instructions.md`
- Design tokens: `src/lib/app.css`
