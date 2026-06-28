---
description: "Use when: performing any development task — editing code, running commands, searching files, asking questions, or invoking subagents. Covers strict rules for each Copilot tool including when to use vscode_askQuestions, browser, terminal, edit, search, memory, and subagents."
applyTo:
  - "src/**"
  - ".github/**"
---

# Regras de Uso de Ferramentas — Copilot

Estas regras são **obrigatórias** para qualquer tarefa de desenvolvimento. O não cumprimento compromete a qualidade e segurança do código.

---

## 0. Harness Copilot — Visão Geral das Primitivas de Customização

O VS Code oferece **6 primitivas de customização** que formam o harness completo do Copilot. Cada agente do pipeline DEVE conhecer todas elas para tomar decisões corretas de arquitetura.

| Primitiva | Arquivo | Onde | Uso | Carregamento |
|-----------|---------|------|-----|-------------|
| **Agent Instructions** | `AGENTS.md` / `copilot-instructions.md` | Raiz ou `.github/` | Diretrizes globais para todo o projeto | Sempre-on |
| **File Instructions** | `*.instructions.md` | `.github/instructions/` | Regras específicas por padrão de arquivo | `applyTo` ou on-demand via `description` |
| **Custom Agents** | `*.agent.md` | `.github/agents/` | Personas especializadas com ferramentas restritas | Seletor de agente ou subagente |
| **Prompts** | `*.prompt.md` | `.github/prompts/` | Tarefa única e focada com parâmetros | Slash command (`/`) |
| **Skills** | `SKILL.md` | `.github/skills/<nome>/` | Workflows reutilizáveis com assets empacotados | Slash command ou detecção automática |
| **Hooks** | `*.json` | `.github/hooks/` | Automação determinística no ciclo de vida do agente | Eventos (`PreToolUse`, `PostToolUse`, etc.) |
| **MCP Servers** | Config JSON | `.vscode/` ou settings | Integração com APIs e serviços externos | Sob demanda via ferramentas MCP |

### Como o Harness Funciona

1. **Descoberta:** O VS Code escaneia os diretórios configurados (`.github/instructions/`, `.github/agents/`, `.github/skills/`, `.github/prompts/`, `.github/hooks/`) e carrega os arquivos encontrados.
2. **Seleção:** Para `*.instructions.md`, o sistema usa o campo `applyTo` (glob pattern) para anexar automaticamente quando o arquivo alvo corresponde. Se não houver `applyTo`, usa `description` para correspondência semântica.
3. **Prioridade:** Instruções de perfil do usuário > Instruções do workspace > Instruções da organização.
4. **Context Isolado:** Custom agents como subagentes (`runSubagent`) rodam em contexto isolado — o agente pai só recebe o resultado final.
5. **Fork Mode:** Skills com `context: fork` executam em um subagente dedicado, limpando o contexto da conversa principal.

### Mapa do Pipeline para o Harness

```
AGENTS.md + *.instructions.md → Diretrizes (sempre disponíveis)
       │
       ▼
  orquestrador.agent.md  → Agente coordenador (tools: all, agents: [planejador, implementador, revisor, Explore])
       │
       ├── ▶ planejador.agent.md   → Subagente read-only (tools: read, search, web)
       ├── ▶ implementador.agent.md → Subagente executor (tools: read, search, edit, execute)
       ├── ▶ revisor.agent.md      → Subagente read-only (tools: read, search)
       └── ▶ Explore (built-in)     → Subagente de exploração (tools: read, search)
       
Skills (pipeline-orquestracao, criar-section, etc.) → Workflows sob demanda
Prompts (/iniciar-bugfix, /iniciar-feature, etc.) → Tarefas parametrizadas
```

---

## 1. `vscode/askQuestions` — Perguntas ao Usuário

**🚨 REGRA CRÍTICA: NUNCA faça perguntas em texto livre para o usuário. SEMPRE use a tool `vscode/askQuestions` (carrossel interativo). Perguntas em markdown comum são PROIBIDAS — o usuário não consegue respondê-las de forma estruturada e o fluxo de trabalho é interrompido.**

