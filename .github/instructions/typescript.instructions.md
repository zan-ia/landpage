---
name: "TypeScript"
description: "TypeScript conventions for the Zan.IA SvelteKit project. Use when: writing or editing .ts files, adding type definitions, or working with SvelteKit TypeScript configuration."
applyTo: "src/**/*.ts"
---

# TypeScript — Convenções SvelteKit

## Configuração do Projeto

- `tsconfig.json` estende `./.svelte-kit/tsconfig.json`
- `strict: true` — todas as verificações estritas ativas
- `moduleResolution: "bundler"` — resolução de módulos moderna
- `rewriteRelativeImportExtensions: true` — importações sem extensão `.ts`
- `esModuleInterop: true` — compatibilidade com módulos CommonJS

## Regras de Importação

```typescript
// ✅ Correto — sem extensão de arquivo
import { base } from '$app/paths';
import type { PageData } from './$types';

// ✅ Correto — alias $lib mapeia para src/lib/
import { something } from '$lib/index';

// ❌ Errado — extensões .ts no import
import { something } from '$lib/index.ts';
```

## Tipos e Interfaces

```typescript
// ✅ Prefira interface para objetos públicos, type para unions/utilitários
interface ServiceItem {
  icon: string;
  title: string;
  description: string;
}

type ServiceCategory = 'web' | 'ai' | 'media' | 'automation';

// ✅ Sempre tipar props e retornos de funções
function buildServiceUrl(category: ServiceCategory, id: string): string {
  return `/services/${category}/${id}`;
}

// ❌ Errado — any explícito (evite)
function process(data: any): any { ... }
```

## Arquivos de Declaração

- `src/app.d.ts` — declarações globais do app SvelteKit
- Mantenha tipos específicos de domínio próximos ao código que os usa
- Use `declare global` apenas em `app.d.ts`

## Convenções Gerais

1. **SEMPRE** use `strict` types — sem `any` desnecessário
2. **SEMPRE** defina tipos de retorno em funções exportadas
3. **NUNCA** adicione `// @ts-ignore` ou `// @ts-nocheck` sem justificativa explícita
4. **SEMPRE** use `import type` para imports apenas de tipo
5. **SEMPRE** use optional chaining (`?.`) e nullish coalescing (`??`) para valores potencialmente nulos
