# Destrinchando Branch Local e Remota

---

### **1. Branch local**

É a *branch que só existe na sua máquina*.
Quando você cria uma nova branch com:

```bash
git checkout -b feat/login
```

essa branch existe **apenas localmente**.
O Git não a envia automaticamente para o repositório remoto (GitHub, por exemplo).

Pra ela aparecer lá, você precisa fazer:

```bash
git push -u origin feat/login
```

Isso cria uma **branch remota** com o mesmo nome e vincula as duas.

---

### **2. Branch remota**

São as branches que estão **no repositório remoto** (GitHub, GitLab, etc).
Elas aparecem quando você roda:

```bash
git branch -r
```

O `-r` vem de *remote*.
Essas branches representam o que existe **no servidor**, não necessariamente o que você tem atualizado localmente.

Exemplo de saída:

```
origin/main
origin/feat/login
origin/bugfix/cadastro
```

---

### **3. Diferença entre os comandos**

* `git branch` → mostra **apenas as branches locais**
* `git branch -r` → mostra **apenas as branches remotas**
* `git branch -a` → mostra **todas** (locais + remotas)

---

| Tipo de branch | Onde vive                     | Criada por           | Exemplo de comando  |
| -------------- | ----------------------------- | -------------------- | ------------------- |
| Local          | Na sua máquina                | `git checkout -b`    | `feat/login`        |
| Remota         | No GitHub (ou outro servidor) | `git push -u origin` | `origin/feat/login` |

---

## Timing para `push` da nova branch

---

### **1) Fazer o `git push -u origin feat/login` logo após criar a branch**

Fluxo:

```bash
git checkout -b feat/login
git push -u origin feat/login
```

**O que acontece:**

* Cria a branch remota imediatamente.
* Liga (track) sua branch local à remota.
* **Não envia código**, só a referência da branch (se ainda não houver commits novos).

**Vantagens:**

* Se você trabalha em equipe, outros devs já podem ver que a branch existe e até contribuir.
* Facilita integração contínua (CI/CD), que pode exigir que a branch esteja remota pra rodar testes.
* Evita esquecer o `-u` depois.

**Desvantagem (mínima):**

* Você “sujou” o repositório remoto com uma branch ainda vazia, que pode ser descartada se não fizer nada nela.

---

### **2) Fazer o `git push -u origin feat/login` após o primeiro commit**

Fluxo:

```bash
git checkout -b feat/login
# (faz alterações)
git add .
git commit -m "feat: cria tela de login"
git push -u origin feat/login
```

**O que acontece:**

* Cria a branch remota **já com código**, e vincula à local.
* No GitHub, a branch aparece com commits prontos e já permite abrir PR imediatamente.

**Vantagens:**

* A branch remota já tem conteúdo real.
* Evita deixar branches “vazias” no servidor.
* É o **fluxo mais comum e limpo em projetos profissionais individuais**.

**Desvantagens:**

* Se você esquecer o `-u`, precisa configurá-lo depois.
* Em times grandes, outros devs não verão sua branch até o primeiro push (mas isso não é problema em projetos solo).

---

**(profissionalmente falando):**

> O melhor momento é **após o primeiro commit**, quando a feature já tem alguma base funcional.

Assim:

* mantém o repositório remoto mais limpo,
* evita branches fantasmas,
* e só publica algo que de fato existe.

---

Independente do momento em que você fizer o `git push -u origin nome-da-branch` — **seja logo após criar a branch, seja depois do primeiro commit** — o efeito é o mesmo:

> o Git registra o vínculo permanente entre a branch local e a remota.

Depois disso:

* basta `git push` pra enviar novas alterações,
* e `git pull` pra atualizar a branch com o que estiver no remoto.

Você só precisaria usar o `-u` novamente se:

* recriasse a branch do zero,
* renomeasse a branch local ou remota,
* ou fizesse um novo clone do repositório (porque o `.git/config` seria novo).

Então, sim — **um único `-u` por branch é suficiente.**