O `vscode/askQuestions` é uma tool nativa do VS Code que exibe um **carrossel interativo** de perguntas com opções clicáveis, selects, e campos de texto livre. O usuário responde com cliques/seleções e as respostas são mapeadas de volta para o agente.

**Use `vscode/askQuestions` obrigatoriamente quando:**
- Houver **qualquer** ambiguidade no pedido do usuário que impacte o escopo da tarefa
- Existirem múltiplas abordagens válidas com trade-offs diferentes
- O usuário não especificar preferência de design/estilo/ferramenta
- For necessário confirmar uma decisão antes de prosseguir
- Precisar de input do usuário para continuar o fluxo de trabalho

**Regras do carrossel:**
- Máximo **4 perguntas** por interação
- Sempre ofereça opções pré-definidas (`options`) quando possível — reduz atrito
- Use `allowFreeformInput: true` para permitir respostas abertas (default)
- `header`: curto e descritivo (máx. 50 caracteres)
- `question`: concisa (máx. 200 caracteres)
- `multiSelect: true` quando o usuário pode escolher múltiplas opções
- Marque a opção recomendada com `recommended: true`

```yaml
# ✅ Exemplo correto — carrossel interativo
- header: "Escopo da Feature"
  question: "Quais são os limites desta funcionalidade?"
  options:
    - label: "Apenas frontend"
      description: "Somente componentes visuais"
      recommended: true
    - label: "Frontend + backend"
      description: "Inclui endpoints e lógica de servidor"
  allowFreeformInput: true

# ✅ Exemplo — múltipla escolha
- header: "Breakpoint Responsivo"
  question: "Quais breakpoints devo usar?"
  multiSelect: true
  options:
    - label: "Mobile (< 768px)"
    - label: "Tablet (768px - 1024px)"
    - label: "Desktop (> 1024px)"
```

**NUNCA use `vscode/askQuestions` para:**
- Senhas, tokens, API keys ou secrets (peça ao usuário para digitar diretamente no terminal)
- Perguntas triviais cuja resposta está documentada no código, em AGENTS.md, ou em docs/INSTITUCIONAL.md

**Exemplo do que NÃO fazer:**
```markdown
<!-- ❌ PROIBIDO — pergunta em texto livre -->
Você prefere que eu use flexbox ou grid para este layout?
```
O correto é usar `vscode/askQuestions` com opções clicáveis.

---

## 2. Browser — Ferramentas de Navegação

As ferramentas de browser são essenciais para verificação visual, pesquisa de documentação e validação de UI.

### Catálogo de Ferramentas

| Ferramenta | Uso Principal |
|-----------|---------------|
| `open_browser_page` | Abrir uma URL no navegador integrado |
| `fetch_webpage` | Buscar conteúdo textual de uma URL (mais leve) |
| `read_page` | Obter snapshot de acessibilidade da página atual |
| `screenshot_page` | Capturar screenshot da página ou elemento |
| `click_element` | Clicar em um elemento da página |
| `type_in_page` | Digitar texto ou pressionar teclas |
| `navigate_page` | Navegar (url, back, forward, reload) |
| `run_playwright_code` | Executar código Playwright arbitrário |
| `handle_dialog` | Responder a modais (alert, confirm, prompt) |
| `hover_element` | Pairar sobre um elemento |
| `drag_element` | Arrastar um elemento |

### Quando Usar Cada Ferramenta

**`fetch_webpage`** — documentação e pesquisa:
- Verificar documentação oficial de dependências (Svelte, SvelteKit, Vite, MD3)
- Pesquisar soluções para erros ou padrões de código
- Buscar referências de API e boas práticas
- SEMPRE valide a URL antes de buscar

**`open_browser_page` + `read_page`** — verificação visual:
- Comparar DEV local (`localhost:5173`) com LIVE (`www.zan.ia.br`)
- Verificar layout, cores, e posicionamento de elementos
- Reutilize páginas existentes (`forceNew: false`) — evite abrir novas desnecessariamente

