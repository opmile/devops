# Merge Conflicts

Beleza, Milena — vou te explicar isso de um jeito bem direto, prático e realista, porque **merge conflict** é aquele tipo de coisa que todo dev encontra e precisa aprender a lidar sem drama.

---

### 1. **O que é um merge conflict, na prática**

Um *merge conflict* acontece quando **duas pessoas (ou você mesma em dois momentos diferentes)** alteram **a mesma parte do arquivo**, e o Git não sabe qual versão deve manter.

Não é erro, não é falha.
É só o Git dizendo:

> “Ei, você mexeu aqui. Alguém (ou você) também mexeu no mesmo lugar. Decide aí qual fica.”

Exemplo clássico: você alterou uma função na sua branch e outra pessoa (ou outra branch sua) alterou a mesma função na `main`.

O Git tenta juntar tudo automaticamente, mas quando não consegue, ele te entrega o arquivo marcado para você resolver na mão.

---

### 2. **Como o Git mostra o conflito**

No arquivo aparecem essas marcações:

```txt
<<<<<<< HEAD
versão do código da branch onde você está
=======
versão do código da branch que você está tentando mesclar
>>>>>>> main
```

A partir disso, você escolhe:

* manter só uma versão
* misturar as duas
* reescrever tudo manualmente

Depois remove essas marcações e salva.

---

### 3. **Por que conflitos acontecem?**

Normalmente por:

1. **Alterações no mesmo bloco de código**
   Mesma linha, mesma função, mesma variável.

2. **Branch muito antiga**
   Quanto mais tempo você demora pra sincronizar com a main, maior a chance de conflito.

3. **Áreas do código que mudam muito rápido**
   Funções que todo mundo usa, configurações, modelos de dados, etc.

---

### 4. **Como resolver merge conflicts — passo a passo**

Vamos ao fluxo mais comum:

#### Você tenta um merge:

```bash
git checkout minha-branch
git merge main
```

E Git te dá uma mensagem tipo:

```
CONFLICT (content): Merge conflict in src/App.java
Automatic merge failed; fix conflicts and then commit the result.
```

#### Verificar quais arquivos têm conflito:

```bash
git status
```

Eles vão aparecer como **“both modified”**.

#### Abrir o arquivo e resolver o conflito manualmente

Retira `<<<<<<<`, `=======` e `>>>>>>>`, decide o que fica.

Essa é a parte artesanal, mas as IDEs (IntelliJ, VSCode) ajudam muito com botões tipo:

* Accept Current
* Accept Incoming
* Accept Both

#### Adicionar o arquivo resolvido:

```bash
git add src/App.java
```

#### Finalizar o merge:

```bash
git commit
```

Não precisa escrever mensagem — o Git cria uma pra você.

E pronto: conflito resolvido, fluxo volta ao normal.

---

### 5. **Depois disso, qual o fluxo normal?**

Você segue normalmente:

* continua seus commits,
* ou faz o merge da sua branch para a main,
* ou faz push da sua branch.

**E sim, você pode dar push depois de resolver conflitos. Não tem problema.**

---

### 6. **Dicas profissionais para evitar conflitos chatos**

**1) Sempre mantenha sua branch atualizada:**

```bash
git checkout minha-branch
git pull origin main
```

Se fizer isso regularmente, a chance de conflitos diminui MUITO.

---

**2) Faça commits pequenos e frequentes**
Quanto menos coisa junta num commit enorme, mais fácil de resolver.

---

**3) Evite alterar o mesmo arquivo gigantesco que todo mundo altera**
(Tá, nem sempre dá pra evitar… mas ajuda pensar nisso.)

---

**4) Resolva conflitos *localmente* antes de abrir PR**
Time nenhum gosta de PR cheio de conflito.

---

* Merge conflict = Git não sabe qual alteração manter.
* Aparece quando duas alterações afetam o mesmo trecho do código.
* Você resolve manualmente, remove marcações e faz commit.
* Fluxo volta ao normal logo depois.
* Melhor prevenção: manter a branch sincronizada com a main.

---
