# Tarefa 06 — Configuração do AGENTS.md

## Objetivo
Criar/validar o arquivo `AGENTS.md` raiz do projeto — o ponto de entrada principal que o Copilot lê para entender o contexto do workspace, stack, convenções e fluxo de trabalho.

## Responsabilidades Específicas

1. **Definir Project Overview**:
   - Nome e propósito do projeto (landing page institucional)
   - Tech stack completa (SvelteKit 5 + Vite + adapter-static)
   - Tipo de site: static generation, GitHub Pages

2. **Documentar Tech Stack**:
   - Framework: SvelteKit 5 (Runes mode)
   - Build: Vite + `@sveltejs/adapter-static`
   - Markup: Componentes Svelte com scoped CSS
   - Estilos: Scoped `<style>` + `app.css` global (design tokens)
   - Ícones: Google Material Symbols Outlined
   - Tipografia: Space Grotesk, Geist, JetBrains Mono (Google Fonts)
   - Tema: Dark mode (Material Design 3)
   - Deploy: GitHub Pages + GitHub Actions

3. **Documentar Arquitetura**:
   - SvelteKit 5 com Runes mode
   - Static site generation via adapter-static
   - Scoped CSS nativo
   - Único CSS global em `src/lib/app.css`
   - PROIBIDO: Tailwind CSS

4. **Documentar Estrutura de Arquivos**:
   - Diagrama da árvore de diretórios
   - Propósito de cada diretório e arquivo principal
   - Onde criar novos componentes, rotas, assets

5. **Documentar Convenções de Código**:
   - Componentes Svelte com Runes mode
   - Scoped CSS + design tokens
   - Animações e ícones
   - Imagens e responsividade
   - Glass panels

6. **Documentar Pipeline e Workflow**:
   - Tabela dos 7 agentes com papel e ferramentas
   - Fluxo completo: Plan → Implement → Review
   - Como iniciar: `/iniciar-bugfix`, `/iniciar-feature`, `/iniciar-melhoria`
   - Referência ao skill `pipeline-orquestracao`

7. **Documentar Regras de Uso de Ferramentas**:
   - Tabela resumo das ferramentas e regras principais
   - Referência ao arquivo `.github/instructions/tool-usage.instructions.md`

8. **Referenciar documentação complementar**:
   - `docs/INSTITUCIONAL.md` — informações da empresa
   - `.github/instructions/*` — instruções específicas
   - `.github/skills/*` — skills especializados

## Artefato de Saída
- `AGENTS.md` criado/atualizado na raiz do projeto

## Dependências
- **Todas as tarefas anteriores** — AGENTS.md sumariza e referencia tudo

## Critérios de Aceitação
- [ ] Project overview claro e conciso
- [ ] Tech stack completa e precisa
- [ ] Arquitetura explicada (SvelteKit, static, scoped CSS, sem Tailwind)
- [ ] Estrutura de diretórios documentada
- [ ] Convenções de código listadas
- [ ] Pipeline com agentes, fluxo e HITL documentados
- [ ] Referências para docs complementares
- [ ] Formatação Markdown limpa e legível
- [ ] Não excede 300 linhas (deve ser escaneável rapidamente)
