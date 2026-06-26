---
context: fork
name: otimizar-imagens
description: "Otimiza imagens da landing page Zan.IA. Converte para formatos modernos (WebP/AVIF), gera srcset com múltiplas resoluções, aplica lazy loading, e sugere compressão. Reduz o peso total de imagens mantendo qualidade visual."
---

# Skill: Otimização de Imagens

Skill para gerenciar e otimizar todas as imagens do projeto Zan.IA.

## Workflow de Otimização

### 1. Conversão para Formatos Modernos

```bash
# WebP (qualidade 80): ~30% menor que JPEG/PNG
cwebp -q 80 input.jpg -o input.webp

# AVIF (qualidade 60): ~50% menor que JPEG/PNG
avifenc -s 8 -q 60 input.jpg -o input.avif
```

> Se `cwebp` ou `avifenc` não estiverem instalados, use ferramentas online como [Squoosh](https://squoosh.app) ou [Convertio](https://convertio.co).

### 2. Estrutura `<picture>` com Fallback

```html
<picture>
  <source srcset="/assets/images/hero.avif" type="image/avif">
  <source srcset="/assets/images/hero.webp" type="image/webp">
  <img
    src="/assets/images/hero.jpg"
    alt="Descrição da imagem"
    loading="lazy"
    width="1200"
    height="630"
    class="w-full h-auto"
  >
</picture>
```

### 3. Imagens Críticas (acima da dobra)

Para a imagem hero (primeira da página):
```html
<img
  src="/assets/images/hero.webp"
  alt="Hero Zan.IA"
  fetchpriority="high"
  width="1200"
  height="630"
  class="w-full h-auto"
  decoding="async"
>
```

### 4. Convenções de Nomenclatura

```
assets/images/
├── hero.webp          # Hero principal (acima da dobra)
├── hero.jpg           # Fallback
├── servico-sistemas.webp
├── servico-agentes.webp
├── servico-midia.webp
├── icone-contato.webp
└── og-image.jpg       # Open Graph (1200x630px)
```

## Checklist por Imagem

- [ ] Formato: WebP + fallback JPEG/PNG (AVIF se suporte for aceitável)
- [ ] Tamanho: < 100kB (ideal), < 200kB (aceitável)
- [ ] Dimensões: explícitas (`width` + `height`)
- [ ] `loading="lazy"` em imagens abaixo da dobra
- [ ] `fetchpriority="high"` apenas na hero
- [ ] `alt` descritivo e relevante (nunca vazio)
- [ ] Nome consistente e em minúsculas (hífens para separar)

## Procedimento

1. **Mapeie** todas as imagens referenciadas em `build/index.html`
2. **Identifique** quais estão em CDN externo vs. locais
3. **Converta** para WebP usando a ferramenta disponível
4. **Atualize** o HTML com `<picture>` e fallbacks
5. **Remova** dependências de CDNs externos de imagens
6. **Verifique** visualmente se a qualidade está aceitável

## Tolerâncias

| Cenário | Ação |
|---------|------|
| Imagem heróica > 200kB | Comprimir para < 100kB |
| Imagem decorativa > 100kB | Redimensionar + comprimir |
| PNG com transparência | WebP suporta alpha, converter |
| GIF animado | Converter para WebP animado ou video MP4 |
| SVG | Manter SVG (vetorial), otimizar com SVGO se > 10kB |
