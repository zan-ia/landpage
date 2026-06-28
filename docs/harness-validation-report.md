# Harness Copilot — Relatório de Validação

**Data:** 2026-06-27  
**Tarefa:** Task-01 — Pesquisa e Validação do Harness Copilot  
**Fonte oficial:** [VS Code Agent Customization Docs](https://code.visualstudio.com/docs/agent-customization/overview) (atualizado em 24/06/2026)

---

## Sumário Executivo

| Área | Total | ✅ OK | ⚠️ Ajuste | ❌ Faltando |
|------|-------|-------|-----------|------------|
| Agents (`.agent.md`) | 7 | 4 | 3 | 0 |
| Instructions (`.instructions.md`) | 7 | 2 | 5 | 2 |
| Skills (`SKILL.md`) | 5 | 5 | 0 | 0 |
| Prompts (`.prompt.md`) | 6 | 3 | 3 | 0 |
| AGENTS.md | 1 | 1 | 0 | 0 |
| `copilot-instructions.md` | — | — | — | ❌ |

---

## 1. Documentação Oficial — Sumário

### 1.1 Custom Agents (`.agent.md`)

Localização padrão: `.github/agents/` (VS Code detecta qualquer `.md` nessa pasta).

**Campos YAML do frontmatter:**

| Campo | Obrigatório | Notas |
|-------|-------------|-------|
| `name` | Não | Default: nome do arquivo |
| `description` | Não | Texto placeholder no input do chat |
| `argument-hint` | Não | Hint mostrado ao usuário |
| `tools` | Não | Array de ferramentas disponíveis |
| `agents` | Não | Array de subagentes permitidos; `*` = todos; `[]` = nenhum |
| `model` | Não | String ou array de modelos em ordem de prioridade |
| `user-invocable` | Não | Boolean, default `true` — controla visibilidade no dropdown |
| `disable-model-invocation` | Não | Boolean, default `false` — impede invocação automática como subagente |
| `target` | Não | `vscode` ou `github-copilot` |
| `handoffs` | Não | Lista de transições para outros agentes (novo, 2026) |
| `hooks` | Não | Comandos executados em pontos do ciclo do agente (Preview) |

> **Deprecated:** `infer` — substituído por `user-invocable` + `disable-model-invocation`.

### 1.2 Instructions (`.instructions.md`)

Localização padrão: `.github/instructions/` (busca recursiva).

**Campos YAML do frontmatter:**

| Campo | Obrigatório | Notas |
|-------|-------------|-------|
| `name` | Não | Nome exibido na UI. Default: nome do arquivo |
| `description` | Não | Descrição breve para matching semântico |
| `applyTo` | Não | Glob pattern único; se ausente, não é aplicada automaticamente |

> ⚠️ **CRÍTICO:** `applyTo` aceita **um único glob pattern** (string) ou **array YAML** (`["glob1", "glob2"]`). Valores separados por vírgula em uma string (`"src/**, .github/**"`) **podem não funcionar** conforme documentado — o comportamento não é garantido.

### 1.3 Skills (`SKILL.md`)

Localização padrão: `.github/skills/<nome>/SKILL.md`.

**Campos YAML do frontmatter:**

| Campo | Obrigatório | Notas |
|-------|-------------|-------|
| `name` | **SIM** | Deve corresponder **exatamente** ao nome do diretório pai. Apenas letras minúsculas, números e hífens. Máx. 64 caracteres. Nome errado = skill não carrega (silenciosamente) |
| `description` | **SIM** | Descrição do que a skill faz e quando usar. Máx. 1024 caracteres |
| `argument-hint` | Não | Hint no input do chat |
| `user-invocable` | Não | Default `true` — controla visibilidade no menu `/` |
| `disable-model-invocation` | Não | Default `false` — impede carregamento automático pelo modelo |
| `context` | Não | `inline` (default) ou `fork` — executar em subagente isolado (experimental) |

### 1.4 Prompt Files (`.prompt.md`)

Localização padrão: `.github/prompts/`.

**Campos YAML do frontmatter:**

| Campo | Obrigatório | Notas |
|-------|-------------|-------|
| `description` | Não | Descrição curta |
| `name` | Não | Nome após `/` no chat. Default: nome do arquivo |
| `argument-hint` | Não | Hint no input |
| `agent` | Não | Agente para executar: `ask`, `agent`, `plan`, ou nome de agente customizado |
| `model` | Não | Modelo de linguagem |
| `tools` | Não | Lista de ferramentas disponíveis |

### 1.5 AGENTS.md

Arquivo de instruções sempre ativas, reconhecido por múltiplos agentes de IA. Alternativa / complemento ao `copilot-instructions.md`. Suporta múltiplos arquivos em subpastas (experimental, via `chat.useNestedAgentsMdFiles`).

---

## 2. Validação de Agents (`.agent.md`)

### 2.1 `orquestrador.agent.md` ✅

| Campo | Valor | Status |
|-------|-------|--------|
| `name` | `"orquestrador"` | ✅ |
| `description` | Descritivo e completo | ✅ |
| `tools` | `["read","search","edit","execute","web"]` | ✅ |
| `agents` | `["planejador","implementador","revisor","Explore"]` | ✅ |
| `user-invocable` | `true` | ✅ |
| `disable-model-invocation` | `false` | ✅ |
| Corpo | Fluxo completo, constraints, handoffs HITL | ✅ |

**Nenhum problema encontrado.**

---

### 2.2 `planejador.agent.md` ✅

| Campo | Valor | Status |
|-------|-------|--------|
| `name` | `"planejador"` | ✅ |
| `tools` | `["read","search","web"]` — read-only correto | ✅ |
| `agents` | `["Explore"]` | ✅ |
| `user-invocable` | `true` | ✅ |
| `disable-model-invocation` | `false` | ✅ |

**Nenhum problema encontrado.**

---

### 2.3 `implementador.agent.md` ✅

| Campo | Valor | Status |
|-------|-------|--------|
| `tools` | `["read","search","edit","execute"]` | ✅ |
| `user-invocable` | `true` | ✅ |
| `disable-model-invocation` | `false` | ✅ |

**Nenhum problema encontrado.**

---

### 2.4 `revisor.agent.md` ✅

| Campo | Valor | Status |
|-------|-------|--------|
| `tools` | `["read","search"]` — read-only correto | ✅ |
| `user-invocable` | `true` | ✅ |
| `disable-model-invocation` | `false` | ✅ |

**Nenhum problema encontrado.**

---

### 2.5 `criador-conteudo.agent.md` ⚠️

| Campo | Valor | Status |
|-------|-------|--------|
| `tools` | `["read","search","edit"]` | ✅ |
| `user-invocable` | ❌ Ausente | ⚠️ (default `true`, OK) |
| `disable-model-invocation` | ❌ Ausente | ⚠️ (default `false`, OK) |
| `agents` | ❌ Ausente | ⚠️ Deveria ser `[]` para impedir subagentes |

**Problemas:**
- Campos `user-invocable` e `disable-model-invocation` ausentes — usando defaults. Recomendado: ser explícito.
- Sem campo `agents: []` — o agente pode tentar invocar subagentes desnecessariamente.

---

### 2.6 `performance-auditor.agent.md` ✅ (corrigido)

| Campo | Valor | Status |
|-------|-------|--------|
| `tools` | `["read","search","edit","web","browser"]` | ✅ |
| `user-invocable` | `true` | ✅ |
| `disable-model-invocation` | `false` | ✅ |

**Nota:** `browser` é um tool set built-in oficial do VS Code (experimental, requer `workbench.browser.enableChatTools`). Oferece Playwright: navigate, screenshot, click, type, hover, drag. `web` oferece `fetch` (read-only). Ambos são complementares — `web` para análise de HTML/timing, `browser` para medição interativa de Core Web Vitals.

---

### 2.7 `refactor-css.agent.md` ⚠️

| Campo | Valor | Status |
|-------|-------|--------|
| `tools` | `[read, search, edit]` — **sem aspas** | ⚠️ |
| `user-invocable` | `false` | ✅ Correto (subagente apenas) |
| `disable-model-invocation` | ❌ Ausente | ⚠️ |

**Problemas:**
- `tools: [read, search, edit]` usa valores YAML sem aspas. Tecnicamente válido (strings não-quoted em YAML), mas inconsistente com os outros agentes que usam strings com aspas. Melhor: `["read", "search", "edit"]`.
- `disable-model-invocation` ausente — sem isso, o modelo pode tentar invocar este agente automaticamente, mesmo ele sendo `user-invocable: false`.

---

## 3. Validação de Instructions (`.instructions.md`)

### 3.1 `style-architecture.instructions.md` ✅

```yaml
applyTo: "src/**"
```

Glob simples, correto. Aplica a todo código fonte.

---

### 3.2 `project-organization.instructions.md` ✅

```yaml
applyTo: "src/**"
```

Glob simples, correto.

---

### 3.3 `css.instructions.md` ⚠️

```yaml
applyTo: "src/**/*.svelte, src/lib/app.css"
```

**Problema:** Valor separado por vírgula em uma string simples. O VS Code documenta `applyTo` como um único glob pattern. Essa sintaxe **pode não funcionar** para múltiplos padrões — o sistema pode interpretar literalmente como um glob com vírgula.

**Correção recomendada:** Usar array YAML:
```yaml
applyTo:
  - "src/**/*.svelte"
  - "src/lib/app.css"
```

---

### 3.4 `html.instructions.md` ⚠️

```yaml
applyTo: "src/**/*.svelte, src/app.html"
```

**Mesmo problema** da `css.instructions.md` — vírgula em string.

**Correção recomendada:**
```yaml
applyTo:
  - "src/**/*.svelte"
  - "src/app.html"
```

---

### 3.5 `deploy.instructions.md` ✅ (com observação)

```yaml
applyTo: ["build/**", ".github/workflows/**"]
```

Array YAML inline — formato mais robusto que a string com vírgula. Funciona corretamente.

**Observação:** `build/**` raramente será atingido na prática (pasta não versionada), mas é útil como guardrail para o CI.

---

### 3.6 `pipeline-workflow.instructions.md` ⚠️

```yaml
applyTo: ".github/agents/**, .github/prompts/**, .github/skills/**"
```

**Problema:** Três padrões separados por vírgula em uma string.

**Correção recomendada:**
```yaml
applyTo:
  - ".github/agents/**"
  - ".github/prompts/**"
  - ".github/skills/**"
```

---

### 3.7 `tool-usage.instructions.md` ⚠️

```yaml
applyTo: "src/**, .github/**"
```

**Problema:** Dois padrões separados por vírgula em uma string.

**Correção recomendada:**
```yaml
applyTo:
  - "src/**"
  - ".github/**"
```

---

### 3.8 Instructions faltando ❌

| Arquivo Sugerido | `applyTo` | Conteúdo |
|-----------------|-----------|----------|
| `svelte.instructions.md` | `"src/**/*.svelte"` | Svelte 5 Runes: `$state`, `$effect`, `$props`, `$derived`, semântica correta, evitar `export let` e `onMount` deprecated |
| `typescript.instructions.md` | `"src/**/*.ts"` | Padrões TypeScript do projeto: tipos, interfaces, `lang="ts"` em Svelte |

---

## 4. Validação de Skills (`SKILL.md`)

> **Regra crítica:** O campo `name` DEVE corresponder exatamente ao nome do diretório pai. Nome incorreto causa falha silenciosa no carregamento.

| Diretório | `name` no SKILL.md | Match? | Status |
|-----------|-------------------|--------|--------|
| `criar-section/` | `criar-section` | ✅ | ✅ |
| `criar-pagina-institucional/` | `criar-pagina-institucional` | ✅ | ✅ |
| `pipeline-orquestracao/` | `pipeline-orquestracao` | ✅ | ✅ |
| `otimizar-imagens/` | `otimizar-imagens` | ✅ | ✅ |
| `css-comparison-workflow/` | `css-comparison-workflow` | ✅ | ✅ |

**Todos os 5 skills passam na regra crítica de nome.**

### Campos por skill:

| Skill | `description` ✅ | `argument-hint` | `user-invocable` | `disable-model-invocation` |
|-------|-----------------|----------------|-----------------|---------------------------|
| `criar-section` | ✅ | ❌ Ausente | ❌ Ausente (default `true`) | ❌ Ausente (default `false`) |
| `criar-pagina-institucional` | ✅ | ✅ | ❌ Ausente | ❌ Ausente |
| `pipeline-orquestracao` | ✅ | ✅ | ✅ `true` | ✅ `false` |
| `otimizar-imagens` | ✅ | ❌ Ausente | ❌ Ausente | ❌ Ausente |
| `css-comparison-workflow` | ✅ | ✅ | ❌ Ausente | ❌ Ausente |

**Observação:** Campos opcionais ausentes usam defaults corretos (`user-invocable: true`, `disable-model-invocation: false`). Sem problemas funcionais.

**Melhoria sugerida:** `pipeline-orquestracao` é um skill grande e complexo. Usar `context: fork` (experimental) manteria o contexto da conversa principal mais limpo, executando a skill em subagente isolado.

---

## 5. Validação de Prompts (`.prompt.md`)

| Prompt | `description` | `argument-hint` | `agent` | Status |
|--------|---------------|----------------|---------|--------|
| `iniciar-bugfix` | ✅ | ✅ | ✅ `orquestrador` | ✅ |
| `iniciar-feature` | ✅ | ✅ | ✅ `orquestrador` | ✅ |
| `iniciar-melhoria` | ✅ | ✅ | ✅ `orquestrador` | ✅ |
| `adicionar-depoimento` | ✅ | ✅ | ❌ Ausente | ⚠️ |
| `adicionar-servico` | ✅ | ✅ | ❌ Ausente | ⚠️ |
| `otimizar-seo` | ✅ | ✅ | ❌ Ausente | ⚠️ |

**Problemas nos 3 prompts sem `agent`:**

- `adicionar-depoimento` e `adicionar-servico` — tarefas de edição de conteúdo. Deveriam apontar para `agent: "criador-conteudo"` para consistência e qualidade de resposta.
- `otimizar-seo` — auditoria técnica que envolve múltiplos arquivos. Poderia se beneficiar do agente correto (`agent: "agent"` mínimo, ou `orquestrador` se precisar do ciclo completo).

Sem `agent` definido, o prompt usa o agente corrente selecionado na UI — comportamento imprevisível.

---

## 6. Validação do AGENTS.md ✅

O arquivo `AGENTS.md` está bem estruturado e cobre:

| Área | Status |
|------|--------|
| Visão geral do projeto | ✅ |
| Tech stack (tabela) | ✅ |
| Arquitetura | ✅ |
| Build & Deploy commands | ✅ |
| Estrutura de arquivos | ✅ |
| Convenções de código | ✅ |
| GitHub Workflow (issues/PRs) | ✅ |
| Tabela de agentes com papéis | ✅ |
| Fluxo do pipeline | ✅ |
| Regras de uso de ferramentas | ✅ |
| Referências para outros arquivos | ✅ |

**Nenhum problema crítico. Está funcionando como always-on instruction.**

---

## 7. Item Faltando — `copilot-instructions.md` ❌

O projeto usa `AGENTS.md` como arquivo de instruções always-on, o que é válido e suportado. Porém, `.github/copilot-instructions.md` é o arquivo primário para Copilot in VS Code e GitHub.com.

**Recomendação:** Criar `.github/copilot-instructions.md` com um resumo conciso das convenções principais (stack, scoped CSS, design tokens, sem Tailwind). O `AGENTS.md` pode continuar como documentação mais completa para outros agentes de IA.

Alternativamente: redirecionar do `copilot-instructions.md` para o `AGENTS.md`:
```markdown
<!-- .github/copilot-instructions.md -->
See [AGENTS.md](../../AGENTS.md) for full project guidelines.
```

---

## 8. Funcionalidades Novas Não Utilizadas

Estas features foram adicionadas ao VS Code em 2025-2026 e podem melhorar o harness:

### 8.1 `handoffs` nos Agentes (Junho 2026)

Permite criar transições guiadas entre agentes com botões de ação após cada resposta. Ideal para o pipeline Plan→Implement→Review:

```yaml
# planejador.agent.md
handoffs:
  - label: "🚀 Iniciar Implementação"
    agent: implementador
    prompt: "Implemente o plano gerado acima."
    send: false
```

**Benefício:** O usuário não precisa digitar o próximo comando — um botão aparece automaticamente.

### 8.2 `context: fork` em Skills

Para skills complexas como `pipeline-orquestracao`, usar `context: fork` executa a skill em subagente isolado, retornando apenas o resultado final para a conversa principal. Mantém o contexto limpo.

```yaml
# pipeline-orquestracao/SKILL.md
context: fork
```

### 8.3 Hook Files (Preview)

Hooks executam comandos automaticamente em pontos do ciclo do agente (ex: após cada edição de arquivo). Útil para automatizar `npm run check`:

```yaml
# .github/agents/implementador.agent.md
hooks:
  - event: onFileEdit
    command: npm run check
```

### 8.4 Instrução de Tools via `#tool:<name>` no Corpo

Os arquivos `.agent.md` e `.instructions.md` podem referenciar tools diretamente no corpo usando `#tool:<tool-name>`. Pode melhorar a clareza das instruções de uso de ferramentas.

---

## 9. Recomendações Priorizadas

### Prioridade Alta (afeta funcionamento)

| # | Issue | Arquivo(s) | Ação |
|---|-------|-----------|------|
| 1 | `applyTo` com vírgulas em string — pode não funcionar | `css.instructions.md`, `html.instructions.md`, `pipeline-workflow.instructions.md`, `tool-usage.instructions.md` | Converter para array YAML |
| 2 | `performance-auditor` usava `"browser"` — tool é válido mas experimental; complementar com `"web"` | `performance-auditor.agent.md` | Adicionado `"web"` + `"browser"` juntos ✅ |

### Prioridade Média (melhoria de qualidade)

| # | Issue | Arquivo(s) | Ação |
|---|-------|-----------|------|
| 3 | Prompts sem `agent` definido | `adicionar-depoimento`, `adicionar-servico`, `otimizar-seo` | Adicionar `agent:` correspondente |
| 4 | `criador-conteudo` sem `agents: []` e campos de visibilidade | `criador-conteudo.agent.md` | Adicionar campos explícitos |
| 5 | `refactor-css` tools sem aspas e sem `disable-model-invocation` | `refactor-css.agent.md` | Padronizar YAML e adicionar campo |
| 6 | `criar-section` sem `argument-hint` | `criar-section/SKILL.md` | Adicionar hint descritivo |

### Prioridade Baixa (gaps e melhorias)

| # | Issue | Ação |
|---|-------|------|
| 7 | Ausência de `copilot-instructions.md` | Criar `.github/copilot-instructions.md` |
| 8 | Faltam `svelte.instructions.md` e `typescript.instructions.md` | Criar as duas instructions files |
| 9 | `handoffs` não utilizados nos agentes de pipeline | Adicionar nos agents `planejador` e `implementador` |
| 10 | `context: fork` para `pipeline-orquestracao` | Avaliar se melhora a experiência |

---

## 10. Checklist de Aceitação

- [x] Documentação oficial consultada e sumarizada (vs code docs, junho 2026)
- [x] Todos os 7 arquivos `.agent.md` validados
- [x] Todos os 7 arquivos `.instructions.md` validados (incluindo `applyTo`)
- [x] Todos os 5 arquivos `SKILL.md` validados (incluindo regra nome=diretório)
- [x] Todos os 6 arquivos `.prompt.md` validados
- [x] `AGENTS.md` validado contra documentação oficial
- [x] Relatório de validação criado com achados claros

---

*Relatório gerado por GitHub Copilot — Task 01 do harness de qualidade Zan.IA*