**`screenshot_page`** — captura de evidência:
- Documentar estado visual antes/depois de mudanças
- Capturar elementos específicos (`ref` ou `selector`)
- Para comparação DEV vs LIVE, use o skill `css-comparison-workflow`

**`run_playwright_code`** — automação avançada:
- Extrair computed styles de elementos (para comparação CSS)
- Executar ações complexas que exigem lógica condicional
- Use `page.evaluate()` para ler propriedades do DOM

**`click_element`, `type_in_page`, `navigate_page`** — interação:
- Testar fluxos de usuário (navegação, scroll, formulários)
- Verificar comportamento de componentes interativos (carrossel, accordion)

### Regras Gerais
- SEMPRE valide que a URL é válida e acessível antes de abrir
- Prefira `read_page` (snapshot de acessibilidade) sobre `screenshot_page` para análise de estrutura
- Para `screenshot_page`, capture elementos específicos, não a página inteira
- Documente o que foi verificado e o resultado

### Exemplo: Verificação Visual DEV vs LIVE
```
1. open_browser_page("http://localhost:5173") → pageId: "dev"
2. open_browser_page("https://www.zan.ia.br") → pageId: "live"
3. screenshot_page("dev", element="Hero section") → hero-dev.png
4. screenshot_page("live", element="Hero section") → hero-live.png
5. Comparar visualmente → relatar diferenças
```

---

## 3. Terminal — `run_in_terminal`

**Regras de segurança:**
- Comandos devem ser **não-destrutivos** e verificados
- Sempre use `mode=sync` para comandos pontuais (build, test, lint)
- Use `mode=async` apenas para servidores de longa duração (`npm run dev`)
- NUNCA execute: `rm -rf`, `git push --force`, `npm publish`, ou comandos destrutivos sem confirmação explícita

**Regras de verificação:**
- SEMPRE leia o output completo do terminal
- Se houver erros, analise e corrija antes de prosseguir
- Execute `npm run check` após cada arquivo modificado
- Execute `npm run build` ao final para garantir build limpo
- NUNCA modifique arquivos em `build/` — toda edição deve ser em `src/`

```powershell
# ✅ Correto — comandos verificados
npm run check
npm run build
npm run dev

# ❌ Errado — comandos cegos ou destrutivos
rm -r build/
git push --force origin main
```

---

## 4. Edição de Arquivos — `replace_string_in_file`, `insert_edit_into_file`

**Regra fundamental: Leia antes de editar.**

- SEMPRE leia o arquivo com `read_file` antes de editar — contexto mínimo de 30 linhas
- Alterações devem ser **mínimas e cirúrgicas** — nunca reescreva arquivos inteiros
- Prefira `replace_string_in_file` (mais preciso); use `insert_edit_into_file` como fallback
- Para múltiplas edições no mesmo arquivo, use `replace_string_in_file` sequencialmente
- Inclua 3-5 linhas de contexto antes e depois do `oldString`
- Verifique erros com `get_errors` após cada edição
- Se `oldString` não for encontrada, releia o arquivo e tente novamente

**Fluxo de edição:**
```
1. read_file (contexto completo)
2. replace_string_in_file (edição cirúrgica)
3. get_errors (verificar se não quebrou nada)
4. Se erro → corrigir; se OK → próximo arquivo
```

---

## 5. Busca — `grep_search`, `semantic_search`, `file_search`

**Regra: Busque antes de assumir.**

- Use `grep_search` para strings exatas e expressões regulares
- Use `semantic_search` para conceitos e padrões de código
- Use `file_search` para localizar arquivos por glob pattern
- SEMPRE busque antes de afirmar que algo "não existe" no código
- Combine múltiplas estratégias de busca quando necessário

```typescript
// ✅ Correto — busca antes de criar
// 1. grep_search para ver se componente similar já existe
// 2. semantic_search para encontrar padrões de código relacionados
// 3. Só então criar o novo componente

// ❌ Errado — criar sem verificar duplicação
```

---

## 6. Memória — `memory`

Use o sistema de memória para:

