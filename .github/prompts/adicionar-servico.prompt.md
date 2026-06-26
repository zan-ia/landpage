---
description: "Adiciona um novo card de serviço na seção Solutions do site Zan.IA. Cria o HTML seguindo o padrão visual existente (glass panel, ícone Material, título, descrição)."
argument-hint: "Nome do serviço e descrição (ex: 'Desenvolvimento de Chatbots Personalizados - Criamos chatbots inteligentes...')"
---

# Adicionar Card de Serviço

Adicione um novo card na grid de soluções da landing page seguindo o padrão visual existente.

## Template do Card

```html
<div class="group glass-panel rounded-2xl p-8 transition-all duration-500 hover:scale-[1.02] hover:shadow-2xl flex flex-col items-start gap-4">
  <div class="w-14 h-14 rounded-xl bg-gradient-to-br from-[var(--color-primary)] to-[var(--color-primary-container)] flex items-center justify-center text-[var(--color-on-primary)] shadow-lg">
    <span class="material-symbols-outlined text-3xl">icon_name</span>
  </div>
  <h3 class="text-xl font-semibold text-[var(--color-on-surface)]">Título do Serviço</h3>
  <p class="text-[var(--color-on-surface)]/70 leading-relaxed">Descrição do serviço em 2-3 frases.</p>
</div>
```

## Procedimento

1. Leia `build/index.html` e identifique a seção `.solutions-grid` (ou similar)
2. Adicione o novo card seguindo a mesma estrutura dos existentes
3. Escolha um ícone do [Material Symbols](https://fonts.google.com/icons) adequado ao serviço
4. Mantenha o gap/ padding consistente com os demais cards
5. Não remova ou modifique cards existentes
