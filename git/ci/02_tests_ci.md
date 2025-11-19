# Testes em CI

* Quem escreve os testes? O dev.

* Quem executa os testes automaticamente? O pipeline.**

---

### **1. O DEV é responsável por escrever os testes da própria feature**

Num time profissional, ninguém espera que o pipeline “faça mágica”.
O pipeline **não cria testes**.
Ele **roda testes**.

Então quando você desenvolve uma nova feature, o esperado é:

* escrever a feature
* escrever os testes unitários que validam essa feature
* ajustar testes quebrados
* garantir que tudo passa localmente antes do push

Esse é o comportamento profissional porque:

* você garante que não tá empurrando código quebrado
* você não sobrecarrega a CI
* você mantém a qualidade do projeto

Em muitos times, inclusive, **não escrever testes é considerado quebra de contrato técnico** do time.

---

### **2. O pipeline valida o que você fez**

Assim que você sobe sua branch ou abre uma PR:

1. O pipeline é acionado.
2. Ele roda todos os testes existentes (os seus + os já existentes).
3. Se **qualquer** teste falhar:

   * a PR bloqueia
   * você tem que corrigir antes de mergear
4. Se tudo passar, você ganha o “check verde”.

Ou seja:
**o pipeline é o juiz, não o autor.**

---

### **Por que isso é tão importante no mundo DevOps?**

Porque DevOps preza por:

* qualidade constante
* entregas pequenas e frequentes
* risco reduzido
* automação máxima

E isso só funciona se o desenvolvedor entregar código **já testado**, e não um “experimento que talvez funcione”.

Imagina se ninguém faz teste e deixa pra CI descobrir tudo…
O time não progride nunca. Fica num “loop infinito de incêndio”.

---

### **Como fica a lógica final?**

**Na sua máquina:**

* cria a feature
* cria os testes
* roda `mvn test` / `gradle test` / ou o que for
* tudo passando → push

**No repositório:**

* pipeline da CI roda
* refaz todo o processo
* se algo quebrar: PR é bloqueada
* se tudo ok: liberado pra merge

---

### **Por que o pipeline precisa rodar de novo se eu já testei local?**

Porque localmente:

* sua máquina tem dependências diferentes
* sua JVM pode ser outra versão
* seu ambiente pode estar “contaminado” com artefatos velhos
* você pode ter rodado só parte dos testes sem perceber

O pipeline roda em um ambiente **padronizado e limpo**, igual pra todo mundo.
Isso garante que “na máquina do CI”, tudo de fato funciona.

---

## Quando adicionar testes?

---

### **Quando você cria uma feature relevante, você escreve os testes dela.**

No fluxo profissional + CI + GitHub organizado, a expectativa é:

**Feature nova → testes novos.**

Se você adicionou:

* endpoint novo,
* regra de negócio nova,
* cálculo novo,
* validação nova,
* persistência nova…

…então automaticamente existe **algum tipo de teste** que deve acompanhar isso.

Isso mostra maturidade, reduz bugs e faz seu código durar.

---

**Mas você não testa *tudo* que existe.**
Você testa o que faz sentido.
E isso varia muito dependendo do tipo de código.

---

### **O que deve SEMPRE ter teste**

Se a feature mexe com:

#### Regra de negócio

Ex: “usuário não pode se cadastrar com email duplicado”, “preço com desconto não pode ficar negativo”, “senha deve ter 8+ caracteres”.

Isso tem que ter teste. Sempre.

#### Cálculos

Se envolve matemática, probabilidade de bug sobe exponencialmente.
Teste é obrigatório.

#### Validações

Coisas como “campo obrigatório”, “formato inválido”, “CPF inválido”, etc.

#### Funções puras (aquelas que recebem entrada e devolvem saída previsível)

Essas são as mais fáceis e mais importantes de testar.

#### Código que, se quebrar, causa efeito cascata

Ex: conversão de DTO, parsing de JSON, lógica que prepara dados pra salvar no banco.

---

