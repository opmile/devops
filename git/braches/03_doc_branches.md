# Convenção de Nomenclatura de Branches

A distinção (`feat/`, `fix/`, `chore/`, etc.) vem de uma **convenção de nomenclatura**, não de um recurso técnico do Git.
O Git não “sabe” o que é uma `feat` ou um `fix`; são apenas **nomes de branches que ajudam na organização visual e semântica** do projeto.

---

### O que são esses prefixos (`feat/`, `fix/`, etc.)

São **convenções**, geralmente inspiradas nos padrões de *Conventional Commits* e *Git Flow*.
Eles indicam o **propósito da branch**, ajudando o time (ou você mesma, no futuro) a entender rapidamente o tipo de mudança que está sendo feita.

---

### Principais tipos de branches

| Prefixo     | Significado                     | Quando usar                                                                            |
| ----------- | ------------------------------- | -------------------------------------------------------------------------------------- |
| `feat/`     | *Feature* (nova funcionalidade) | Quando vai adicionar uma funcionalidade nova ao sistema.                               |
| `fix/`      | *Bug fix* (correção de erro)    | Quando vai corrigir um comportamento inesperado ou falha.                              |
| `chore/`    | *Chore* (tarefas rotineiras)    | Mudanças que não alteram o código funcional — configs, dependências, ajustes do build. |
| `refactor/` | *Refactor* (refatoração)        | Melhorias no código sem mudar comportamento.                                           |
| `docs/`     | *Documentation*                 | Alterações em documentação (`README`, tutoriais, etc.).                                |
| `test/`     | *Tests*                         | Criação ou ajuste de testes automatizados.                                             |
| `hotfix/`   | *Correção urgente*              | Correções rápidas e críticas direto na produção.                                       |
| `release/`  | *Preparação de release*         | Branch para empacotar e testar antes de lançar uma nova versão.                        |

---

### Estrutura hierárquica (feat dentro de feat?)

Tecnicamente, **você pode criar quantas branches quiser**, partindo de qualquer outra.
Por exemplo:

```
main
 └─ feat/cadastro-de-alunos
      └─ feat/validação-email-aluno
```

Mas **isso não é o mais comum**.
Normalmente, cada branch nasce a partir da `main` (ou `develop`, se o projeto seguir o *Git Flow* clássico).
Criar sub-branches de `feat` pode fazer sentido se:

* várias pessoas estão desenvolvendo partes diferentes da mesma feature;
* ou se você quer isolar incrementos dentro de uma grande funcionalidade.

Nesse caso, basta garantir que cada sub-branch seja *mergeada* de volta à branch principal da feature antes do merge final para `main`.

O Git não cria pastas; ele trata tudo depois da `/` como parte do nome da branch. Mas o GitHub (e outras interfaces) **mostram como se fossem pastas** — o que ajuda a organizar visualmente.

Então se você tem:

```
feat/login
feat/cadastro
feat/api-integracao
```

Essas três branches são independentes, todas partindo da `main` (ou `develop`), mas aparecem agrupadas sob o “prefixo” `feat/` na interface.

O mesmo vale pra:

```
fix/erro-login
fix/nullpointer-produto
chore/update-dependencias
```

Tudo no mesmo nível, só categorizado por prefixo.
Não há herança ou dependência entre elas — é apenas uma convenção de nomenclatura que torna o histórico e o repositório mais legíveis.

---

## Commits em Projetos Solo

Mesmo num projeto solo, ter uma estrutura de commits bem pensada deixa o código mais legível, fácil de manter e até ajuda quando você for revisitar ou mostrar no GitHub.

---

## 1. O que faz sentido num projeto solo

Você não precisa de burocracia de equipe (como aprovar PRs ou abrir branches para cada detalhe), mas ainda vale:

* **Branches separadas por tipo de tarefa:** `feat/`, `fix/`, `refactor/`… ajudam a rastrear o que cada coisa faz.
* **Commits pequenos e atômicos:** cada commit deve representar uma unidade lógica de mudança (algo que “faz sentido sozinho”).
* **Mensagens claras e consistentes:** isso é o que mais importa pra você no futuro.

---

## 2. Estrutura da mensagem de commit

Use um **padrão simples, mas expressivo** — tipo o *Conventional Commits*, adaptado para uso pessoal:

```
<tipo>: <descrição curta>
```

Tipos mais úteis pra você:

| Tipo       | Quando usar                            | Exemplo                                               |
| ---------- | -------------------------------------- | ----------------------------------------------------- |
| `feat`     | nova funcionalidade                    | `feat: adiciona endpoint de cadastro`                 |
| `fix`      | correção de bug                        | `fix: corrige erro de validação de email`             |
| `refactor` | melhora código sem mudar comportamento | `refactor: simplifica método de autenticação`         |
| `docs`     | muda ou adiciona documentação          | `docs: adiciona instruções no README`                 |
| `chore`    | tarefas de manutenção                  | `chore: atualiza dependências do Maven`               |
| `test`     | adiciona ou ajusta testes              | `test: cria testes unitários para classe UserService` |

---

## 3. Estrutura opcional (para commits maiores)

Quando o commit tem mais contexto, dá pra expandir:

```
feat: implementa API de cadastro de alunos

- cria classe AlunoController
- adiciona endpoint POST /alunos
- inclui validação de campos obrigatórios
```

Essa quebra de linhas (título + corpo com bullets) é perfeita pra commits que introduzem várias mudanças relacionadas.

---

## 4. Fluxo prático sugerido

1. Crie uma branch curta pra cada bloco de trabalho (nem precisa fazer PR):

   ```
   git switch -c feat/cadastro-alunos
   ```
2. Faça commits pequenos e coerentes:

   ```
   git add .
   git commit -m "feat: cria classe Aluno e repositório inicial"
   git commit -m "feat: adiciona método de validação de matrícula"
   ```
3. Quando terminar a feature, *mergeie* na main:

   ```
   git switch main
   git merge feat/cadastro-alunos
   git push
   ```
4. Apague a branch (opcional, pra manter limpo):

   ```
   git branch -d feat/cadastro-alunos
   ```

---

## 5. Boas práticas simples

* **Evite commits genéricos** tipo “update”, “ajustes” ou “mudanças finais”.
  Pense: “se eu lesse isso daqui a 3 meses, eu saberia o que mudei?”
* **Um commit por ideia** — se você corrigiu um bug e criou uma nova função, isso são dois commits.
* **Commits de progresso** (como “WIP” ou “em andamento”) podem existir, mas só se você ainda não vai publicar a branch. Depois dá pra “squashar” (juntar) antes de enviar.

---

## 6. Extra: exemplo real de histórico bem estruturado

```
feat: adiciona API de login e autenticação JWT
fix: corrige erro de serialização de datas no JSON
refactor: extrai lógica de validação para classe utilitária
docs: adiciona exemplos de uso da API no README
chore: atualiza dependências do Spring Boot
```

Mesmo sozinha, esse tipo de histórico mostra profissionalismo — e te ajuda a rastrear qualquer coisa no futuro sem se perder.

---
