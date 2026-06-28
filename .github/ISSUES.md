# Análise de Refatoração — Landing Page

**Data:** 2026-06-25  
**Arquivo analisado:** `build/index.html`

---

## Resumo Executivo

O site atual é um HTML monolítico com dependências externas pesadas. Foram identificadas **3 áreas críticas** de melhoria:

| # | Problema | Prioridade | Esforço |
|---|----------|------------|---------|
| 1 | Tailwind CSS via CDN | Alta | Médio |
| 2 | HTML monolítico sem modularização | Alta | Alto |
| 3 | Imagens externas não locais | Média | Baixo |

---

## ISSUE #1: Remover Tailwind CSS e migrar para CSS vanilla

**Título:** `Remover Tailwind CSS e migrar para CSS vanilla organizado`  
**Labels:** `enhancement`, `refactor`, `css`

### Descrição

O projeto atual depende do Tailwind CSS via CDN, adicionando sobrecarga desnecessária e dependência externa. Precisamos migrar para CSS vanilla com boas práticas.

### Problemas identificados

1. **Dependência CDN** — Tailwind carregado via `<script src="cdn.tailwindcss.com">`
2. **Classes utilitárias inline** — HTML verboso com `flex`, `items-center`, `gap-2`, `px-4`, etc.
3. **Config inline** — Config do Tailwind em `<script>` dentro do `<head>`
4. **CSS não modular** — Estilos customizados em `<style>` inline

### Estrutura CSS proposta

```
css/
├── base/
│   ├── reset.css          # Reset/normalize
│   ├── variables.css      # Design tokens
│   └── typography.css     # Tipografia
├── components/
│   ├── header.css
│   ├── hero.css
│   ├── authority.css
│   ├── solutions.css
│   ├── differential.css
│   ├── testimonials.css
│   ├── cta.css
│   └── footer.css
├── utilities/
│   ├── glass-panel.css
│   ├── animations.css
│   └── responsive.css
└── main.css               # @import de tudo
```

### Design tokens a migrar

```css
:root {
  /* Cores - Material Design 3 */
  --color-surface: #0c1324;
  --color-surface-container: #191f31;
  --color-surface-container-lowest: #070d1f;
  --color-primary: #baf2ff;
  --color-primary-container: #00e0ff;
  --color-on-surface: #dce1fb;
  --color-on-primary: #00363f;
  --color-on-primary-container: #005f6d;
  --color-outline-variant: #3b494c;
  
  /* Fontes */
  --font-display: 'Space Grotesk', sans-serif;
  --font-body: 'Geist', sans-serif;
  --font-code: 'JetBrains Mono', monospace;
  
  /* Espaçamentos */
  --spacing-gutter: 24px;
  --spacing-margin-mobile: 16px;
  --spacing-margin-desktop: 64px;
  --spacing-base: 8px;
  
  /* Breakpoints */
  --breakpoint-mobile: 768px;
}
```

### Checklist

- [ ] Criar estrutura `css/` com módulos
- [ ] Migrar design tokens para CSS custom properties
- [ ] Converter classes utilitárias para classes semânticas
- [ ] Migrar estilos (glass-panel, electric-glow, scanning-line)
- [ ] Organizar responsividade com media queries
- [ ] Remover dependência Tailwind
- [ ] Verificar equivalência visual

---

## ISSUE #2: Modularizar HTML com Web Components

**Título:** `Modularizar HTML com Web Components vanilla`  
**Labels:** `enhancement`, `architecture`, `web-components`

### Descrição

O `index.html` atual tem ~350 linhas de HTML monolítico. Precisamos modularizar em componentes reutilizáveis usando Web Components vanilla.

### Estrutura de componentes proposta

