## **Dockerfile Java completo e simples**

Aqui vai um bem básico para um app Java com um `.jar` pronto:

```Dockerfile
FROM openjdk:17-jdk-slim

WORKDIR /app

COPY target/app.jar app.jar

EXPOSE 8080

CMD ["java", "-jar", "app.jar"]
```

Explicando rapidamente a lógica:
– usa uma imagem do Java
– define a pasta de trabalho
– copia seu jar
– documenta a porta
– define o comando de execução

A partir daí, você só faz:

```
docker build -t meuapp .
docker run -p 8080:8080 meuapp
```

---

### obs: O artefato final da aplicação (fat) `.jar`

`app.jar` é simplesmente o artefato final da sua aplicação Java — o executável gerado depois que você compila seu projeto.

Se fosse um projeto Java simples (sem Maven/Gradle), você poderia gerar assim:

```
javac MinhaApp.java
jar cfe app.jar Main MinhaApp.class
```

— Mas ninguém faz isso na vida real.
— Isso é só o modo mais “cru” de imaginar o jar.

Na prática, quase sempre é Maven ou Gradle que gera o JAR para você.

Quando você tem um projeto Maven (ex.: Spring Boot), você gera um JAR **com dependências empacotadas** usando:

```
mvn clean package
```

Isso vai gerar algo como:

```
target/minha-app-1.0.0.jar
```

Ou, se for Spring Boot:

```
target/meu-spring.jar
```

Esse JAR de Spring Boot já vem com:

* Suas classes
* Suas dependências (Spring, Jackson, Lombok, etc.)
* O servidor embutido (Tomcat ou Netty)
* Um entrypoint claro (classe main)

Esse tipo de JAR é chamado de **fat jar** ou **uber jar**.

* O nome do jar vem do **artifactId + version** do pom.
* Você descobre olhando o pom ou rodando `mvn package` e vendo o arquivo na pasta target.

---

**Dockerfile para projeto Maven**

Se o Maven já gerou o JAR, o Dockerfile fica assim:

```
FROM eclipse-temurin:17-jre

WORKDIR /app

COPY target/minha-app-1.0.0.jar app.jar

EXPOSE 8080

CMD ["java", "-jar", "app.jar"]
```

Simples.

Você apenas copia o JAR pronto.

---