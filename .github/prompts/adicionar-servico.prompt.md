---
description: "Adds a new service card to the Solutions section of the landing page. Edits the services array in the Svelte component."
argument-hint: "Service name, Material icon and description (e.g., 'Chatbot Development - smart_toy: We create intelligent chatbots...')"
agent: "criador-conteudo"
---

# Add Service Card

Add a new entry to the services array in the `src/lib/components/Solutions.svelte` component.

## Object Template (TypeScript)

```typescript
{
  icon: "smart_toy",          // Material Symbols icon name
  title: "Service Title",
  description: "Service description in 2-3 sentences highlighting the benefit."
}
```

## Visual Template (Svelte)

The component automatically renders each service as:

```svelte
<div class="solutions__card glass-panel">
  <div class="solutions__icon-wrapper">
    <span class="material-symbols-outlined solutions__icon">{service.icon}</span>
  </div>
  <h3 class="solutions__card-title">{service.title}</h3>
  <p class="solutions__card-desc">{service.description}</p>
</div>
```

## Icon Choices

Use [Material Symbols](https://fonts.google.com/icons) — Outlined style:
- Systems/Websites: `language`, `web`, `devices`
- AI/Agents: `smart_toy`, `psychology`, `robot`
- Media/Content: `movie`, `palette`, `auto_awesome`
- Automation: `settings_suggest`, `sync`, `bolt`
- Data/Analytics: `analytics`, `insights`, `bar_chart`

## Procedure

1. Open `src/lib/components/Solutions.svelte`
2. Locate the services array in `<script lang="ts">`
3. Add the new object following the existing interface
4. Choose an appropriate Material Symbols icon
5. The grid adjusts automatically (3 columns desktop, 1 mobile)
6. Run `npm run dev` to verify