| Escopo | Uso | Exemplo |
|--------|-----|---------|
| `/memories/session/` | Estado do pipeline, progresso da tarefa atual | `pipeline-state.md` |
| `/memories/repo/` | Convenções do projeto, builds verificados | `build-commands.md` |
| `/memories/` | Preferências do usuário persistentes | `preferences.md` |

**Pipeline State:** O orquestrador DEVE manter `/memories/session/pipeline-state.md` com:
- Issue atual (número, título, URL)
- Branch atual
- Fase do pipeline (planejamento | implementação | revisão)
- Iteração atual de revisão
- Próximo passo

---

## 7. Subagentes — `runSubagent` e Padrões de Orquestração

### 7.1 Conceitos Fundamentais

Subagentes são instâncias independentes do Copilot que executam trabalho focado em contexto isolado. O agente pai invoca um subagente, passa uma tarefa específica, e recebe apenas o resultado final — o contexto intermediário não polui a conversa principal.

**Quando usar:**
- Tarefas complexas que exigem contexto isolado (planejamento, revisão)
- Operações que se beneficiam de um agente especializado
- Tarefas de exploração em larga escala (use o agente `Explore`)
- Paralelismo: múltiplos subagentes rodando simultaneamente para perspectivas diferentes

**Regras:**
- SEMPRE passe contexto completo no prompt do subagente — eles são stateless
- Use `agentName` para selecionar o agente especializado correto
- Subagentes são stateless — inclua TODAS as informações necessárias no prompt
- Prefira o agente `Explore` para buscas complexas na codebase (evita poluir a conversa principal)
- Especifique o thoroughness: `quick`, `medium`, ou `thorough`

---

### 7.2 Ciclo de Vida de um Subagente

```
Agente Pai (orquestrador)
  │
  ├── 1. Identifica subtarefa que beneficia contexto isolado
  ├── 2. Invoca runSubagent(agentName, prompt)
  │       └── Subagente recebe contexto INTEIRO no prompt
  │       └── Subagente executa ferramentas (read, search, edit, ...)
  │       └── Subagente retorna resultado ÚNICO (string)
  ├── 3. Agente pai incorpora resultado e continua
  └── 4. Contexto do subagente é descartado
```

O subagente aparece no chat como uma chamada de ferramenta colapsável — você pode expandir para ver detalhes.

---

### 7.3 Controle de Invocação

O arquivo `.agent.md` de cada agente controla como ele pode ser invocado:

| Configuração | Efeito |
|-------------|--------|
| `user-invocable: false` | Agente NÃO aparece no seletor de agentes, mas ainda pode ser subagente |
| `disable-model-invocation: true` | Agente NÃO pode ser invocado como subagente por outros agentes |
| `agents: ["planejador", "implementador"]` | Lista explícita de subagentes permitidos (vazio `[]` = nenhum, `*` = todos) |
| `tools: ["read", "search"]` | Restringe as ferramentas disponíveis para o agente |

**Importante:** Listar explicitamente um agente no array `agents` sobrescreve `disable-model-invocation: true`. Isso permite criar agentes "protegidos" que só um coordenador específico pode invocar.

**Exemplo — orquestrador restrito:**
```yaml
# orquestrador.agent.md
tools: ["read", "search", "edit", "execute", "web"]
agents: ["planejador", "implementador", "revisor", "Explore"]
```

---

### 7.4 Padrão Coordinator + Worker

Este é o padrão principal do pipeline Zan.IA. Um agente coordenador (orquestrador) gerencia o fluxo de alto nível e delega subtarefas para subagentes especializados (workers). Cada worker tem ferramentas restritas ao seu papel.

```
orquestrador (coordena o fluxo)
  │
  ├── planejador (read-only: read, search, web)
  │     └── Gera plano em .github/plans/
  │
  ├── implementador (executor: read, search, edit, execute)
  │     └── Executa o plano, altera arquivos, verifica build
  │
  ├── revisor (read-only: read, search)
  │     └── Analisa diff, checklists de qualidade, classifica issues
  │
  └── Explore (built-in, read-only: read, search)
        └── Exploração rápida da codebase
```

