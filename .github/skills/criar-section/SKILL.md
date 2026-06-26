---
context: fork
name: criar-section
description: "Cria uma nova seção HTML na landing page Zan.IA seguindo os padrões visuais existentes. Gera o HTML completo com glass panels, gradientes, tipografia consistente e animações padrão do projeto."
---

# Skill: Criar Nova Seção

Cria uma nova seção HTML em `build/index.html` mantendo consistência visual com o resto da landing page.

## Estrutura de Seção

```html
<!-- ════════════════════════════════════════════ -->
<!-- NOME_DA_SEÇÃO -->
<!-- ════════════════════════════════════════════ -->
<section id="nome-da-secao" class="relative py-24 px-4 md:px-8 overflow-hidden">
  <!-- Background decorativo -->
  <div class="absolute inset-0 bg-gradient-to-b from-[var(--color-surface)] via-[var(--color-surface-container)] to-[var(--color-surface)] pointer-events-none"></div>

  <div class="max-w-6xl mx-auto relative z-10">
    <!-- Header da seção -->
    <div class="text-center max-w-3xl mx-auto mb-16">
      <span class="inline-block px-4 py-1.5 rounded-full bg-[var(--color-primary)]/10 text-[var(--color-primary)] text-sm font-medium border border-[var(--color-primary)]/20 mb-4">
        badge_label
      </span>
      <h2 class="text-3xl md:text-4xl lg:text-5xl font-bold text-[var(--color-on-surface)]">
        Título da Seção
      </h2>
      <p class="mt-4 text-lg text-[var(--color-on-surface)]/70 max-w-2xl mx-auto">
        Descrição ou subtítulo da seção.
      </p>
    </div>

    <!-- Grid ou conteúdo: use glass-panel para cards -->
    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
      <!-- Cards padrão glass-panel -->
      <div class="group glass-panel rounded-2xl p-8 transition-all duration-500 hover:scale-[1.02] hover:shadow-2xl">
        <!-- conteúdo -->
      </div>
    </div>
  </div>
</section>
```

## Design Tokens a Usar

| Token | Onde usar |
|-------|-----------|
| `--color-surface` | Background principal da seção |
| `--color-surface-container` | Background de containers/cards |
| `--color-primary` | Badges, destaques, links |
| `--color-primary-container` | Gradientes, backgrounds de ícones |
| `--color-on-surface` | Texto principal |
| `--color-on-surface`/70 | Texto secundário/descrições |
| `--color-on-primary` | Texto em backgrounds primary |

## Animações Padrão

```css
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(30px); }
  to { opacity: 1; transform: translateY(0); }
}

/* Aplicar com animate-fadeInUp na seção ou cards */
```

Inclua o bloco `@keyframes fadeInUp` apenas se ele ainda não estiver definido em `build/index.html` ou nos seus stylesheets vinculados. Se já estiver definido, aplique a classe `animate-fadeInUp` sem redeclarar os keyframes.

## Procedimento

1. **Analise** o `build/index.html` para entender o padrão visual atual. Se o arquivo não existir ou não puder ser lido, pare e peça ao usuário para fornecer o caminho do arquivo ou compartilhar o HTML relevante antes de prosseguir.
2. **Insira** a nova seção imediatamente antes do elemento `<footer>`, a menos que o usuário especifique uma posição diferente explicitamente.
3. **Crie** a seção seguindo a estrutura acima
4. **Adapte** o grid de acordo com a quantidade de itens de conteúdo distintos: 1 item → 1 coluna, 2 itens → 2 colunas, 3+ itens → 3 colunas
5. **Mantenha** os estilos existentes — não adicione CSS novo se o padrão já cobre

## Checklist de Consistência

- [ ] Usa `glass-panel` para cards/containers
- [ ] Badge com gradiente e borda sutil
- [ ] Tipografia com `font-geist` (ou `font-space-grotesk` para displays)
- [ ] Transições `duration-500` em hover
- [ ] Responsivo (`grid-cols-1 md:grid-cols-*`)
- [ ] Ícones Material Symbols quando aplicável
- [ ] Padding vertical `py-24`
- [ ] max-width `max-w-6xl` para conteúdo
