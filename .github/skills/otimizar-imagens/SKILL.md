---
name: otimizar-imagens
description: "Otimiza imagens da landing page Zan.IA (SvelteKit). Converte para formatos modernos (WebP/AVIF), gera srcset com múltiplas resoluções, aplica lazy loading, e sugere compressão."
argument-hint: "Caminho da imagem ou diretório a otimizar (ex: 'static/assets/images/hero.jpg', 'todas as imagens do projeto')"
user-invocable: true
disable-model-invocation: false
---

# Skill: Otimização de Imagens (SvelteKit)

Skill para gerenciar e otimizar todas as imagens do projeto Zan.IA.

## Localização dos Assets

```
static/assets/images/     ← Coloque as imagens aqui
  └── hero.webp           ← Referencie como /assets/images/hero.webp
```

Nos componentes Svelte:
```svelte
<img src="/assets/images/hero.webp" alt="..." />
```

## Workflow de Otimização

### 1. Conversão para Formatos Modernos

```bash
# WebP (qualidade 80)
cwebp -q 80 input.jpg -o static/assets/images/output.webp

# AVIF (qualidade 60)
avifenc -s 8 -q 60 input.jpg -o static/assets/images/output.avif
```

### 2. Imagem com Fallback (Svelte)

```svelte
<picture>
  <source srcset="/assets/images/hero.avif" type="image/avif">
  <source srcset="/assets/images/hero.webp" type="image/webp">
  <img
    src="/assets/images/hero.jpg"
    alt="Zan.IA — Deep Tech Systems"
    loading="lazy"
    width="1200"
    height="630"
    decoding="async"
  >
</picture>
```

### 3. Imagens Críticas (acima da dobra)

```svelte
<img
  src="/assets/images/hero.webp"
  alt="Hero Zan.IA"
  fetchpriority="high"
  width="1200"
  height="630"
  decoding="async"
>
```

### 4. Convenções de Nomenclatura

```
static/assets/images/
├── hero.webp          # Hero principal (acima da dobra)
├── hero.jpg           # Fallback
├── servico-sistemas.webp
├── servico-agentes.webp
├── servico-midia.webp
└── og-image.jpg       # Open Graph (1200x630px)
```

## Checklist por Imagem

- [ ] Local: `static/assets/images/` (não `build/assets/images/`)
- [ ] Referência: `/assets/images/nome.ext` (path absoluto a partir da raiz)
- [ ] Formato: WebP + fallback JPEG/PNG
- [ ] Tamanho: < 100kB (ideal), < 200kB (aceitável)
- [ ] Dimensões: `width` + `height` explícitos
- [ ] `loading="lazy"` em imagens abaixo da dobra
- [ ] `fetchpriority="high"` apenas na hero
- [ ] `alt` descritivo

## Procedimento

1. **Mapeie** todas as imagens referenciadas nos componentes `src/lib/components/*.svelte`
2. **Identifique** quais estão em CDN externo vs. locais
3. **Converta** para WebP e coloque em `static/assets/images/`
4. **Atualize** os `src` nos componentes Svelte
5. **Remova** dependências de CDNs externos de imagens
6. **Verifique** visualmente com `npm run dev`

## Tolerâncias

| Cenário | Ação |
|---------|------|
| Imagem heróica > 200kB | Comprimir para < 100kB |
| Imagem decorativa > 100kB | Redimensionar + comprimir |
| PNG com transparência | WebP suporta alpha, converter |
| GIF animado | Converter para WebP animado ou video MP4 |
| SVG | Manter SVG (vetorial), otimizar com SVGO se > 10kB |
