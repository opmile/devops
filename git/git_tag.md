# `git tag`

---

Tag é basicamente um *marcador permanente* na história do repositório.

No mundo real, ela é usada quase sempre para:

**1) Marcar versões de releases (v1.0.0, v1.2.3, etc.)**
Essa é a função mais clássica.
Toda vez que seu software “gera uma versão” que vai pra produção, você marca aquela snapshot do código.

**2) Marcar pontos importantes da história**
Por exemplo:

* “última versão estável antes da migração X”
* “commit do MVP entregue ao cliente”
* “build de demonstração”

Mas hoje em dia isso é bem menos comum — no geral, vira release no GitHub.

---

### ➤ **Automação de CI/CD depende fortemente de tags**

Pipelines normalmente fazem:

* gerar release automatizado
* criar pacote para deploy
* criar artefatos como JARs, ZIPs, imagens Docker
* disparar deploy para ambiente de produção/staging
* versionar semanticamente (semver)

Muitas dessas etapas só disparam **quando uma tag é criada**.

### ➤ **Deploys e rollback ficam 100x mais fáceis**

Se algo deu errado em produção:

* “Volta pra tag v2.1.0”
  Pronto.
  Código imutável, fácil de rastrear.

### ➤ **É essencial pra versionamento semântico (semver)**

Se seu projeto segue `1.2.3` (MAJOR.MINOR.PATCH):
Cada release vira uma tag.

Sem tag, esquece coerência.

### ➤ **Gerar releases no GitHub/GitLab depende de tags**

Quando você marca uma tag, o GitHub cria a página do release.
É assim que libs e libs Java, JS, Python, etc., são distribuídas.

---

**Um dev trabalhando em uma feature não mexe com tag.**

Quem cria tags na real é:

* o pipeline (automatizado),
* ou a pessoa responsável pelo release (Tech Lead, DevOps, etc.).

Então, apesar de ser **importante no ciclo de desenvolvimento**,
**não é uma preocupação diária do dev comum**.

---

* **Você não cria tags o tempo todo**.
* Você **interpreta tags**, **faz checkout nelas**, usa para debugging.
* Seu pipeline vai criar tags automaticamente quando a `main` passa em todos os testes.

**Tags fazem parte do fluxo de versionamento profissional**, não importa o padrão (GitFlow ou Trunk-Based).

---

**Tags continuam extremamente relevantes, mas invisíveis no dia a dia do dev.**
Elas importam especialmente para:

* versionamento de releases,
* infraestrutura de CI/CD,
* deploys,
* rollback,
* rastreabilidade.

Não são “coisa antiga”.
São parte do básico de qualquer software moderno.

---

## Criando Tags

### 1. **Criar uma tag anotada (a mais usada no mundo real)**

Essa é a que vale para versionamento de software.

```bash
git tag -a v1.0.0 -m "Primeiro release estável"
```

Explicando rápido:

* `-a` cria uma tag anotada (com autor, data, mensagem, metadados).
* `v1.0.0` é o nome da tag (padrão semver).
* `-m` adiciona a mensagem do release.

**É essa aqui que times e pipelines usam.**

---

### 2. **Listar tags**

```bash
git tag
```

Ou, com detalhes:

```bash
git show v1.0.0
```

---

### 3. **Enviar a tag pro GitHub (importante!)**

Criar localmente **não envia automaticamente** pro remoto.

```bash
git push origin v1.0.0
```

Ou enviar todas de uma vez:

```bash
git push origin --tags
```

---

### 4. **Criar uma tag leve (lightweight)**

Quase nunca usada pra versões, mas existe:

```bash
git tag v1.0.0
```

Sem metadados. Sem mensagem. Só um ponteiro.

---

### 5. **Apagar uma tag**

Local:

```bash
git tag -d v1.0.0
```

Remoto:

```bash
git push origin --delete v1.0.0
```

---

### 6. **Marcar versão a partir de um commit antigo**

Se você percebe que deveria ter marcado uma versão, mas já avançou:

```bash
git tag -a v1.0.0 <hash-do-commit>
```

---

### 7. **Checar uma versão específica do código**

Isso aqui é ouro para fazer debug de algo antigo:

```bash
git checkout v1.0.0
```

Lembrando: isso te coloca em **detached HEAD**.
Se quiser trabalhar com o código dessa tag:

```bash
git checkout -b hotfix/v1.0.0-fix
```

---

### 8. **Como se encaixa no fluxo de versionamento**

Em projetos reais, a tag geralmente é criada quando:

* O código está na `main` ou `master`
* Todos os testes passaram
* O build está estável

Fluxo mais comum:

```bash
git checkout main
git pull origin main
git tag -a v2.3.1 -m "Release 2.3.1 - correção de bugs"
git push origin v2.3.1
```

Muitos times automatizam isso via GitHub Actions (CI/CD):

* Passou no pipeline → pipeline cria a tag → pipeline cria o release → pipeline faz o deploy.

---

### 9. **Em resumo**

| Ação                            | Comando                           |
| ------------------------------- | --------------------------------- |
| Criar tag anotada (recomendada) | `git tag -a vX.Y.Z -m "mensagem"` |
| Criar tag leve                  | `git tag vX.Y.Z`                  |
| Listar tags                     | `git tag`                         |
| Enviar tag                      | `git push origin vX.Y.Z`          |
| Enviar todas                    | `git push origin --tags`          |
| Apagar local                    | `git tag -d vX.Y.Z`               |
| Apagar remoto                   | `git push origin --delete vX.Y.Z` |
| Criar tag retroativa            | `git tag -a vX.Y.Z <hash>`        |
| Ver detalhes                    | `git show vX.Y.Z`                 |
| Checar código da tag            | `git checkout vX.Y.Z`             |

---

