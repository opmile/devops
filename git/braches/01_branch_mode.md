# Para que serve criar uma *branch*?

A branch (ramo) é uma linha de desenvolvimento separada do código principal (`main` ou `master`).

Você cria uma branch quando vai implementar algo novo, corrigir um bug, testar algo, etc.

A ideia é:

* trabalhar isoladamente

* sem quebrar a aplicação que já está funcionando

* e depois mesclar suas mudanças de volta para a branch principal.

---

## Fluxo de Trabalho (Local + GitHub)

1. Estar com a branch principal atualizada

2. Criar uma branch nova para sua feature

3. Desenvolver e fazer commits nela

4. Subir essa branch para o GitHub

4. Abrir um Pull Request (PR) - ou merge manual

5. Mesclar de volta na branch principal

---

## Passo a passo guiado

### 1) Conferir estado atual

```bash
git status
```

**O que faz:** mostra em qual branch você está, quais arquivos foram modificados, quais estão staged (prontos para commit) e se há commits pendentes para enviar ao remoto.
**Por que usar:** evita surpresas — você não quer trocar de branch com mudanças não commitadas que poderiam atrapalhar.

---

## 2) Ver branches locais e remotas

```bash
git branch
git branch -r
git branch -a
```

**O que faz:**

* `git branch` lista as branches locais e marca a atual com `*`.
* `git branch -r` lista branches remotas (ex.: `origin/main`).
* `git branch -a` mostra tudo (local + remoto).
  **Por que usar:** confirmar nome da branch base e checar se a branch principal remota existe com outro nome.

---

## 3) Ir para a branch principal e atualizá-la

```bash
git checkout main
# ou (comandos modernos)
git switch main

git pull
# ou para evitar merges automáticos:
git pull --rebase
```

**O que faz:**

* `git checkout main` *muda o HEAD* do repositório para a branch `main` (altera o conteúdo do seu diretório de trabalho para a versão daquela branch). Se houver mudanças não commitadas, o Git pode impedir a troca — para evitar perda acidental.
  *Obs.:* desde versões mais recentes do Git existe `git switch` (mais claro semanticamente): `git switch main` faz o mesmo.
* `git pull` busca (`fetch`) as alterações do repositório remoto e tenta integrá-las na branch local (`merge` por padrão).
* `git pull --rebase` busca e reaplica seus commits locais sobre a ponta mais recente da branch remota (histórico mais linear).
  **Por que usar:** sempre crie sua branch nova a partir de uma `main` atualizada — evita conflitos e trabalho extra depois.

---

## 4) Criar e mudar para a branch da feature

```bash
git checkout -b feat/nome-da-feature
# equivalente moderno:
git switch -c feat/nome-da-feature
```

**O que faz:** cria uma nova branch chamada `feat/nome-da-feature` baseada na branch atual (geralmente `main`) e já muda o HEAD para ela.
**Por que usar:** isola seu trabalho em uma linha separada; o `-b` evita dois comandos (criar + trocar). Nomeie de forma descritiva (tipo `feat/`, `fix/`, `chore/`).

---

## 5) Trabalhar, adicionar e commitar mudanças

```bash
git add caminho/arquivo    # adiciona arquivos específicos ao stage
git add .                  # adiciona todas as mudanças (cuidado)
git commit -m "mensagem clara e curta"
git commit -m "feat: validação do formulário (nome, email)"
```

**O que faz:**

* `git add` coloca mudanças no *staging area* (preparando para commit).
* `git commit` cria um novo commit com o conteúdo do *staging area* e a mensagem que descreve a mudança.
  **Boas práticas:** commits pequenos, atômicos e com mensagem clara ajudam revisão e histórico.

---

## 6) Empurrar (push) a branch para o GitHub

```bash
git push -u origin feat/nome-da-feature
# depois, só:
git push
```

**O que faz:**

* `git push origin feat/nome-da-feature` cria a branch remota `origin/feat/nome-da-feature` e envia seus commits.
* O flag `-u` (ou `--set-upstream`) associa sua branch local à remota; depois disso `git push` e `git pull` funcionam sem especificar `origin nome`.
  **Por que usar:** permite abrir PR no GitHub e compartilhar o trabalho com o time.

---

## 7) Abrir Pull Request (PR) no GitHub

No site do GitHub: vá ao repositório → selecione sua branch → **Compare & pull request** → descreva a mudança → criar PR.
**O que fazer no PR:** escreva objetivo, riscos, testes e screenshots se preciso. Se for só você no projeto, pode revisar e fazer merge direto.

---

## 8) Atualizar sua branch com mudanças recentes da main (opcional antes do merge)

Duas opções principais:

### a) Merge (mais simples)

