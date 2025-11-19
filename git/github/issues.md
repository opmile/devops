# Issues

Issues são **tarefas rastreáveis** dentro do repositório.

Elas servem para registrar:

* novas funcionalidades (features)
* bugs
* melhorias técnicas
* discussões
* tarefas gerais (infra, documentação, testes, etc.)

Pensa nelas como **cartões de Kanban**, mas integrados diretamente ao GitHub, ao código e aos PRs.

---

Issues são necessárias porque ajudam em várias frentes:

**1) Organização**

Cada issue representa “uma unidade de trabalho”.
Times grandes (e até solos profissionais) precisam saber:

* o que precisa ser feito
* quem está fazendo o quê
* qual o status da tarefa
* quando ela foi concluída
* por que aquela mudança existiu

Sem issues, tudo vira caos.

---

**2) Rastreabilidade**

Isso aqui é crucial:

* Commit → aponta pra issue
* Branch → nomeada para a issue
* PR → fecha a issue
* Tag/Release → também referencia issue

Fica tudo documentado.
Se alguém pergunta: “Por que isso foi implementado?”, você acha a issue, lê a discussão e entende o contexto.

---

**3) Comunicação**

Issue é o lugar onde você:

* discute a solução
* coloca prints
* cola logs de erro
* adiciona links
* marca pessoas
* descreve a abordagem técnica

Ela vira um “mini fórum” sobre aquele problema específico.

---

**4) Trigger para automações**

Pipelines podem ser configurados para:

* fechar issues automaticamente
* atualizar status no board
* gerar documentação
* registrar métricas

Ou seja, issues fazem parte do ciclo **Dev → Review → Merge → Release → Deploy**.

---

**Padrão de título mais comum**

```
[TIPO] Descrição curta da tarefa
```

**Exemplos reais:**

```
[FEAT] Criar endpoint de login
[BUG] Erro ao processar pagamento quando usuário está deslogado
[DOC] Atualizar README com instruções de build
[CHORE] Melhorar logs do serviço de autenticação
[REFACTOR] Padronizar nomes das classes DTO
```

---

**Tipos mais usados (convenções)**

| Sigla    | Tipo           | Para quê serve                          |
| -------- | -------------- | --------------------------------------- |
| FEAT     | Feature        | Nova funcionalidade                     |
| BUG      | Bugfix         | Erro identificado                       |
| DOC      | Documentation  | Alterações em docs                      |
| TEST     | Test           | Melhorias de testes                     |
| REFACTOR | Refatoração    | Melhorar código sem mudar comportamento |
| CHORE    | Tarefas gerais | Infra, configs, CI/CD, build, etc.      |

Esses nomes não são do GitHub — são convenções de times.

---

**Numeração das issues é automática**

Você não precisa colocar número no nome.
O GitHub cria: `#1`, `#2`, `#3`, …

Quando um commit referencia a issue:

```bash
git commit -m "feat: adiciona login (#12)"
```

O GitHub vincula tudo automaticamente.

---

### 4. **Padrão de descrição (corpo da issue)**

Quase todos os times usam um modelo tipo:

#### **1) Para features**

```
### Descrição
Explicar a funcionalidade

### Objetivo
O que se espera da entrega

### Critérios de aceitação
- [ ] Deve permitir...
- [ ] Deve retornar...
- [ ] Deve validar...

### Notas técnicas
(Banco, API, endpoints, dependências)
```

---

#### **2) Para bugs**

```
### Descrição do bug
O que está acontecendo?

### Passos para reproduzir
1. ...
2. ...
3. ...

### Comportamento esperado
O que deveria acontecer?

### Evidências
(prints, logs, stacktrace)
```

---

### 5. **Branches e Issues trabalham juntas**

Fluxo profissional típico:

1. Criar issue no GitHub
2. Criar branch com o número da issue:

```
git checkout -b feat/12-login
```

3. Trabalhar no código
4. Commit vinculado:

```
git commit -m "feat: implementa login (#12)"
```

5. Abrir PR que fecha a issue:

```
Fixes #12
```

6. Merge → Issue fecha automaticamente
7. Release vincula todas issues fechadas naquela versão

Tudo fica redondo e documentado.

---