**Regras do padrão Coordinator + Worker:**
- O coordenador NUNCA faz o trabalho dos workers — apenas gerencia o fluxo
- Cada worker tem UM papel e UM conjunto mínimo de ferramentas
- O coordenador passa contexto COMPLETO para cada worker (issue, plano, diff)
- Workers retornam apenas o resultado final (resumo estruturado)
- O coordenador sintetiza os resultados e decide o próximo passo

---

### 7.5 Padrão Multi-Perspectiva (Revisão Paralela)

Para code review, você pode rodar múltiplos subagentes em paralelo, cada um com uma perspectiva diferente:

```
Revisor Multifacetado
  ├── (paralelo) Corretude: erros lógicos, edge cases, tipos
  ├── (paralelo) Qualidade: legibilidade, naming, duplicação
  ├── (paralelo) Segurança: validação de input, riscos de injeção
  └── (paralelo) Arquitetura: padrões da codebase, consistência estrutural
  
  └── SINTETIZA: prioriza issues, destaca acertos
```

**Quando usar:** revisões complexas com múltiplas dimensões que se beneficiam de olhares independentes.

---

### 7.6 Subagentes Aninhados (Nested)

Por padrão, subagentes NÃO podem criar outros subagentes. Para habilitar:
- Setting: `chat.subagents.allowInvocationsFromSubagents = true`
- Máximo: 5 níveis de profundidade

**Quando usar:** padrão divide-and-conquer — um agente que quebra um problema grande em partes menores e delega cada parte para uma nova instância de si mesmo.

```yaml
# Exemplo de agente recursivo
---
name: ProcessadorRecursivo
tools: ["read", "search", "edit"]
agents: [ProcessadorRecursivo]
---
Se a lista tiver > 4 itens, divida ao meio e delegue cada metade para um subagente.
Se ≤ 4 itens, processe diretamente. Mescle os resultados.
```

---

### 7.7 Handoffs — Transições entre Agentes

Handoffs permitem criar fluxos de trabalho sequenciais com transições guiadas entre agentes. Após uma resposta, botões de handoff aparecem para o usuário avançar para o próximo agente com contexto pré-preenchido.

```yaml
# Exemplo no frontmatter de um agente
---
handoffs:
  - label: "Iniciar Implementação"
    agent: implementador
    prompt: "Implemente o plano descrito acima."
    send: false
  - label: "Revisar Código"
    agent: revisor
    prompt: "Revise o código implementado na etapa anterior."
    send: false
    model: "Claude Sonnet 4.6 (copilot)"
---
```

| Campo | Descrição |
|-------|-----------|
| `label` | Texto exibido no botão |
| `agent` | Agente alvo para a transição |
| `prompt` | Prompt pré-preenchido para o próximo agente |
| `send` | `true` = envia automaticamente; `false` = só preenche, aguarda usuário |
| `model` | Modelo específico para o handoff (opcional) |

**Handoffs vs Subagentes:** Handoffs são interativos (usuário decide quando avançar), subagentes são automáticos (agente decide quando delegar).

---

### 7.8 Seleção de Modelo por Subagente

É possível especificar qual modelo cada subagente usa:

1. **Parâmetro explícito:** o agente pai especifica o modelo ao invocar `runSubagent`:
   ```
   "Use Claude Sonnet 4.6 em um subagente para revisar este código."
   ```
2. **Configurado no agente:** o `.agent.md` pode definir `model` no frontmatter:
   ```yaml
   model: ["Claude Haiku 4.6 (copilot)", "Gemini 3 Flash (Preview) (copilot)"]
   ```
3. **Herança:** se não especificado, o subagente usa o mesmo modelo do agente pai.

**Regra:** O modelo do subagente não pode exceder o tier de custo do modelo principal.

---

### 7.9 Subagentes Disponíveis no Projeto

