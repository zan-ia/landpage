# Pipeline Plans

Diretório para armazenar os planos de implementação gerados pelo agente `planejador`.

## Convenção de Nomenclatura
```
.github/plans/issue-{N}-{slug-da-issue}.md
```

Exemplo: `.github/plans/issue-42-fix-header-colors.md`

## Estrutura do Plano
Cada arquivo de plano deve conter:
- Resumo da abordagem
- Arquivos a modificar/criar
- Padrões a seguir
- Ordem de implementação
- Riscos identificados
- Checklist de verificação

## Template
Use `_template.md` como base para novos planos. O agente `planejador` preenche
os placeholders `{...}` automaticamente com dados da issue.

## Ciclo de Vida
1. **Criado** pelo `planejador` durante a fase de planejamento
2. **Lido** pelo `implementador` durante a implementação
3. **Verificado** pelo `revisor` durante a revisão
4. **Arquivado** (mantido no repo) após merge do PR — para rastreabilidade histórica
