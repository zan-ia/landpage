# Tarefa 03 — Instruções de Uso de Ferramentas

## Objetivo
Criar/validar o arquivo de instruções que define COMO cada ferramenta do Copilot deve ser usada no contexto do projeto — com regras estritas, exemplos corretos/incorretos e fluxos de uso.

## Responsabilidades Específicas

1. **Definir regras para `vscode_askQuestions`** (ferramenta de perguntas):
   - REGRA DE OURO: Nunca assumir. Sempre perguntar.
   - Quando usar obrigatoriamente: ambiguidade, múltiplas abordagens, preferências não especificadas
   - Quando NUNCA usar: senhas, tokens, secrets, perguntas triviais documentadas
   - Máximo 4 perguntas por interação
   - Sempre oferecer `options` pré-definidas quando possível
   - Formato: header curto (máx. 50 chars), question concisa (máx. 200 chars)

2. **Definir regras para ferramentas de Browser**:
   - Catálogo completo: `open_browser_page`, `fetch_webpage`, `read_page`, `screenshot_page`, `click_element`, `type_in_page`, `navigate_page`, `run_playwright_code`, `handle_dialog`, `hover_element`, `drag_element`
   - `fetch_webpage` — para documentação e pesquisa (mais leve)
   - `open_browser_page` + `read_page` — para verificação visual DEV vs LIVE
   - `screenshot_page` — para captura de evidência (preferir elementos específicos)
   - `run_playwright_code` — para extrair computed styles e automação avançada
   - Regra: reutilizar páginas existentes (`forceNew: false`)
   - Exemplo prático: fluxo de comparação DEV vs LIVE

3. **Definir regras para Terminal (`run_in_terminal`)**:
   - `mode=sync` para comandos pontuais (build, test, lint)
   - `mode=async` apenas para servidores (`npm run dev`)
   - Comandos PROIBIDOS: `rm -rf`, `git push --force`, `npm publish`
   - Verificar output completo, analisar erros antes de prosseguir
   - `npm run check` após cada arquivo, `npm run build` ao final
   - NUNCA modificar `build/`

4. **Definir regras para Edição de Arquivos**:
   - Ler antes de editar (contexto mínimo 30 linhas)
   - Alterações mínimas e cirúrgicas
   - `multi_replace_string_in_file` para múltiplas edições
   - Incluir 3-5 linhas de contexto no `oldString`
   - Verificar `get_errors` após cada edição
   - Fluxo: read → edit → get_errors → (corrigir ou próximo)

5. **Definir regras para Busca**:
   - `grep_search` para strings exatas e regex
   - `semantic_search` para conceitos e padrões
   - `file_search` para localizar por glob pattern
   - SEMPRE buscar antes de afirmar que algo "não existe"

6. **Definir regras para Memória**:
   - `/memories/session/` — estado do pipeline, progresso da tarefa
   - `/memories/repo/` — convenções do projeto, builds verificados
   - `/memories/` — preferências do usuário persistentes

7. **Definir regras para Subagentes (`runSubagent`)**:
   - Quando usar: tarefas complexas com contexto isolado
   - Sempre passar contexto completo no prompt
   - Agentes especializados disponíveis e quando usar cada um
   - Especificar thoroughness para `Explore`: quick, medium, thorough

8. **Definir regras para GitHub Tools**:
   - Catálogo de grupos: Issues, PRs, PR Management, Repository Info, Labels, Comments, Code Search, Teams
   - Quando ativar cada grupo (`activate_*_tools`)
   - Fluxo de criação de Issue + PR
   - HITL obrigatório em pontos específicos

## Artefato de Saída
- `.github/instructions/tool-usage.instructions.md` criado/atualizado
- `applyTo: "src/**, .github/**"`

## Dependências
- **Tarefa 01** (pesquisa) — para conhecer capacidades reais das ferramentas

## Critérios de Aceitação
- [ ] Cada ferramenta tem seção dedicada com: propósito, quando usar, quando NÃO usar, exemplos
- [ ] `vscode_askQuestions`: regra de ouro + limites + formato
- [ ] Browser: catálogo completo + fluxo DEV vs LIVE
- [ ] Terminal: comandos proibidos + verificação obrigatória
- [ ] Edição: fluxo read→edit→verify
- [ ] Busca: buscar antes de assumir
- [ ] Memória: escopos e propósitos
- [ ] Subagentes: quando usar + agentes disponíveis
- [ ] GitHub: grupos de ferramentas + fluxos
