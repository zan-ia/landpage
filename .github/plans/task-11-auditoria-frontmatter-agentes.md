# Tarefa 11 — Auditoria de Consistência de Frontmatter dos Agentes

## Objetivo
Verificar e corrigir inconsistências no frontmatter YAML dos 7 agentes (`.github/agents/*.agent.md`), garantindo que tools, permissões, e handoffs estejam alinhados com o papel de cada agente.

## Diagnóstico Atual

| Agente | Tools | User-Invocable | Disable-Model | Handoffs |
|--------|-------|:---:|:---:|:---:|
| `orquestrador` | read, search, edit, execute, web, todos, vscode/askQuestions | ✅ | ❌ | → planejador, implementador |
| `planejador` | read, search, web, todos, vscode/askQuestions | ✅ | ❌ | → implementador |
| `implementador` | read, search, edit, execute, todos, vscode/askQuestions | ✅ | ❌ | → revisor |
| `revisor` | read, search, todos, vscode/askQuestions | ✅ | ❌ | → orquestrador |
| `criador-conteudo` | read, search, **edit**, todos, vscode/askQuestions | ✅ | ❌ | — |
| `performance-auditor` | read, search, **edit**, web, **browser**, todos, vscode/askQuestions | ✅ | ❌ | — |
| `refactor-css` | read, search, edit, todos, vscode/askQuestions | ❌ | ❌ | — |

## Responsabilidades Específicas

### 1. Verificar ferramentas por papel

Cada agente deve seguir o **princípio do menor privilégio**:

| Agente | Ferramentas Esperadas | Observação |
|--------|----------------------|------------|
| `orquestrador` | ✅ read, search, edit, execute, web, todos, vscode/askQuestions | Coordenador — acesso amplo justificado |
| `planejador` | ✅ read, search, web, todos, vscode/askQuestions | Read-only + web para consultar docs/GitHub |
| `implementador` | ✅ read, search, edit, execute, todos, vscode/askQuestions | Precisa editar e executar build |
| `revisor` | ✅ read, search, todos, vscode/askQuestions | Read-only — não edita, não executa |
| `criador-conteudo` | ⚠️ tem `edit` | **Questionável** — gera conteúdo, mas deveria editar arquivos? Se sim, manter. Se é gerador de texto para copy, remover `edit`. |
| `performance-auditor` | ⚠️ tem `edit` e `browser` | `browser` é justificado (Lighthouse, Core Web Vitals). `edit` é questionável — auditor deveria só reportar, não corrigir. |
| `refactor-css` | ✅ read, search, edit, todos, vscode/askQuestions | Refatorador — editar é justificado. `user-invocable: false` está correto (subagente apenas). |

### 2. Ações de Correção

#### 2a. `criador-conteudo.agent.md`
- **Decidir:** o agente edita arquivos ou só gera texto?
- Se **gera texto** (copy para seções): remover `edit`, manter `read`, `search`
- Se **edita componentes** (ex: atualiza texto em Testimonials.svelte): manter `edit`
- **Recomendação:** remover `edit` — o papel é gerar conteúdo institucional, não editar componentes. A edição fica com o `implementador`.

#### 2b. `performance-auditor.agent.md`
- **Decidir:** o agente corrige problemas ou só reporta?
- Se **só reporta**: remover `edit`
- Se **corrige**: manter `edit`
- **Recomendação:** remover `edit` — auditoria é diagnóstica. Correções devem passar pelo pipeline (implementador).

#### 2c. `refactor-css.agent.md`
- ✅ Já está correto — editar é seu papel, `user-invocable: false` é adequado.

### 3. Verificar `disable-model-invocation`

Nenhum agente tem `disable-model-invocation: true`. Isso é **intencional** — todos podem ser invocados como subagentes. Mas o `orquestrador` e `planejador` restringem quais subagentes podem usar via campo `agents`.

