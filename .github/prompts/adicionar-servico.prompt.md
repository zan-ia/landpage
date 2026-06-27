---
description: "Adiciona um novo card de serviço na seção Solutions do site Zan.IA. Edita o array de serviços no componente Svelte."
argument-hint: "Nome do serviço, ícone Material e descrição (ex: 'Desenvolvimento de Chatbots - smart_toy: Criamos chatbots inteligentes...')"
---

# Adicionar Card de Serviço

Adicione uma nova entrada ao array de serviços no componente `src/lib/components/Solutions.svelte`.

## Template do Objeto (TypeScript)

```typescript
{
  icon: "smart_toy",          // Nome do ícone Material Symbols
  title: "Título do Serviço",
  description: "Descrição do serviço em 2-3 frases destacando o benefício."
}
```

## Template Visual (Svelte)

O componente renderiza automaticamente cada serviço como:

```svelte
<div class="solutions__card glass-panel">
  <div class="solutions__icon-wrapper">
    <span class="material-symbols-outlined solutions__icon">{service.icon}</span>
  </div>
  <h3 class="solutions__card-title">{service.title}</h3>
  <p class="solutions__card-desc">{service.description}</p>
</div>
```

## Escolha de Ícones

Use [Material Symbols](https://fonts.google.com/icons) — estilo Outlined:
- Sistemas/Sites: `language`, `web`, `devices`
- IA/Agentes: `smart_toy`, `psychology`, `robot`
- Mídia/Conteúdo: `movie`, `palette`, `auto_awesome`
- Automação: `settings_suggest`, `sync`, `bolt`
- Dados/Analytics: `analytics`, `insights`, `bar_chart`

## Procedimento

1. Abra `src/lib/components/Solutions.svelte`
2. Localize o array de serviços no `<script lang="ts">`
3. Adicione o novo objeto seguindo a interface existente
4. Escolha um ícone adequado do Material Symbols
5. O grid se ajusta automaticamente (3 colunas desktop, 1 mobile)
6. Execute `npm run dev` para verificar
