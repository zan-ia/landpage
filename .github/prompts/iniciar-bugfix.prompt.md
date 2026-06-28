---
description: "Inicia o pipeline de correção de bug na landing page Zan.IA. Cria issue, branch fix/, planeja, implementa, revisa e abre PR."
argument-hint: "Descreva o bug encontrado (ex: 'O header fica com cores erradas no mobile...')"
agent: "orquestrador"
---

# Iniciar Pipeline de Bugfix

Inicie o pipeline completo de correção de bug seguindo o fluxo definido em `.github/instructions/pipeline-workflow.instructions.md`.

## Procedimento

### 1. Entender o Bug

Se a descrição do usuário estiver incompleta ou ambígua, use `vscode_askQuestions` **obrigatoriamente** para esclarecer:

```
- header: "Passos para Reproduzir"
  question: "Quais passos exatos levam ao bug?"
- header: "Comportamento Esperado"
  question: "O que deveria acontecer em vez do bug?"
- header: "Ambiente"
  question: "Em qual navegador/dispositivo o bug ocorre?"
```

### 2. Criar Issue de Bug

Crie uma issue no GitHub `zan-ia/landpage` com:

**Título:** `fix: [descrição curta do bug]`

**Corpo:**
```markdown
### Descrição do Bug
[Descrição clara e concisa]

### Passos para Reproduzir
1. 
2. 
3. 

### Comportamento Esperado
[O que deveria acontecer]

### Comportamento Atual
[O que está acontecendo de errado]

### Ambiente
- SO: Windows 11
- Navegador: Chrome/Edge/Firefox
- Dispositivo: Desktop/Mobile
- Branch: main
```

### 3. HITL — Aprovação da Issue

🛑 **PARE e aguarde.** Apresente a issue ao usuário e aguarde aprovação explícita antes de prosseguir.

### 4. Seguir o Pipeline

Após aprovação, execute o pipeline completo:

1. Criar branch `fix/descricao-curta` a partir de `main`
2. Invocar `planejador` (subagente) para analisar e planejar
3. Invocar `implementador` (subagente) para corrigir o bug
4. Invocar `revisor` (subagente) para validar a correção
5. Se critical/major → re-planejar (máx. 3x)
6. Commit com `fix:` + push
7. Criar PR com `Closes #N`
8. HITL — aguardar revisão do PR

---

## Template de Commit (Bugfix)
```
fix(Header): corrigir cores do glass-panel no mobile

O backdrop-filter não estava sendo aplicado corretamente em
dispositivos móveis devido à ausência do prefixo -webkit-.

Corrigido adicionando o prefixo e verificando compatibilidade
cross-browser.

Closes #42
```

---

## Referências
- Pipeline completo: `.github/instructions/pipeline-workflow.instructions.md`
- Uso de ferramentas: `.github/instructions/tool-usage.instructions.md`
- Convenções de código: `AGENTS.md`
