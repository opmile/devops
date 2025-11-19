# Regra de Proteção

Você pode configurar seu repositório, dentro outras políticas de segurança, para que nada seja mergeado para a branch `main` sem devido PR aberto.

---

### 1) O que significa “ninguém pode dar push direto na main”?

Significa:

* main é uma branch **protegida**
* qualquer alteração precisa passar por:

  * **pull request**
  * **pipeline de CI**
  * **review (opcional, mas recomendado)**

Você impede o cenário caótico onde alguém manda código direto pra main às 23h e derruba o sistema inteiro.

---

### 2) O que isso impede, na prática?

Muita coisa. Vamos no tiro curto:

#### a) Impede código quebrado

Sem PR, qualquer pessoa poderia:

* esquecer um teste
* quebrar uma importação
* deixar um erro de compilação
* subir uma lógica mal escrita
* apagar uma pasta inteira sem perceber

Com PR + CI obrigatória, tudo isso **para antes** de chegar na main.

---

#### b) Impede mudança apressada ou emocional

Sim, isso existe:

* “Vou só fazer um hotfix aqui rapidinho…”
* “Ops, dei push no arquivo errado.”
* “Ah, esqueci de testar esse método.”

Esse tipo de push impulsivo desaparece.

---

#### c) Impede falta de histórico claro

Sem PR = tudo misturado.

Com PR =

* contexto
* discussão
* histórico
* descrição do problema
* link com a issue
* commit message limpa

Fica muito fácil rastrear quem mexeu no quê e por quê.

---

#### d) Impede código sem backup (sem branch própria)

Quando todo mundo trabalha em branch isolada:

* cada alteração tem “seu próprio espaço”
* você nunca sobrescreve nada acidentalmente
* rollback é trivial
* auditoria é simples

Sem isso, é o caos.

---

#### e) Impede que o ambiente de produção caia

Essa é a mais óbvia:

1. main = branch que gera deploy
2. push direto = risco
3. push direto sem teste = bomba relógio

PR + pipeline protegem seu deploy.

---

### 3) Qual o impacto real no código?

Transforma o projeto em algo:

* mais estável
* mais consistente
* mais revisável
* mais audítavel
* mais seguro
* mais fácil de dar manutenção
* menos propenso a regressões
* menos sujeito a “brincadeiras” ou descuidos

E principalmente:

**main vira um lugar sagrado.**
Tudo que entra ali foi verificado, testado e aprovado.

Esse é o comportamento de times maduros.

---

### 4) E para projetos solo, como o seu?

Sim, até no solo vale a pena.

Por quê?

* você cria hábito de trabalhar certo
* seu repositório parece profissional
* seu código quebra menos
* você se acostuma a depender da CI
* seu GitHub vira portfólio com fluxo de verdade
* você evita futuros “meu Deus, eu fiz cagada na main”

Você está treinando do jeito certo.

---

### main nunca deve ser um lugar onde:

* se experimenta algo
* se testa ideia
* se escreve código com pressa
* se faz push impulsivo
* se mexe direto sem passar por validação

### main deve ser o lugar onde:

* só entra código aprovado
* só entra código testado
* só entra código revisado
* só entra código que não quebrou nada

---