### **O que usualmente deve ter teste (mas depende do contexto)**

#### Acesso a banco

Aqui existem dois caminhos:

* **teste de integração** com Testcontainers ou H2
* **mock do repositório** para focar só na regra de negócio

Depende do grau de importância da operação.

#### Controller (endpoints)

Normalmente você testa:

* se retorna o status correto (200, 400, 404…)
* se valida a entrada
* se chama o service certo

Não precisa testar tudo como se fosse um sistema inteiro, só o essencial.

#### Fluxos críticos

Login, pagamento, cadastro… esses são obrigatórios em qualquer projeto sério.

---

### **O que geralmente NÃO vale a pena testar**

#### Código trivial

Getters, setters, construtores.
Sem chance — isso é perda de tempo e ruído.

#### Código só de roteamento

Tipo um controller que só chama um service sem lógica nenhuma.

#### Classes utilitárias extremamente simples

Ex: uma função que retorna `String.isBlank()` por baixo.
Isso já foi testado pela API Java.

#### Infraestrutura pura

Configurações do Spring, beans triviais, arquivos YAML.
Só testaria se for uma config crítica.

---

Se a ausência desse teste pode gerar:

* bug silencioso
* regressão futura
* comportamento inesperado
* custo emocional em debug
* medo de refatorar
* impacto direto na experiência do usuário
* impacto direto no banco de dados

…então sim, você deve testar.

É um guia prático:
**se quebrar, vai doer? → testa.

---

# **E por que isso é tão valorizado?**

Porque quando você começa a trabalhar com CI + workflow profissional, seus testes viram:

* o alicerce que protege sua main
* a garantia de que PRs não quebram o projeto
* a segurança pra refatorar sem medo
* o que separa “dev iniciante” de “dev que pensa como engenheiro”

E isso inclui projetos solo — porque você está se preparando para a expectativa real do mercado.

---

## Cenários de Teste em Pipelines CI

Se você configurou o pipeline para rodar **no push**, e você dá push **sem ter testes escritos**, o que acontece depende de duas coisas:

1. **Seu projeto tem testes obrigatórios?**
2. **Os testes que existem falham?**

---

### 1 — Você **não tem nenhum teste no projeto**

Exemplo: pasta `src/test/java` vazia.

O que a pipeline faz? Ela roda normalmente e **PASSA**.

Não tem teste nenhum para rodar, logo o Maven executa o estágio `test` e diz:

* “Não encontrei testes.”
* “Ok, vida que segue.”

Não é erro. Isso é o comportamento padrão.

---

### CENÁRIO 2 — Você tem testes, mas **não escreveu testes novos para a feature**

Exemplo:

* Testes antigos existem.
* Mas sua nova feature não está coberta.

O que acontece? A pipeline *passa*, **DESDE QUE**:

* os testes existentes **NÃO QUEBREM**.

CI não sabe que você “deveria ter escrito mais testes”.
Ela só sabe:
“rodei testes → deu tudo certo → ok.”

O trabalho de obrigar teste por feature é **política do time**, não da CI.

---

### CENÁRIO 3 — Você tem testes e eles quebram por causa da nova feature

Aí sim: a pipeline vai **falhar** no estágio `test`.

E vai te impedir de abrir PR (se estiver configurado assim).

Isso é ótimo, porque te protege de mandar código quebrado.

---

### CENÁRIO 4 — Você tem testes, mas quer que a pipeline só seja acionada no PR

Se você coloca CI em **pull_request** somente:

* push não faz nada
* PR roda a pipeline
* se falhar, você conserta antes de mergear

Esse é o fluxo mais usado e mais inteligente para iniciantes.

---

CI não adivinha se *faltam* testes.
Ela só sabe:

* “tem teste?”
* “falhou?”
* “passou?”

Se quiser algo mais rígido, existe:

* cobertura mínima (com Jacoco)
* qualidade obrigatória (SonarQube)
* regras preventivas (Checkstyle, PMD etc.)

Mas isso é etapa avançada.

---

