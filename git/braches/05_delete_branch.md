# Deletar Branch Local e Remota

| Comando                                     | Onde age   | O que faz                                                                                       | Quando usar                                                                                                    |
| ------------------------------------------- | ---------- | ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `git branch -d feat/branch-feat`            | **Local**  | Deleta **a branch local** do seu repositório (no seu computador). Não afeta o remoto.           | Quando você **já fez merge** da branch com a `main` (ou outra base) e não precisa mais dela localmente.        |
| `git push origin --delete feat/branch-feat` | **Remoto** | Deleta **a branch no Git remoto (origin)**, ou seja, ela deixa de existir no GitHub/GitLab/etc. | Quando você quer **remover a branch do servidor remoto** após ela ter sido mesclada e não for mais necessária. |

* `git branch -d` → limpa seu repositório local.
* `git push origin --delete` → limpa o repositório remoto.

Ou seja, normalmente você usa os dois em sequência:

```bash
git branch -d feat/branch-feat        # apaga local
git push origin --delete feat/branch-feat  # apaga remoto
```

**Dica bônus:**
Se quiser garantir que a branch já foi mesclada antes de apagar, use o comando:

```bash
git branch --merged
```

Ele lista todas as branches já mescladas na branch atual (geralmente `main`).

O principal motivo pra usar `git push origin --delete feat/branch-feat` é **manter a organização do repositório remoto (GitHub, GitLab etc.)**.

### 1. Depois que a feature já foi mesclada (merge)

É o caso mais comum.
Depois que você faz o merge da branch (por exemplo, `feat/login-page`) na `main`, aquela branch não tem mais propósito.
Apagá-la evita acúmulo de branches antigas e deixa o repositório **limpo, fácil de navegar e mais profissional**.

Faz sentido **em repositórios colaborativos**, onde muita gente cria branches.
Sem limpeza, o repositório remoto pode acabar com dezenas de branches desatualizadas.

---

### 2. Quando uma branch de teste ou experimento não será usada

Se você criou uma branch só pra testar uma ideia (ex: `test/try-new-api`) e decidiu que não vai levar aquilo adiante, pode deletá-la do remoto pra não confundir os outros desenvolvedores (ou o “você do futuro”).

---

### 3. Quando uma feature foi descartada

Às vezes, uma branch começa uma feature que é cancelada antes do merge.
Nesse caso, deletar também faz sentido — mantém o histórico do que foi feito localmente, mas **sem poluir o remoto** com algo que não foi entregue.

---


