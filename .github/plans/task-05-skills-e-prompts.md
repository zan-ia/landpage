# Tarefa 05 — Skills e Prompts (Commands)

## Objetivo
Criar/validar skills (conhecimento especializado) e prompts (comandos customizados do usuário) que habilitam o pipeline de desenvolvimento com capacidades específicas e pontos de entrada para o usuário.

## Responsabilidades Específicas

### Parte A: Skills (Conhecimento Especializado)

1. **`pipeline-orquestracao/SKILL.md`** — Skill de orquestração:
   - Contém o conhecimento completo do pipeline
   - Carregado quando usuário menciona: pipeline, bugfix, feature, melhoria, corrigir, implementar
   - Define o fluxo Plan→Implement→Review com HITL
   - Invoca automaticamente os agentes corretos

2. **`criar-pagina-institucional/SKILL.md`** — Criação de páginas:
   - Padrão visual da landing page (glass panels, MD3, tipografia)
   - Quando usar: novas landing pages, páginas de serviço, páginas institucionais
   - Como estruturar o componente Svelte + scoped CSS

3. **`criar-section/SKILL.md`** — Criação de seções:
   - Padrão para novas seções na landing page
   - Template base com glass panels, gradientes, tipografia consistente
   - Gera .svelte completo com scoped CSS

4. **`css-comparison-workflow/SKILL.md`** — Comparação DEV vs LIVE:
   - Fluxo para verificar equivalência visual
   - Usar browser tools para comparar localhost:5173 com o site de produção
   - Extrair computed styles, capturar screenshots, relatar diferenças

5. **`otimizar-imagens/SKILL.md`** — Otimização de imagens:
   - Converter para WebP/AVIF
   - Gerar srcset com múltiplas resoluções
   - Aplicar lazy loading
   - Sugerir compressão

6. **Avaliar necessidade de novos skills**:
   - `svelte5-migration` — migração para Svelte 5 Runes?
   - `seo-otimization` — otimização de SEO?
   - `testing-setup` — configuração de testes?

### Parte B: Prompts (Comandos do Usuário)

1. **`iniciar-bugfix.prompt.md`** — Gatilho para correção de bugs:
   - Usuário reporta bug → orquestrador inicia pipeline
   - Classifica como `fix:`, cria issue bug, branch `fix/`

2. **`iniciar-feature.prompt.md`** — Gatilho para novas features:
   - Usuário solicita funcionalidade → orquestrador inicia pipeline
   - Classifica como `feat:`, cria issue feature, branch `feat/`

3. **`iniciar-melhoria.prompt.md`** — Gatilho para melhorias:
   - Usuário solicita melhoria → orquestrador inicia pipeline
   - Classifica como `improve:`, cria issue melhoria, branch `improve/`

4. **`adicionar-depoimento.prompt.md`** — Adicionar testimonial:
   - Fluxo específico para adicionar depoimento ao componente Testimonials

5. **`adicionar-servico.prompt.md`** — Adicionar serviço:
   - Fluxo específico para adicionar serviço ao componente Solutions

6. **`otimizar-seo.prompt.md`** — Otimização de SEO:
   - Meta tags, headings, semantic HTML, performance

7. **Avaliar necessidade de novos prompts**:
   - `/criar-pagina` — atalho para criar página institucional?
   - `/revisar` — revisão manual de código?

## Artefato de Saída
- 5+ skills em `.github/skills/*/SKILL.md` criados/atualizados
- 6+ prompts em `.github/prompts/*.prompt.md` criados/atualizados

## Dependências
- **Tarefa 01** (pesquisa) — formato correto de SKILL.md e .prompt.md
- **Tarefa 02** (agentes) — agents que serão invocados pelos comandos

## Critérios de Aceitação
- [ ] Skills com descrição clara de quando são ativados
- [ ] Skills com conteúdo técnico preciso e acionável
- [ ] Prompts acionam o fluxo correto do pipeline
- [ ] Cada prompt especifica o agente alvo (orquestrador ou específico)
- [ ] Cobertura completa: bug, feature, melhoria, conteúdo, SEO
- [ ] Skills e prompts não duplicam responsabilidades entre si