```bash
# estando na sua branch:
git fetch origin
git merge origin/main
```

**O que faz:** incorpora todos os commits de `origin/main` criando um commit de merge se houver mudanças divergentes. Mantém histórico fiel, mas pode criar commits extras.

### b) Rebase (histórico linear)

```bash
git fetch origin
git rebase origin/main
```

**O que faz:** reaplica seus commits no topo da `origin/main` mais recente — produz histórico linear, mas reescreve commits (cuidado se branch já foi publicada e outros trabalham nela).
**Quando usar cada:** rebase para branches pessoais antes de PR; merge se preferir evitar reescrita do histórico ou em branches compartilhadas.

---

## 9) Resolver conflitos (quando aparecerem)

Ao `merge` ou `rebase`, se houver conflito:

1. O Git marca arquivos em conflito.
2. Edite arquivos, escolha as mudanças (remova os marcadores `<<<<<<<` `=======` `>>>>>>>`).
3. `git add arquivo_resolvido`
4. Se for merge: `git commit` (às vezes o Git já criou o commit de merge depois do add).
   Se for rebase: `git rebase --continue` até terminar.
   **Dica:** `git status` mostra arquivos em conflito e próximos passos.

---

## 10) Finalizar: merge da feature para main (via GitHub ou local)

### Pelo GitHub

* Faça o merge no PR (squash, merge commit ou rebase + merge — escolha conforme política do projeto).

### Localmente (se preferir)

```bash
git checkout main
git pull
git merge feat/nome-da-feature
git push
```

**O que faz:** incorpora sua branch na `main` local e envia ao remoto.

---

## 11) Apagar branch depois do merge

```bash
git branch -d feat/nome-da-feature          # apaga local (só se foi mergeada)
git push origin --delete feat/nome-da-feature  # apaga no remoto
```

**O que faz:** limpa branches antigas; `-d` recusa apagar se branch não foi mergeada (use `-D` para forçar, mas cuidado).

---

## Comandos auxiliares e úteis

* `git fetch` — baixa commits do remoto, sem integrar. Use para checar o que mudou sem alterar seu trabalho.
* `git log --oneline --graph --all` — ver histórico em forma de grafo compacto.
* `git show <commit>` — ver conteúdo e mensagem de um commit específico.
* `git diff` — ver diferenças não staged; `git diff --staged` mostra o que está no stage.
* `git stash` — guarda temporariamente mudanças não commitadas (útil antes de trocar de branch).

  * `git stash pop` reaplica e remove o stash.
* `git remote -v` — listar remotes configurados (ex.: `origin` aponta para seu repositório no GitHub).

---

## Observações sobre `git checkout main` (específico)

* **Trocar de branch:** `git checkout main` troca o seu diretório de trabalho para o estado da branch `main`.
* **Recusa automática:** se você tiver mudanças não commitadas que conflitam com arquivos na `main`, o Git vai impedir a troca, pedindo para commitar, stashear ou descartar as mudanças.
* **Alternativa moderna:** `git switch main` é mais claro e evita confusão com `git checkout <arquivo>` (que também existe).
* **Antes de criar uma nova feature branch:** sempre faça `git checkout main` + `git pull` (ou `git switch main && git pull`) para garantir que sua nova branch parta da versão mais recente.

---

## Exemplo de fluxo com todos os comandos juntos

```bash
# 1. Conferir e atualizar main
git status
git switch main # git checkout main
git pull --rebase

# 2. Criar branch da feature
git switch -c feat/cadastro-de-alunos

# 3. Desenvolver, adicionar e commitar
git add src/Aluno.java
git commit -m "feat: cria entidade Aluno e validações básicas"

# 4. Enviar para o GitHub
git push -u origin feat/cadastro-de-alunos

# 5. (No GitHub) abrir PR -> revisar -> merge

# 6. Atualizar main local e apagar branch
git switch main
git pull
git branch -d feat/cadastro-de-alunos
git push origin --delete feat/cadastro-de-alunos
```

---

### Com PR

```bash
git status
git checkout main
git pull origin main
git checkout -b feat/nome-da-feature
# (fazer alterações no código)
git add .
git commit -m "feat: descrição da funcionalidade"
git push -u origin feat/nome-da-feature
# (abrir PR e fazer merge no GitHub)
git checkout main
git pull origin main
```

### Sem PR 

```bash
git status
git checkout main
git pull origin main
git checkout -b feat/nome-da-feature
# (fazer alterações no código)
git add .
git commit -m "feat: descrição da funcionalidade"
git push -u origin feat/nome-da-feature
git checkout main
git pull origin main
git merge feat/nome-da-feature
git push origin main
git branch -d feat/nome-da-feature
```