**Verificar:** o `orquestrador.agent.md` lista explicitamente seus subagentes permitidos no campo `agents`? Isso é uma proteção importante.

### 4. Padronizar descrições

- `refactor-css` tem description em **inglês** ("Use when: refactoring...") — todos os outros estão em português.
- **Correção:** traduzir para português para consistência.

## Artefatos de Saída
- `.github/agents/criador-conteudo.agent.md` — possível remoção de `edit`
- `.github/agents/performance-auditor.agent.md` — possível remoção de `edit`
- `.github/agents/refactor-css.agent.md` — tradução da description
- `.github/agents/orquestrador.agent.md` — verificação do campo `agents`

## Dependências
- **Task 02** (definição original dos agentes)

## Critérios de Aceitação
- [x] `criador-conteudo`: decisão documentada sobre `edit` (manter ou remover) → **REMOVIDO** (gera conteúdo, não edita componentes)
- [x] `performance-auditor`: decisão documentada sobre `edit` (manter ou remover) → **REMOVIDO** (audita/reporta, correções via pipeline)
- [x] `refactor-css`: description traduzida para português
- [x] `orquestrador`: campo `agents` verificado e com lista explícita de subagentes → ✅ `planejador`, `implementador`, `revisor`, `Explore`
- [x] Princípio do menor privilégio respeitado em todos os 7 agentes
- [x] `npm run check` passa (verificação de sintaxe YAML não quebrou) → ✅ 0 errors, 0 warnings

---

## Resolução (2026-06-27)

### Alterações Realizadas

| # | Arquivo | Mudança | Motivo |
|---|---------|---------|--------|
| 1 | `criador-conteudo.agent.md` | Removido `edit` das tools | Agente gera conteúdo textual; edição de componentes cabe ao `implementador` |
| 2 | `performance-auditor.agent.md` | Removido `edit` das tools | Auditoria é diagnóstica; correções devem passar pelo pipeline |
| 3 | `refactor-css.agent.md` | Description traduzida para português | Consistência com os outros 6 agentes (todos em português) |
| 4 | `orquestrador.agent.md` | Removido `disable-model-invocation` duplicado | Estava declarado 2x no frontmatter (linha 7 e linha 31) |

### Estado Final dos 7 Agentes

| Agente | Tools | User-Invocable | Disable-Model | Handoffs |
|--------|-------|:---:|:---:|:---:|
| `orquestrador` | read, search, edit, execute, web, todo, vscode/askQuestions | ✅ | ❌ | → planejador, implementador |
| `planejador` | read, search, web, todo, vscode/askQuestions | ✅ | ❌ | → implementador |
| `implementador` | read, search, edit, execute, todo, vscode/askQuestions | ✅ | ❌ | → revisor |
| `revisor` | read, search, todo, vscode/askQuestions | ✅ | ❌ | → orquestrador |
| `criador-conteudo` | read, search, todo, vscode/askQuestions | ✅ | ❌ | — |
| `performance-auditor` | read, search, web, browser, todo, vscode/askQuestions | ✅ | ❌ | — |
| `refactor-css` | read, search, edit, todo, vscode/askQuestions | ❌ | ❌ | — |

### Verificação
- `npm run check`: **0 errors, 0 warnings** ✅
- Princípio do menor privilégio: **respeitado** — apenas `orquestrador` e `implementador` têm `edit`+`execute`; `planejador` e `revisor` são read-only; `performance-auditor` mantém `browser`+`web` justificados

### Correção Adicional (2026-06-27): `"todos"` → `"todo"`
- Substituição do nome da ferramenta `"todos"` → `"todo"` em **10 arquivos**:
  - 7 agentes: `.github/agents/*.agent.md` (tools list)
  - `.github/instructions/tool-usage.instructions.md` (9 referências textuais)
  - `.github/copilot-instructions.md` (1 referência)
  - `AGENTS.md` (1 referência)
- Palavra portuguesa "todos" (= "all") mantida onde aplicável