```
src/
├── components/
│   ├── zan-header/
│   │   ├── zan-header.js
│   │   └── zan-header.css
│   ├── zan-hero/
│   │   ├── zan-hero.js
│   │   └── zan-hero.css
│   ├── zan-authority/
│   │   ├── zan-authority.js
│   │   └── zan-authority.css
│   ├── zan-solutions/
│   │   ├── zan-solutions.js
│   │   └── zan-solutions.css
│   ├── zan-differential/
│   │   ├── zan-differential.js
│   │   └── zan-differential.css
│   ├── zan-testimonials/
│   │   ├── zan-testimonials.js
│   │   └── zan-testimonials.css
│   ├── zan-cta/
│   │   ├── zan-cta.js
│   │   └── zan-cta.css
│   ├── zan-footer/
│   │   ├── zan-footer.js
│   │   └── zan-footer.css
│   └── zan-card/
│       ├── zan-card.js
│       └── zan-card.css
├── js/
│   └── main.js           # Registra todos os componentes
├── css/
│   └── main.css          # Imports de CSS
└── index.html            # Apenas tags de componentes
```

### Exemplo de Web Component

```javascript
// zan-hero.js
class ZanHero extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
  }

  connectedCallback() {
    this.shadowRoot.innerHTML = `
      <link rel="stylesheet" href="css/components/hero.css">
      <section class="hero">
        <div class="hero__background">
          <img src="${this.getAttribute('bg-image')}" alt="" />
        </div>
        <div class="hero__content">
          <span class="hero__badge">Deep Tech Revolution</span>
          <h1 class="hero__title">
            Tecnologia de Ponta e Conteúdo de Elite.
            <span class="hero__title--highlight">Agora ao Seu Alcance.</span>
          </h1>
          <p class="hero__description">
            Unimos a experiência de veteranos da indústria à inovação da IA...
          </p>
          <button class="hero__cta">Transformar meu Negócio</button>
        </div>
      </section>
    `;
  }
}

customElements.define('zan-hero', ZanHero);
```

### Uso no HTML resultante

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Nome da Empresa] | [Slogan]</title>
  <link rel="stylesheet" href="css/main.css">
</head>
<body>
  <zan-header></zan-header>
  
  <main>
    <zan-hero 
      bg-image="assets/images/hero-bg.webp"
      badge="Deep Tech Revolution"
      title="Tecnologia de Ponta e Conteúdo de Elite."
      highlight="Agora ao Seu Alcance."
      description="Unimos a experiência de veteranos da indústria à inovação da IA..."
      cta-text="Transformar meu Negócio"
    ></zan-hero>
    
    <zan-authority
      image="assets/images/authority-circuit.webp"
      image-alt="Circuitos integrados de alta tecnologia"
      stat-1-value="20+"
      stat-1-label="Anos de Experiência"
      stat-2-value="100%"
      stat-2-label="IA Nativa"
    ></zan-authority>
    
    <zan-solutions></zan-solutions>
    
    <zan-differential></zan-differential>
    
    <zan-testimonials></zan-testimonials>
    
    <zan-cta></zan-cta>
  </main>
  
  <zan-footer></zan-footer>
  
  <script src="js/main.js"></script>
</body>
</html>
```

### Checklist

- [ ] Criar estrutura `src/components/`
- [ ] Implementar `ZanHeader` component
- [ ] Implementar `ZanHero` component
- [ ] Implementar `ZanAuthority` component
- [ ] Implementar `ZanSolutions` component (com `ZanCard` sub-component)
- [ ] Implementar `ZanDifferential` component
- [ ] Implementar `ZanTestimonials` component
- [ ] Implementar `ZanCTA` component
- [ ] Implementar `ZanFooter` component
- [ ] Criar `js/main.js` para registro
- [ ] Simplificar `index.html` para usar apenas tags de componentes

---

## ISSUE #3: Baixar imagens externas para local

**Título:** `Baixar imagens externas e atualizar referências`  
**Labels:** `enhancement`, `assets`, `images`

### Descrição

O HTML contém **2 imagens** referenciadas de URLs externas do Google (aida-public) que precisam ser baixadas para o repositório.

### Imagens identificadas

| # | Descrição | URL | Destino |
|---|-----------|-----|---------|
| 1 | Hero background (atmosférico/sci-fi) | `https://lh3.googleusercontent.com/aida-public/AB6AXuCG...` | `assets/images/hero-bg.webp` |
| 2 | Authority section (circuitos/neural) | `https://lh3.googleusercontent.com/aida-public/AB6AXuD7D...` | `assets/images/authority-circuit.webp` |

