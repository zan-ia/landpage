---
description: "Adds a new testimonial to the landing page testimonials carousel. Edits the testimonials[] array in the Svelte component."
argument-hint: "Client name, role/company and testimonial text (e.g., 'Maria Silva, CEO TechCorp: The company transformed our digital presence...')"
agent: "criador-conteudo"
---

# Add Testimonial

Add a new entry to the `testimonials[]` array in the `src/lib/components/Testimonials.svelte` component.

## Object Template

```typescript
{
  stars: 5,
  text: "Testimonial text describing the problem, solution, and result achieved with the company.",
  initials: "MS",
  name: "Maria S.",
  role: "CEO, TechCorp"
}
```

## Rules

- **stars:** always 5 (maximum)
- **text:** 1-3 sentences. Structure: Problem → Solution → Result. Include credible metrics.
- **initials:** 2 uppercase letters (first letter of first name + last name)
- **name:** Name + last name initial (e.g., "Maria S.")
- **role:** Position + company (separated by comma)

## Procedure

1. Open `src/lib/components/Testimonials.svelte`
2. Locate the `testimonials: Testimonial[] = [...]` array in `<script lang="ts">`
3. Add the new object BEFORE the last item (to maintain order)
4. The carousel uses automatic clones — no need to duplicate HTML
5. The indicator dots adjust automatically (`testimonials.length`)
6. Run `npm run dev` to verify visually
