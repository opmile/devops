# Entendendo `git push`

O comando `git push` é usado para enviar as alterações confirmadas (commits) do seu repositório local para um repositório remoto, como o GitHub, GitLab ou Bitbucket.

O `-u` do `git push` parece só um detalhe, mas na real ele é **fundamental pra configurar o “vínculo” entre sua branch local e a remota**, e muda totalmente o fluxo de trabalho a partir dali.

---

### **1. O que é o `-u` no `git push`**

O comando completo é:

```bash
git push -u origin nome-da-branch
```

O `-u` é um **atalho para `--set-upstream`**, e sua função é **dizer ao Git que sua branch local deve rastrear (track)** uma branch remota específica.

Ou seja:

> “Git, a partir de agora, a minha branch local `feat/login` está conectada à `origin/feat/login`.”

---

### **2. O que isso muda na prática**

Antes de usar `-u`:

* Você precisa digitar sempre o nome da branch e do remoto:

  ```bash
  git push origin feat/login
  git pull origin feat/login
  ```

Depois de usar `-u` (uma única vez):

* O Git **sabe qual branch remota está associada à sua local**, então basta:

  ```bash
  git push
  git pull
  ```

  Ele já entende:

  > “Ah, estou na branch `feat/login`, então devo fazer push/pull para `origin/feat/login`.”

Isso é o que chamamos de **tracking branch** (ou *upstream branch*).

---

### **3. Exemplo prático**

1. Cria uma branch local:

   ```bash
   git checkout -b feat/login
   ```

2. Faz commits:

   ```bash
   git add .
   git commit -m "feat: cria tela de login"
   ```

3. Envia a branch para o remoto com vínculo:

   ```bash
   git push -u origin feat/login
   ```

Agora, o Git salva essa relação internamente (você pode ver com `git branch -vv`):

```
* feat/login   abc1234 [origin/feat/login] feat: cria tela de login
```

Daqui em diante, basta usar `git push` e `git pull` — o Git já sabe a “rota” exata de envio e recebimento.

---

### **4. Sem o `-u`, o que acontece?**

Se você fizer:

```bash
git push origin feat/login
```

sem o `-u`, o push até funciona, mas **o Git não grava o vínculo**.
Na próxima vez que tentar dar `git pull`, ele vai reclamar:

```
fatal: The current branch feat/login has no upstream branch.
```

E vai sugerir algo como:

```
git push --set-upstream origin feat/login
```

---

### **5. Em resumo**

| Situação                        | Comportamento                   | Consequência                              |
| ------------------------------- | ------------------------------- | ----------------------------------------- |
| `git push origin feat/login`    | Envia código                    | Não vincula branches                      |
| `git push -u origin feat/login` | Envia código **e cria vínculo** | Depois basta usar `git push` / `git pull` |

---

### **6. Em nível profissional**

Em times e pipelines de CI/CD, **usar `-u` é obrigatório na primeira vez que você faz push de uma nova branch**, porque:

* Mantém o repositório remoto organizado;
* Garante que automações (builds, testes) saibam qual branch acompanhar;
* Evita erros manuais no futuro.

---

## Usar `-u` é uma configuração permanente

**Essa configuração continua valendo mesmo que você mude de branch e volte dias depois**.

Aqui vai o motivo:

Quando você usa o `-u` (ou `--set-upstream`), o Git grava **de forma permanente** no seu repositório local a relação entre a branch local e a remota.
Isso fica salvo no arquivo `.git/config`, algo como:

```ini
[branch "feat/login"]
    remote = origin
    merge = refs/heads/feat/login
```

Essa configuração diz literalmente:

> “Quando eu estiver na branch `feat/login`, use `origin/feat/login` como referência para `git push` e `git pull`.”

Ou seja:

* Você pode sair da branch (`git checkout main`),
* Trabalhar em outras coisas,
* Voltar depois (`git checkout feat/login`),
  e o Git ainda vai lembrar qual é a “upstream branch” associada.

Então, quando você der:

```bash
git push
git pull
```

o Git sabe exatamente para onde enviar ou de onde buscar — **não precisa repetir o `-u` nunca mais**, a menos que:

* você apague e recrie a branch local,
* ou mude o nome da branch remota,
* ou clone o repositório de novo (porque aí o `.git/config` é refeito).

---

> O `-u` é um *setup permanente* da relação local–remota.
> Depois de configurado uma vez, o Git lembra para sempre (até você apagar ou alterar essa branch).