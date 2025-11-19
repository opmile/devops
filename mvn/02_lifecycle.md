# O Processo de `build`

---

## 1. **Primeiro: o Maven tem um “ciclo de vida”**

Pensa no Maven como uma esteira de montagem. Ele tem etapas fixas, chamadas “fases”.
Quando você roda um comando, na real você está pedindo “execute esta fase e tudo até ela”.

As mais importantes:

1. `validate` — checa se o projeto faz sentido
2. `compile` — compila o código fonte
3. `test` — compila e executa testes
4. `package` — empacota (gera `.jar` ou `.war`)
5. `verify` — valida coisas extras
6. `install` — instala no repositório local (`~/.m2`)
7. `deploy` — manda para um repositório remoto (não é seu caso ainda)

Isso é a espinha dorsal.

---

## 2. **Uso no dia a dia**

* o que o comando faz
* quando ele é útil
* como ele se encaixa no ciclo de build
* e como isso funciona num projeto Spring Boot.

---

### `mvn clean`

Apaga toda a pasta `target/`.

**Por que isso importa?**
É como limpar a “cozinha” antes de cozinhar de novo. Evita lixo de builds anteriores, classes antigas, dependências sujas, etc.

**Quando usar?**

* Antes de rodar uma build completa
* Quando mexe no `pom.xml`
* Quando a IDE fica bugada com arquivos antigos
* Quando algo inexplicável começa a falhar (mais comum do que parece)

---

### `mvn compile`

Compila **somente o código fonte**, sem rodar teste.

**Quando usar?**

* Acabou de alterar o `pom.xml` (dependências)
* Quer saber se o código compila *antes* de rodar testes
* Quer só garantir que tudo está ok no módulo principal

---

### `mvn test`

Compila + executa testes.

**Útil para:**

* validar localmente antes de subir código
* rodar CI manualmente
* garantir integridade do projeto

---

### `mvn package`

Esse é muito importante em Spring Boot.

Ele gera o artefato final:

* No Spring Boot, é um **fat jar** (um jar que já contém tudo pra rodar).

Geralmente é algo como:

```
target/minha-app-1.0.0.jar
```

**Quando usar?**

* Quando você quer gerar o jar final e executar manualmente:

  ```
  java -jar target/minha-app.jar
  ```
* Quando está preparando o jar pra colocar num servidor ou num Dockerfile.

---

### `mvn install`

Faz tudo:
clean -> compile -> test -> package -> install

E no final, coloca seu jar no repositório local (`~/.m2`).

**Por que isso importa?**
Se você trabalha com microserviços, projetos modulares, libs internas… o `install` é como “publicar localmente” pro resto do seu ecossistema poder usar.

**Quando usar?**

* Sempre antes de subir código para garantir que tudo está 100%
* Quando trabalha com vários módulos que dependem uns dos outros
* Quando algo deve ser disponibilizado localmente para outros projetos

Esse é o comando que a maioria dos pipelines de CI executa.

---

### `mvn spring-boot:run`**

É suficiente para *desenvolver*, mas não substitui o processo de build.

O `spring-boot:run` faz o seguinte:

* compila o projeto
* roda a aplicação diretamente da IDE / source
* recarrega automaticamente algumas mudanças (dependendo da config)

Se você deseja alterar o `pom.xml`, para retirar ou adicionar dependências, `mvn spring-boot:run` comando já faz o trabalho do `mvn clean` + `mvn compile`.

`mvn spring-boot:run` já faz tudo que você precisa para desenvolvimento, inclusive quando você muda o pom.

**O que ele NÃO faz:**

* não gera o jar final
* não roda testes automaticamente
* não garante que seu projeto é “publicável”
* não simula o processo que o servidor real vai usar
* não replica o pipeline de CI

Ele é ótimo para:

* desenvolvimento rápido
* testar endpoints enquanto você mexe no código
* trabalhar sem precisar reconstruir a aplicação toda hora
* subir a aplicação em uma port (8080 com Tomcat)

Você usa isso aqui quando quer trabalhar, testar mudanças, ver endpoints funcionando, iterar no código.

É o equivalente ao botão verde da IDE.

Ou seja: `spring-boot:run` = “deixa meu projeto rodando pra eu continuar codando”.

Mas ele **não** substitui isso aqui:

```
mvn clean package
```

e principalmente isso:

```
mvn clean install
```

**Use `mvn clean install` quando:**

* terminou um bloco de código importante

* vai abrir um Pull Request

* vai dar push para o GitHub

* quer garantir que TUDO compila e TODOS testes passam

* quer gerar um .jar de verdade (para rodar ou empacotar em Docker)

* quer simular o pipeline CI

* quer ter certeza de que o projeto funciona fora da sua IDE

**É seu modo de validação e build.**

---

### **Durante o desenvolvimento:**

```
mvn spring-boot:run
```

---

### **Antes de commitar / abrir PR:**

```
mvn test
```

---

### **Antes de subir pro GitHub (simulação do CI):**

```
mvn clean install
```

---

### **Quando quiser gerar o jar final (produção / deploy / Docker):**

```
mvn clean package
java -jar target/minha-app.jar
```

---

### 1) `spring-boot:run` é só um plugin

Ele não faz parte do ciclo de vida de build. É uma conveniência, só isso.

### 2) Building é sobre “gerar artefato”

Run é sobre “executar localmente”.

### 3) Em empresas, ninguém faz deploy usando o plugin

Sempre usam o jar gerado ou uma imagem Docker.

---

* **Só usar `spring-boot:run` não basta.**
  Ele serve para desenvolvimento, não para validar build.

* **Para garantir a integridade do projeto**, o combo é:

  ```
  mvn clean install
  ```

* **Para gerar o jar final da aplicação:**

  ```
  mvn package
  ```

* **Para rodar rápido enquanto desenvolve:**

  ```
  mvn spring-boot:run
  ```