* Issues = tarefas rastreáveis dentro do GitHub.
* Servem para organizar, documentar, discutir e automatizar trabalho.
* Times usam padrões de tipo: FEAT, BUG, DOC, TEST, CHORE, REFACTOR.
* Titles seguem o padrão `[TIPO] Descrição`.
* Branches e commits costumam referenciar a issue pelo número.
* PRs fecham issues automaticamente.
* Issues fazem parte do fluxo profissional de desenvolvimento moderno.

---

## **Relação entre Issues e PRs**

**Issues = o que precisa ser feito.**
**Pull Requests = a entrega da solução daquela Issue.**

* A **issue abre a necessidade** (“criar login”, “corrigir bug X”, “documentar Y”).
* O **PR apresenta o código que resolve essa necessidade**.
* O GitHub fecha a issue automaticamente quando o PR diz “Fixes #numero”.

No fluxo profissional, **todo PR nasce de uma issue**.

---

## **Visão geral do fluxo profissional completo**

Aqui está o passo a passo real que qualquer time backend usa:

1. **Criar Issue** (representa a tarefa).
2. **Criar Branch vinculada à Issue**.
3. **Fazer commits pequenos, claros e vinculados à issue**.
4. **Abrir PR dizendo que aquela branch resolve a issue**.
5. **Code Review + ajustes (se houver)**.
6. **Merge na main**.
7. **Issue fechada automaticamente** (ou manualmente).
8. **Branch deletada**.

Esse é o fluxo moderno, limpo e rastreável.

---

### **1) Criação da Issue**

Você cria algo assim no GitHub:

```
[FEAT] Criar endpoint de login
```

A issue ganha um número automático: **#12**

E descreve:

* objetivo,
* critérios de aceitação,
* notas técnicas.

Isso vira o **guia da sua implementação**.

---

### **2) Criar a branch baseada na issue**

Esse padrão é o que se usa na indústria:

```bash
git checkout -b feat/12-login
```

Formato:

```
tipo/numero-da-issue-descricao-curta
```

E pronto: o Git já sabe que essa branch está trabalhando na issue #12.

---

### **3) Commits claros, pequenos e rastreáveis**

Padrão moderno:

```
feat: implementa autenticação básica (#12)
```

Você sempre coloca o `(#12)` para vincular o commit à issue.

Isso cria rastreabilidade:

* na linha do tempo,
* no PR,
* na release,
* nos logs do sistema,
* no histórico do repositório.

---

### **4) Abrir o Pull Request**

Você abre um PR da sua branch para a main:

```
feat/12-login → main
```

E no corpo você coloca:

```
Fixes #12
```

Funciona da mesma forma com as seguintes palavra-chave:

* `Fixes #10`

* `Closes #10`

* `Resolves #10`

* Mesma coisa usando `Fix`, `Close`, `Resolve`, no singular.

Isso diz ao GitHub:

* “Quando esse PR for aceito e mergeado, feche automaticamente a issue #12”.

O PR é o pacote final da sua entrega, contendo:

* commits,
* descrição,
* discussão,
* revisão,
* check do pipeline,
* arquivo modificado.

---

### **5) Code Review e ajustes**

Mesmo em projetos solo, você pode tratar isso como uma rotina profissional:

* revisar diffs,
* rodar testes,
* ajustar nomes de variáveis,
* melhorar comentários.

Em equipes, isso é obrigatório.

---

### **6) Merge**

Depois de aprovado, você dá merge:

* Squash (mais limpo, 1 commit final)
* Merge normal (mantém todos commits)
* Rebase & Merge (histórico linear)

O mais comum hoje: **Squash and Merge**.

---

### **7) Issue é fechada automaticamente**

Se você escreveu `Fixes #12`, o GitHub faz a automação:

* PR mergeado
* Issue fechada
* Histórico vinculado

Tudo perfeito.

---

### **8) Branch é deletada**

Depois do merge:

```
git branch -d feat/12-login          # local
git push origin --delete feat/12-login  # remoto
```

Isso mantém o repositório organizado.

---

```
1. Criar Issue no GitHub
2. git checkout -b feat/<numero-issue>-descricao
3. Commits pequenos:
   feat: adiciona endpoint X (#numero)
   fix: ajusta validação (#numero)
4. Abrir PR:
   - título claro
   - descrição
   - "Fixes #numero"
5. Revisar e ajustar
6. Merge na main
7. Issue fecha automaticamente
8. Deletar branch
```

---
