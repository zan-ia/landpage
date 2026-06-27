---
description: "Executa uma auditoria SEO completa na landing page Zan.IA (SvelteKit) e aplica correções. Verifica meta tags, structured data, Open Graph, performance e acessibilidade."
argument-hint: "[opcional] Escopo da auditoria: 'completa', 'meta-tags', 'open-graph', 'structured-data'"
---

# Otimização SEO — Zan.IA (SvelteKit)

Execute uma auditoria SEO completa nos arquivos fonte e corrija todos os problemas.

## Arquivos a Auditar

| Arquivo | O que verificar |
|---------|----------------|
| `src/app.html` | Meta tags, Open Graph, fontes, lang, charset |
| `src/routes/+layout.svelte` | Estrutura semântica, headings, ARIA |
| `src/lib/components/Hero.svelte` | `<h1>` único, texto, imagens |
| `src/lib/components/Footer.svelte` | Links, schema.org |
| `static/robots.txt` | Configuração de rastreamento |

## Checklist

### Meta Tags (`src/app.html`)
- [ ] `<title>` descritivo (50-60 chars) com palavra-chave principal
- [ ] `<meta name="description">` (150-160 chars) com call-to-action
- [ ] `<meta name="viewport" content="width=device-width, initial-scale=1">`
- [ ] `<html lang="pt-BR">`
- [ ] `<meta charset="utf-8">` como primeiro elemento

### Open Graph / Social (`src/app.html`)
- [ ] `og:title` — mesmo padrão do title
- [ ] `og:description` — resumo atrativo
- [ ] `og:image` — URL absoluta (1200x630px)
- [ ] `og:url` — URL canônica (`https://www.zan.ia.br/`)
- [ ] `og:type` — `website`
- [ ] `og:locale` — `pt_BR`
- [ ] `twitter:card` — `summary_large_image`

### Structured Data (JSON-LD)
- [ ] `Organization` schema com nome, logo, URL, descrição
- [ ] `WebSite` schema
- [ ] Adicionar no `<head>` de `src/app.html` ou no `+layout.svelte`

### Técnico (Componentes)
- [ ] `<h1>` único em `Hero.svelte`
- [ ] Headings hierárquicos (h1 → h2 → h3, sem pular)
- [ ] Imagens com `alt` descritivo
- [ ] Links externos com `rel="noopener noreferrer"`
- [ ] `aria-label` em elementos interativos sem texto visível
- [ ] `aria-current="page"` no link ativo

### Performance SEO
- [ ] Imagens com `width` e `height` explícitos
- [ ] Fontes com `font-display: swap` + `rel="preconnect"`
- [ ] `robots.txt` permite rastreamento
- [ ] Sitemap (se aplicável)

## Procedimento

1. Audite `src/app.html` primeiro (meta tags)
2. Audite cada componente por headings e alt text
3. Corrija problemas diretamente nos arquivos `.svelte`
4. Execute `npm run build && npm run preview` para verificar

## Saída Esperada
Relatório com:
- Problemas encontrados (arquivo + linha)
- Correções aplicadas
- Itens que requerem ação manual
