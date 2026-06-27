---
description: "Adiciona um novo depoimento ao carrossel de testimonials da landing page Zan.IA. Edita o array testimonials[] no componente Svelte."
argument-hint: "Nome do cliente, cargo/empresa e texto do depoimento (ex: 'Maria Silva, CEO TechCorp: A Zan.IA transformou nossa presença digital...')"
---

# Adicionar Depoimento

Adicione uma nova entrada ao array `testimonials[]` no componente `src/lib/components/Testimonials.svelte`.

## Template do Objeto

```typescript
{
  stars: 5,
  text: "Texto do depoimento descrevendo o problema, a solução e o resultado obtido com a Zan.IA.",
  initials: "MS",
  name: "Maria S.",
  role: "CEO, TechCorp"
}
```

## Regras

- **stars:** sempre 5 (máximo)
- **text:** 1-3 frases. Estrutura: Problema → Solução → Resultado. Incluir métricas críveis.
- **initials:** 2 letras maiúsculas (primeira letra do nome + sobrenome)
- **name:** Nome + inicial do sobrenome (ex: "Maria S.")
- **role:** Cargo + empresa (separados por vírgula)

## Procedimento

1. Abra `src/lib/components/Testimonials.svelte`
2. Localize o array `testimonials: Testimonial[] = [...]` no `<script lang="ts">`
3. Adicione o novo objeto ANTES do último item (para manter ordem)
4. O carrossel usa clones automáticos — não precisa duplicar HTML
5. Os dots indicadores se ajustam automaticamente (`testimonials.length`)
6. Execute `npm run dev` para verificar visualmente
