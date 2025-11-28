# Multi-stage Build

---

Quando você escreve um Dockerfile normal para apps Java com Maven, você tem **duas opções**:

### **1. Buildar o JAR *na sua máquina***

Você roda:

```
mvn clean package
```

Depois copia o jar:

```
COPY target/minha-app.jar app.jar
```

**Problemas:**

* sua máquina precisa ter Maven instalado
* sua máquina precisa ter JDK instalado
* sua máquina precisa ter a mesma versão que o resto do time
* builds ficam diferentes entre desenvolvedores
* se você mudar de computador, tudo quebra

É **acoplamento do dev ao ambiente local**.

---

### **2. Buildar TUDO dentro do Docker (com Maven dentro)**

Sem multi-stage, você faria:

```
FROM maven:3.9-eclipse-temurin-17

WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package

CMD ["java", "-jar", "target/minha-app.jar"]
```

**Problemas sérios:**

* A imagem final fica ENORME (contém Maven + JDK + dependências + JAR)
* Você leva para produção coisas que só servem para DEV (Maven)
* Mais lenta para baixar, mais lenta para iniciar

Ou seja: você quer um jar simples rodando, mas carrega um “caminhão” junto dele.

---

## **A SOLUÇÃO: Multi-Stage Build**

O multi-stage é basicamente:

**"Use uma imagem pesada SÓ para construir. Use outra imagem leve para rodar."**

Assim:

```
# STAGE 1 — construir (precisa de Maven e JDK completo)
FROM maven:3.9-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

# STAGE 2 — rodar (só precisa do JRE!)
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
CMD ["java", "-jar", "app.jar"]
```

---

## **Por que isso é tão poderoso?**

Aqui vai o motivo REAL, que todo dev sente no dia a dia:

### **1. A imagem final fica MINÚSCULA**

Sem Maven:
Sem dependências de build:
Sem JDK completo:

Ela só contém:

* seu jar
* um JRE mínimo

Produção ama isso.

---

### **2. O build vira 100% reprodutível**

Seja no seu notebook, no CI/CD, no servidor da empresa, no computador da NASA — **o resultado é idêntico**.
Porque tudo acontece dentro do Docker.

Você anula:

* “funciona no meu PC”
* “minha versão do Maven é outra”
* “o JDK aqui é diferente”
* “no Windows deu pau”

---

### **3. O dev não precisa ter NADA instalado**

Com multi-stage:

* o DEV não precisa de Maven
* não precisa de JDK
* não precisa nem de Java instalado

Basta ter Docker.

Tudo acontece *dentro* do primeiro estágio do Dockerfile.

---

### **4. Você não leva lixo pra produção**

Produção roda só o **stage final**:

* só JRE
* só seu app
* bem menor
* arranca mais rápido
* consome menos RAM

O Maven e todo o resto fica descartado no primeiro estágio.

---

### **5. Build mais rápido depois de cacheado**

O stage de Maven baixa dependências uma vez.
Depois disso, o Docker cacheia essas camadas.
Isso acelera builds gigantescos, especialmente em Spring Boot.

---

Multi-stage serve para:

**"Construir pesado e rodar leve."**

* Stage 1 → usa Maven + JDK completo para compilar
* Stage 2 → só copia o JAR e roda com um JRE mínimo
* Resultado → imagem final pequena, limpa, rápida, previsível

É isso. Simples e brilhante.

---

## Projetos Spring

Em projetos Spring, usar multi-stage build no Dockerfile é a forma mais limpa e reprodutível de construir a aplicação. 

O primeiro estágio usa o Maven Wrapper (`mvnw` e `.mvn/`) para compilar o projeto sempre com a mesma versão do Maven, independentemente do ambiente local. Assim, o build acontece de forma totalmente isolada e previsível dentro do Docker. 

O segundo estágio é uma imagem leve (somente JRE), que recebe apenas o JAR final já empacotado. Isso reduz drasticamente o tamanho da imagem, elimina ferramentas desnecessárias em produção (como Maven e JDK completo) e garante um runtime limpo, rápido e consistente. 

Em essência: **construímos pesado, rodamos leve**, mantendo builds reprodutíveis e imagens finais muito menores.

---

# **Dockerfile completo — Spring Boot com Maven Wrapper (multi-stage)**

```Dockerfile
# ------------------------
# STAGE 1 — Build da aplicação
# ------------------------
FROM eclipse-temurin:17-jdk AS build

WORKDIR /app

# Copia apenas o wrapper primeiro (cache mais eficiente)
COPY mvnw mvnw
COPY .mvn/ .mvn/

# Copia o POM para baixar dependências
COPY pom.xml .

# Dá permissão de execução ao wrapper
RUN chmod +x mvnw

# Baixa dependências para aproveitar cache em builds futuros
RUN ./mvnw dependency:go-offline

# Agora copia o código-fonte
COPY src ./src

# Build final do JAR
RUN ./mvnw clean package -DskipTests


# ------------------------
# STAGE 2 — Runtime da aplicação
# ------------------------
FROM eclipse-temurin:17-jre

WORKDIR /app

# Copia apenas o JAR final do estágio anterior
COPY --from=build /app/target/*.jar app.jar

EXPOSE 8080

# Comando para rodar o app
CMD ["java", "-jar", "app.jar"]
```

---

