# `git branch`

---

### 🧩 **Comandos de criação e navegação**

| Comando                          | Função                                             | Observação                                          |
| -------------------------------- | -------------------------------------------------- | --------------------------------------------------- |
| `git branch`                     | Lista todas as branches locais                     | Adicione `-r` para listar remotas e `-a` para todas |
| `git branch nome-da-branch`      | Cria uma nova branch **local**, sem trocar pra ela | Ainda não muda o contexto de trabalho               |
| `git checkout nome-da-branch`    | Muda para uma branch existente                     | Atualiza o diretório de trabalho                    |
| `git checkout -b nome-da-branch` | Cria e muda para a nova branch                     | Atalho muito usado para features                    |
| `git switch nome-da-branch`      | (Alternativa moderna ao checkout) muda de branch   | Mais intuitivo e seguro                             |
| `git switch -c nome-da-branch`   | Cria e muda para nova branch                       | Equivalente a `checkout -b`                         |

---

### 🚀 **Comandos de sincronização e push**

| Comando                             | Função                                         | Observação                              |
| ----------------------------------- | ---------------------------------------------- | --------------------------------------- |
| `git push origin nome-da-branch`    | Envia branch local para o repositório remoto   | Não cria vínculo automático             |
| `git push -u origin nome-da-branch` | Envia e cria o **vínculo upstream** (tracking) | Após isso, basta usar `git push`        |
| `git pull`                          | Atualiza branch local com o conteúdo remoto    | Faz *fetch + merge*                     |
| `git fetch`                         | Baixa atualizações remotas sem integrar        | Ideal para inspecionar antes de mesclar |
| `git fetch origin nome-da-branch`   | Atualiza só uma branch remota específica       | Evita baixar todas                      |

---

### 🔄 **Comandos de merge e integração**

| Comando                            | Função                                            | Observação                                 |
| ---------------------------------- | ------------------------------------------------- | ------------------------------------------ |
| `git merge nome-da-branch`         | Mescla uma branch à atual                         | Gera um commit de merge se houver mudanças |
| `git merge --no-ff nome-da-branch` | Faz merge mesmo que o Git pudesse "fast-forward"  | Mantém histórico explícito                 |
| `git rebase nome-da-branch`        | Reaplica commits da branch atual sobre outra base | Limpa o histórico, mas exige cuidado       |

---

### 🧹 **Comandos de gerenciamento e limpeza**

| Comando                                   | Função                                    | Observação                                 |
| ----------------------------------------- | ----------------------------------------- | ------------------------------------------ |
| `git branch -d nome-da-branch`            | Deleta branch local (se já mesclada)      | “d” de *delete* seguro                     |
| `git branch -D nome-da-branch`            | Força a exclusão da branch local          | Útil para descartar branches não mescladas |
| `git push origin --delete nome-da-branch` | Remove branch **remota**                  | Boa prática após merge                     |
| `git branch -m novo-nome`                 | Renomeia branch atual                     | Preserva histórico e vínculos locais       |
| `git branch -vv`                          | Mostra branches locais com suas upstreams | Exibe relação com branches remotas         |

---

### 🧠 **Resumo rápido**

| Situação                      | Comando típico                                      |
| ----------------------------- | --------------------------------------------------- |
| Criar e entrar em uma branch  | `git checkout -b feat/nova-feature`                 |
| Publicar no GitHub e vincular | `git push -u origin feat/nova-feature`              |
| Atualizar a branch principal  | `git pull origin main`                              |
| Integrar branch de feature    | `git checkout main` → `git merge feat/nova-feature` |
| Apagar branch depois do merge | `git branch -d feat/nova-feature`                   |

---