### Imagens já locais

| Arquivo | Tamanho | Status |
|---------|---------|--------|
| `cover.png` | 1.6 MB | ✅ Local |
| `logo.png` | 1.4 MB | ✅ Local |
| `favicon.ico` | 26 KB | ✅ Local |
| `logo.ico` | 9 KB | ✅ Local |

### Comandos para download

```bash
# Hero background
curl -L -o "build/assets/images/hero-bg.webp" \
  "https://lh3.googleusercontent.com/aida-public/AB6AXuCG118dxGUabm6Ult23q7DeSqtlXjTHRytqZabfShREjy8GQCAVkiSfFgGabUTXvw1fcOTI0RFp7Z1hjDAXaFjjY_gdw498E3zPmmfPv1XIJuJRQ2-0bg1wgDoP9iVzF02x7KtuOcdA0_fGbbgCdjswm3eP-LcU4nKJn98fdirfQqKJBWbIvkRAdzvkyhOqZsl12tOTNjPgYHEdq7aEM8vQMUuccE-59gqlK7qY6rbPaiYJSrVV54wd7lVOl3xmqZcqY9JgOfCSogaZ"

# Authority circuit image
curl -L -o "build/assets/images/authority-circuit.webp" \
  "https://lh3.googleusercontent.com/aida-public/AB6AXuD7DPiU4hsYr5ST7j6EBhDhDsPXfe8dOCA50bGsjV6zSGfqf0VUYAxjP_pXUSJhA9SMk1nhqHpGeqQKik9YWjHsCabqxth94PTt4PNHJga7hHyi7sxpYh3l1mEAOEgXILwgNbGosROcNJUc39kAPsTepY2VnwWtrarBfK3ByHA8iMvxhNB6BHXQSpwTnRt0C_jQCsqOOn5er8x4kZX4dW9oPdvEQzUvDmlPCjiJdq_PuOPt-kzC7odb_t6nfVfDl9T_ZdsVcfsfJxTv"
```

### Referências externas restantes (não-imagens)

| Recurso | Tipo | CDN |
|---------|------|-----|
| Tailwind CSS | CSS Framework | `cdn.tailwindcss.com` |
| Space Grotesk | Fonte | `fonts.googleapis.com` |
| JetBrains Mono | Fonte | `fonts.googleapis.com` |
| Geist | Fonte | `fonts.googleapis.com` |
| Material Symbols | Ícones | `fonts.googleapis.com` |

> **Nota:** As fontes e ícones do Google Fonts podem permanecer via CDN (prática comum) ou serem baixados localmente para PWA/offline.

### Checklist

- [ ] Baixar hero background image
- [ ] Baixar authority circuit image
- [ ] Converter para WebP (otimização)
- [ ] Atualizar referências no HTML
- [ ] Adicionar imagens ao `.gitignore` se necessário
- [ ] Verificar carregamento offline

---

## Plano de Execução Sugerido

```
Fase 1: Imagens (Issue #3) — 30 min
  └── Baixar e organizar assets

Fase 2: CSS (Issue #1) — 2-3 horas
  ├── Criar estrutura css/
  ├── Migrar design tokens
  ├── Converter classes
  └── Remover Tailwind

Fase 3: Componentes (Issue #2) — 4-6 horas
  ├── Criar estrutura src/components/
  ├── Implementar Web Components
  ├── Simplificar index.html
  └── Testar equivalência
```

---

## Referências

- [Web Components MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_components)
- [CSS Custom Properties MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)
- [Material Design 3 Colors](https://m3.material.io/styles/color/overview)
