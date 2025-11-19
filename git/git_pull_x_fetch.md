# Diferenciando `git fetch` de `git pull`

---

### **1. `git fetch` — Atualiza referências, mas não altera seu código**

**O que faz:**

* Baixa as *novidades* do repositório remoto (commits, branches, tags, etc).
* **Não mistura nada** no seu código local.
* Só atualiza o que o Git chama de *referências remotas*, como `origin/main`.

> “Baixe o que tem de novo no GitHub, mas não mexa em nada do meu trabalho local.”

**Exemplo:**

```bash
git fetch origin
```

**Depois disso:**

* Você pode ver o que mudou com:

  ```bash
  git log main..origin/main
  ```
* E decidir se quer atualizar sua branch com:

  ```bash
  git merge origin/main
  ```

  ou

  ```bash
  git rebase origin/main
  ```

**Uso ideal:**

* Quando quer **verificar atualizações** antes de aplicá-las.
* Quando está **no meio de uma feature** e não quer arriscar conflitos ainda.
* Em **ambientes de produção** (por exemplo, CI/CD) para inspecionar mudanças com segurança.

---

### **2. `git pull` — Faz fetch + merge automaticamente**

**O que faz:**

* Primeiro faz o mesmo `fetch` (baixa as novidades).
* Depois, **integra** automaticamente essas mudanças à sua branch atual, via *merge* (ou *rebase*, dependendo da config).

**Em outras palavras:**

> “Baixe e já atualize meu código local com o que mudou no remoto.”

**Exemplo:**

```bash
git pull origin main
```

ou simplesmente:

```bash
git pull
```

(se sua branch já tem um `-u` configurado)

**Uso ideal:**

* Quando você quer **atualizar sua branch rapidamente** com o que está no repositório remoto.
* Quando **confia** que não há conflitos grandes ou modificações paralelas.

---

### **3. Diferença visual**

| Ação                                        | `git fetch`          | `git pull`         |
| ------------------------------------------- | -------------------- | ------------------ |
| Baixa dados do remoto                       | ✅                    | ✅                  |
| Atualiza referências locais (`origin/main`) | ✅                    | ✅                  |
| Atualiza sua branch local                   | ❌                    | ✅                  |
| Pode causar conflitos                       | ❌                    | ✅                  |
| Uso típico                                  | Inspeção e segurança | Atualização direta |

---

### **4. Dica prática (nível profissional)**

* Use `git fetch` **quando estiver incerto** sobre o que mudou — é o modo “seguro”.
* Use `git pull` **quando souber que quer mesmo sincronizar** sua branch.

Por exemplo, um fluxo seguro de trabalho:

```bash
git fetch origin
git diff main..origin/main   # veja o que mudou
git merge origin/main        # aplique se quiser
```

---

## Comportamento para Branches

O comportamento muda um pouquinho entre `fetch` e `pull` dependendo de como você executa o comando.

---

### **1. Quando você está em uma branch com upstream configurado (ex: main ↔ origin/main)**

Se você rodar:

```bash
git fetch
```

ou

```bash
git pull
```

sem especificar nada (sem `origin` nem nome de branch), o Git entende:

> “Ok, estou na branch `main`, que está rastreando (`tracking`) `origin/main`, então vou buscar ou atualizar essa referência.”

Ou seja, **ele baixa (fetch) as atualizações da branch remota correspondente à branch atual.**

---

### **2. Mas o `git fetch` é mais amplo por padrão**

O comando:

```bash
git fetch
```

sem argumentos **baixa atualizações de *todas* as branches remotas**, não apenas da atual.
Ou seja, ele traz:

* novos commits de `origin/main`,
* de `origin/feat/login`,
* de `origin/fix/bug-123`, etc.

Mas **não altera nenhuma branch local** — apenas atualiza as referências “fantasmas” (`origin/...`) dentro do seu repositório local.

Você pode conferir com:

```bash
git branch -r
```

e ver que todas estão atualizadas.

Se quiser baixar só a branch atual (pra economizar tempo e rede), dá pra especificar:

```bash
git fetch origin main
```

ou

```bash
git fetch origin feat/login
```

---

### **3. Já o `git pull` atua só na branch atual**

O `git pull`, por outro lado, **só busca e aplica mudanças da branch remota que está vinculada à branch local atual.**
Por exemplo:

* Se você estiver em `feat/login`, ele busca `origin/feat/login`.
* Se estiver em `main`, ele busca `origin/main`.

E em seguida faz o merge (ou rebase, se configurado) automático com sua branch local.

---

### **4. Em resumo**

| Situação                           | `git fetch`         | `git pull`               |
| ---------------------------------- | ------------------- | ------------------------ |
| Baixa atualizações da branch atual | ✅ (se especificada) | ✅                        |
| Baixa todas as referências remotas | ✅ (por padrão)      | ❌                        |
| Atualiza branch local              | ❌                   | ✅                        |
| Seguro contra conflitos            | ✅                   | ❌ (pode gerar conflitos) |

---

Em ambos os casos, o Git baixa as referências *pelo menos* da branch em que você está, **mas o `fetch` tende a baixar de todas as branches remotas**, enquanto o `pull` atua apenas na que você está trabalhando e faz a integração automática.

Se você quiser trabalhar de forma mais controlada (especialmente em times ou projetos maiores), o uso de `git fetch` seguido de um merge manual é o caminho mais seguro.