| Agente | user-invocable | agents | Ferramentas | Papel |
|--------|---------------|--------|-------------|-------|
| `orquestrador` | `true` | planejador, implementador, revisor, Explore | read, search, edit, execute, web | Coordena pipeline completo |
| `planejador` | `true` | Explore | read, search, web | Gera planos de implementação (read-only) |
| `implementador` | `true` | — | read, search, edit, execute | Executa planos, altera código |
| `revisor` | `true` | — | read, search | Analisa diffs, qualidade (read-only) |
| `refactor-css` | `false` | — | read, search, edit | Refatora CSS (só subagente) |
| `criador-conteudo` | `true` | — | read, search, edit | Gera conteúdo institucional |
| `performance-auditor` | `true` | — | read, search, edit, web | Audita performance |
| `Explore` (built-in) | `true` | — | read, search | Exploração rápida da codebase |

---

## 8. GitHub — Ferramentas de Gestão de Repositório

As ferramentas do GitHub são usadas pelo pipeline para criar issues, branches, PRs, e gerenciar o ciclo de desenvolvimento. Elas são ativadas sob demanda via `activate_*_tools`.

### Catálogo de Grupos de Ferramentas

| Grupo | Ferramenta de Ativação | Uso Principal |
|-------|----------------------|---------------|
| **Issues & Notifications** | `activate_github_issue_and_notification_tools` | Criar/issues, buscar detalhes, listar labels |
| **Pull Requests** | `activate_github_pull_request_management` | Informações do PR ativo, status checks, CI results |
| **PR Management** | `activate_pull_request_management_tools` | Criar branches, PRs, delegar ao Copilot, merge |
| **Repository Info** | `activate_repository_information_tools` | Commits, issues, releases, file contents |
| **Labels & Releases** | `activate_label_and_release_management_tools` | Gerenciar labels e releases |
| **PR Comments** | `activate_pull_request_comment_tools` | Adicionar/responder comentários em PRs e issues |
| **Code Search** | `activate_code_and_repository_search_tools` | Buscar código, commits, repositórios |
| **Team Management** | `activate_team_management_tools` | Informações de times e membros |

### Quando Usar no Pipeline

**Fase de Criação de Issue:**
```
1. activate_github_issue_and_notification_tools
2. Criar issue com template adequado (bug/feature/melhoria)
3. Adicionar labels relevantes
```

**Fase de Criação de PR:**
```
1. activate_pull_request_management_tools
2. Criar branch ou usar branch existente
3. Criar PR com Closes #N no corpo
4. activate_pull_request_comment_tools (se precisar comentar)
```

**Fase de Revisão:**
```
1. activate_github_pull_request_management
2. Verificar status checks e CI results
3. activate_pull_request_comment_tools para adicionar comentários de revisão
```

**Fase de Planejamento:**
```
1. activate_github_issue_and_notification_tools
2. Buscar detalhes completos da issue (descrição, comments)
3. activate_repository_information_tools (se precisar ver commits anteriores)
```

### Regras
- SEMPRE ative o grupo de ferramentas ANTES de usá-las
- Use `mcp_github_mcp_se_create_pull_request_with_copilot` para delegar implementação ao Copilot
- Ao criar issues, use SEMPRE o template específico do tipo (bug/feature/melhoria)
- Ao criar PRs, inclua `Closes #N` no corpo (GitHub auto-close)
- Para revisão de PR, use `activate_github_pull_request_management` para verificar CI status
- NUNCA faça merge automático — sempre aguarde aprovação do usuário (HITL)
- Após merge, verifique se a issue foi fechada automaticamente; se não, feche manualmente

### Exemplo: Fluxo Completo de Criação de Issue + PR
```
1. activate_github_issue_and_notification_tools → criar issue bug
2. HITL: usuário aprova a issue
3. Criar branch local: git checkout -b fix/descricao-curta
4. Implementar correção (via implementador subagente)
5. Revisar (via revisor subagente)
6. Commit + Push
7. activate_pull_request_management_tools → criar PR com Closes #N
8. HITL: usuário revisa e mergeia o PR
```

---

## 9. Skills — Fork Mode (`context: fork`)

### 9.1 O Que é Fork Mode?

