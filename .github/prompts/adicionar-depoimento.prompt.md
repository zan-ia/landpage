---
description: "Adiciona um novo depoimento ao carrossel de testimonials da landing page Zan.IA. Gera o HTML com estrelas, texto e autor seguindo o padrão existente."
argument-hint: "Nome do cliente, cargo/empresa e texto do depoimento (ex: 'Maria Silva, CEO TechCorp: A Zan.IA transformou nossa presença digital...')"
---

# Adicionar Depoimento

Adicione um novo card de depoimento ao carrossel de testimonials.

## Template do Card

```html
<div class="testimonial-card glass-panel rounded-2xl p-8 min-w-[340px] md:min-w-[400px] flex-shrink-0 snap-start flex flex-col gap-4">
  <div class="flex items-center gap-1 text-yellow-400">
    <span class="material-symbols-outlined text-xl">star</span>
    <span class="material-symbols-outlined text-xl">star</span>
    <span class="material-symbols-outlined text-xl">star</span>
    <span class="material-symbols-outlined text-xl">star</span>
    <span class="material-symbols-outlined text-xl">star</span>
  </div>
  <p class="text-[var(--color-on-surface)]/80 leading-relaxed italic">"Texto do depoimento..."</p>
  <div class="mt-auto">
    <p class="font-semibold text-[var(--color-on-surface)]">Nome do Cliente</p>
    <p class="text-sm text-[var(--color-on-surface)]/60">Cargo, Empresa</p>
  </div>
</div>
```

## Procedimento

1. Localize a seção de testimonials em `build/index.html`
2. Adicione o novo card dentro do container com scroll horizontal (`snap-x`)
3. Mantenha a largura consistente (`min-w-[340px]`)
4. Preserve a estrutura de 5 estrelas
