# `git pull`

Nos casos de conflito, `fatal: Need to specify how to reconcile divergent branches.`:

```
hint: your next pull: 
hint: 
hint: git config pull.rebase false # merge 
hint: git config pull.rebase true # rebase 
hint: git config pull.ff only # fast-forward only 
hint: 
hint: You can replace "git config" with "git config --global" to set a default 
hint: preference for all repositories. You can also pass --rebase, --no-rebase, 
hint: or --ff-only on the command line to override the configured default per 
hint: invocation. 
```

Relaxa, isso aí é só o Git sendo dramático. Vamos direto ao ponto, do jeito mais claro possível.

### O QUE O GIT TÁ RECLAMANDO

Ele percebeu que **a sua branch local (`fix/ci-config`) e a `main` do remoto divergiram** — ou seja, cada uma tem commits que a outra não tem.
Quando você dá `git pull`, ele precisa decidir **como misturar essas histórias**.

Como você não configurou um modo padrão, ele pede pra você escolher.

### OS TRÊS MODOS (bem simples)

#### 1. **Merge**

`git pull --no-rebase`
Ou configure:

```
git config pull.rebase false
```

Isso cria um commit extra de merge unindo as duas histórias.
É o mais seguro e o mais comum pra quem tá começando.

#### 2. **Rebase**

`git pull --rebase`
Ou configure:

```
git config pull.rebase true
```

Reorganiza sua branch para parecer que seus commits vieram “depois” dos commits da `main`.
História mais limpa, mas mais fácil de dar confusão se você não estiver acostumada.

#### 3. **Fast-forward only**

`git pull --ff-only`
Ou configure:

```
git config pull.ff only
```

Só funciona se sua branch puder avançar sem criar merge nem rebase.
Como você já tem commits diferentes, **não vai funcionar**.

---

### O QUE VOCÊ DEVE FAZER AGORA

Você quer só trazer a `main` pra sua branch. Então faça o mais direto:

**1. Se você quer merge (mais simples):**

```
git pull origin main --no-rebase
```

**2. Se preferir rebase (história mais “bonita”):**

```
git pull origin main --rebase
```

Se não sabe qual escolher, vai sem medo de **merge**:

```
git pull origin main --no-rebase
```

## O Conceito de `rebase`

Beleza, vamos destravar isso de uma vez — sem enrolar.

Pensa assim: **merge** e **rebase** são duas formas de trazer as mudanças da `main` pra sua branch. A diferença é *como* isso aparece na história do projeto.

### 1. O *merge*:

É o amigão que junta tudo como está.
Ele não mexe nos seus commits, só cria um commit novo dizendo:
“juntei a main com a fix/ci-config”.

A linha do tempo fica com aquele formato meio trançado, tipo dois rios se encontrando.

### 2. O *rebase*:

Esse aqui é o caprichoso.
Ele fala:
“ok, sua branch começou no commit X, mas a main avançou… vamos fingir que você começou o seu trabalho *depois* da versão atual da main”.

Traduzindo em vida real:

* Você começou a escrever um capítulo de um livro.
* Enquanto isso, o autor principal revisou e mudou o capítulo anterior.
* O rebase pega o seu capítulo e diz: “coloque isso por cima da versão atualizada do livro, como se você tivesse escrito depois dessa revisão”.

**Ele reescreve a história**, reposicionando seus commits como se fossem mais recentes.

### Visualmente:

**Antes:**

```
main: A ---- B ---- C
               \
fix:             D ---- E
```

**Depois do rebase:**

```
main: A ---- B ---- C
                         \
fix (rebase):              D' ---- E'
```

Os commits D e E são “copiados” e recriados (D’, E’) em cima do C.

### Por que isso existe?

Pra deixar o histórico **linear**, limpo, bonito.
Pra evitar aqueles merges gigantes cheios de caminho cruzado.

### Por que muita gente apanha com isso?

Porque **rebase muda commits já existentes**.
Se sua branch já foi empurrada pro GitHub e outras pessoas já baixaram, mexer nela com rebase cria confusão — seus commits mudam de ID e vira uma festa.

### Como usar mentalmente:

* Se é só você na branch → rebase é safe e mantém tudo organizado.
* Se a branch já está compartilhada → merge é o seguro-de-vida.