Por padrão, quando um skill é carregado, suas instruções são adicionadas ao contexto do agente pai. Para skills grandes, ou cujo raciocínio intermediário não é relevante para o resto da conversa, é possível usar **fork mode**: o skill executa em um **subagente dedicado** e apenas o resultado final retorna ao agente pai.

```yaml
---
name: meu-skill
description: "Descrição do skill e quando usar."
context: fork   # ← Habilita fork mode
---
```

**Status:** Experimental. Requer setting `github.copilot.chat.skillTool.enabled`.

### 9.2 Quando Usar Fork Mode

| Cenário | Inline (default) | Fork |
|---------|------------------|------|
| Skill pequeno (< 100 linhas) | ✅ Ideal | ❌ Overhead desnecessário |
| Skill que lê muitos arquivos | ❌ Polui contexto | ✅ Isola contexto |
| Resultado focado (resumo, relatório) | ❌ Intermediário polui | ✅ Só resultado final |
| Skill que não deve influenciar comportamento do pai | ❌ Pode vazar contexto | ✅ Isolamento total |

**Use `context: fork` para skills que:**
- Leem muitos arquivos ou fazem investigações longas
- Produzem um resultado focado (resumo, relatório, conjunto pequeno de edições)
- Não devem influenciar o comportamento do agente pai além do resultado final

**Mantenha inline (padrão) para skills que:**
- São pequenos e rápidos
- Produzem informações contextuais que o agente pai precisa para prosseguir
- Fazem parte do raciocínio contínuo da conversa

### 9.3 Skills do Projeto e Modo Recomendado

| Skill | Tamanho | Modo | Motivo |
|-------|---------|------|--------|
| `pipeline-orquestracao` | Grande | `inline` (padrão) | O orquestrador precisa do contexto completo do pipeline |
| `criar-section` | Médio | `inline` (padrão) | Preciso para criar seções com padrões consistentes |
| `criar-pagina-institucional` | Médio | `inline` (padrão) | Similar ao acima |
| `css-comparison-workflow` | Médio | `fork` | Leitura de muitos arquivos, resultado é relatório |
| `otimizar-imagens` | Pequeno | `inline` (padrão) | Rápido, resultado influencia diretamente o código |

### 9.4 Carregamento Progressivo de Skills

O harness carrega skills em 3 níveis para manter o contexto eficiente:

1. **Descoberta** (~100 tokens): O agente lê `name` e `description` do YAML frontmatter
2. **Instruções** (<5000 tokens): Carrega o corpo do `SKILL.md` quando relevante
3. **Recursos**: Arquivos adicionais (scripts, templates) sob demanda quando referenciados

**Em fork mode:** Os passos 2 e 3 rodam no subagente dedicado — o pai só vê o resultado final.

### 9.5 Slash Commands: Skills vs Prompts

Tanto skills quanto prompts aparecem como slash commands (`/`) no chat:

| Configuração | Aparece em `/` | Auto-carregamento |
|-------------|---------------|-------------------|
| Padrão (ambos omitidos) | ✅ Sim | ✅ Sim |
| `user-invocable: false` | ❌ Não | ✅ Sim |
| `disable-model-invocation: true` | ✅ Sim | ❌ Não |
| Ambos configurados | ❌ Não | ❌ Não |

### 9.6 Estrutura de um Skill no Projeto

```
.github/skills/<nome-do-skill>/
├── SKILL.md           # Obrigatório (nome deve corresponder ao diretório)
├── scripts/           # Código executável
├── references/        # Documentos de referência
└── assets/            # Templates, boilerplate
```

**Regras:**
- O campo `name` no SKILL.md DEVE corresponder ao nome do diretório
- Use paths relativos (`./scripts/test.js`) para referenciar recursos
- Mantenha SKILL.md < 5000 tokens; use arquivos de referência para conteúdo extenso
- O description deve usar o padrão "Use when: ..." com palavras-chave para descoberta semântica

---

## 10. `todos` — Lista de Tarefas e Execução Sequencial

A tool `todos` (built-in do VS Code) é a ferramenta de **gerenciamento de execução sequencial** dentro de uma sessão de chat. Ela exibe uma lista de tarefas visível ao usuário, com status rastreável e atualização em tempo real.

