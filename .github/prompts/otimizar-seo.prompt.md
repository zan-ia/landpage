---
description: "Executa uma auditoria SEO completa na landing page Zan.IA e aplica correções. Verifica meta tags, structured data, Open Graph, performance e acessibilidade."
agent: "agent"
---

# Otimização SEO - Zan.IA

Execute uma auditoria SEO completa no `build/index.html` e corrija todos os problemas encontrados.

## Checklist

### Meta Tags
- [ ] `<title>` descritivo (50-60 chars) com palavra-chave principal
- [ ] `<meta name="description">` (150-160 chars) com call-to-action
- [ ] `<meta name="viewport" content="width=device-width, initial-scale=1">`
- [ ] `<html lang="pt-BR">` configurado corretamente
- [ ] `<meta charset="utf-8">` como primeiro elemento no `<head>`

### Open Graph / Social
- [ ] `og:title` — mesmo padrão do title
- [ ] `og:description` — resumo atrativo
- [ ] `og:image` — URL da imagem de compartilhamento (1200x630px)
- [ ] `og:url` — URL canônica
- [ ] `og:type` — `website`
- [ ] `twitter:card` — `summary_large_image`

### Structured Data (JSON-LD)
- [ ] `Organization` schema com nome, logo, URL, descrição
- [ ] `WebSite` schema com search action (se aplicável)
- [ ] `LocalBusiness` schema (se endereço físico for relevante)

### Técnico
- [ ] Canonical URL definida
- [ ] Links internos funcionando
- [ ] Imagens com `alt` descritivo
- [ ] Headings em ordem hierárquica (`h1` único, `h2` para seções)
- [ ] URLs de links externos com `rel="noopener noreferrer"` quando `target="_blank"`

### Performance SEO
- [ ] Verificar se há render-blocking resources desnecessários
- [ ] Imagens com `width` e `height` para evitar CLS
- [ ] Texto compressível (gzip/brotli habilitado no servidor)

## Saída Esperada
Relatório com:
- Problemas encontrados (com linha no HTML)
- Correções aplicadas
- Itens que requerem ação manual (ex: configurar servidor)