### 10.1 Por que Usar `todos`?

Sem `todos`, o agente pode:
- Executar passos fora de ordem
- Pular verificações importantes (build, lint)
- Perder o rastro do que já foi feito
- Deixar o usuário sem visibilidade do progresso

Com `todos`, o agente:
- Planeja a execução ANTES de começar — estrutura visível
- Executa UM passo por vez (só 1 `in-progress` simultâneo)
- Marca cada passo como `completed` IMEDIATAMENTE após concluir
- Dá visibilidade total ao usuário sobre o andamento

### 10.2 Quando Usar — Regra de Ouro

**Use `todos` obrigatoriamente para qualquer tarefa com 3+ passos distintos.** Se a tarefa tem um único passo, é opcional mas recomendado.

Exemplos de quando usar:
- Pipeline completo: 12 fases do orquestrador
- Implementação: editar 3+ arquivos, verificar build
- Revisão: verificar 10 dimensões de qualidade
- Planejamento: explorar codebase → identificar arquivos → escrever plano
- Criação de conteúdo: pesquisar → redigir → revisar → integrar

### 10.3 Como Usar

O agente invoca `manage_todo_list` com um array de todos, cada um com:
- `id`: número sequencial (1, 2, 3...)
- `title`: ação concisa (3-7 palavras)
- `status`: `not-started` | `in-progress` | `completed`

**Fluxo correto de uso:**

```
1. manage_todo_list(todos=[{id:1, title:"Ler arquivo X", status:"not-started"}, ...])
2. manage_todo_list(todos=[{id:1, title:"Ler arquivo X", status:"in-progress"}, ...])
3. [executa a tarefa — read_file, edit, etc.]
4. manage_todo_list(todos=[{id:1, title:"Ler arquivo X", status:"completed"}, {id:2, ...}])
5. manage_todo_list(todos=[{id:2, title:"Editar arquivo Y", status:"in-progress"}, ...])
6. [executa...]
7. ... (repete até todos completed)
```

**Regras estritas:**
- **NUNCA** comece a trabalhar sem antes criar a lista de todos
- **NUNCA** tenha mais de 1 todo `in-progress` simultaneamente
- **SEMPRE** marque como `completed` IMEDIATAMENTE após terminar o passo
- **SEMPRE** inclua TODOS os itens no array (existentes + novos) em cada chamada
- **NUNCA** remova itens da lista — apenas mude o status

### 10.4 Exemplo no Pipeline

```
# Orquestrador inicia pipeline
manage_todo_list([
  {id: 1, title: "Classificar solicitação", status: "in-progress"},
  {id: 2, title: "Criar issue no GitHub", status: "not-started"},
  {id: 3, title: "HITL: aprovação da issue", status: "not-started"},
  {id: 4, title: "Criar branch", status: "not-started"},
  {id: 5, title: "Planejamento (subagente)", status: "not-started"},
  {id: 6, title: "Implementação (subagente)", status: "not-started"},
  {id: 7, title: "Revisão (subagente)", status: "not-started"},
  {id: 8, title: "Commit + Push", status: "not-started"},
  {id: 9, title: "Criar PR", status: "not-started"},
  {id: 10, title: "HITL: revisão do PR", status: "not-started"},
  {id: 11, title: "Checkout main", status: "not-started"},
])
```

### 10.5 Integração com o Harness

A tool `todos` deve estar presente nas listas de `tools` de TODOS os agentes do pipeline:

```yaml
tools:
  - "read"
  - "search"
  - "edit"
  - "todos"            # ← Obrigatório para execução sequencial
  - "vscode/askQuestions"  # ← Obrigatório para comunicar com o usuário
```

E nos corpos dos agentes, a regra deve ser explícita:

```markdown
## Workflow Rules
- SEMPRE use a tool `todos` para criar e gerenciar a lista de tarefas
- Planeje TODOS os passos ANTES de começar a executar
- Apenas UM passo in-progress por vez
- Marque completed IMEDIATAMENTE ao terminar cada passo
```
