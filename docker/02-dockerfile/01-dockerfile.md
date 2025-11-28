# Introdução ao Dockerfile

Um **Dockerfile** é simplesmente um **roteiro** que ensina o Docker a montar uma imagem sozinho.
Em vez de você configurar um ambiente manualmente — instalar dependências, copiar arquivos, compilar, configurar portas — você descreve todos esses passos *uma vez* dentro do Dockerfile. Depois disso, o Docker consegue reconstruir aquele ambiente idêntico sempre que você quiser.

Em termos práticos:

> Um Dockerfile automatiza 100% da criação da imagem.
> Sem ele, você dependeria de instalações manuais, comandos repetidos e ambientes inconsistentes.

---

## **Para que ele serve (direto ao ponto)**

* Criar imagens **reproduzíveis**
* Padronizar o ambiente de execução
* Evitar diferenças entre máquinas de dev, QA e produção
* Garantir que “funciona aqui = funciona em qualquer lugar”
* Automatizar o build para CI/CD

---

## **Por que precisamos dele?**

Sem um Dockerfile, cada dev teria que:

* instalar dependências na mão
* lembrar comandos
* configurar variável de ambiente
* usar versões específicas de Java, Node, Python, etc.
* repetir todo o processo quando mudar algo

Um Dockerfile encerra isso.
Você escreve:

```dockerfile
FROM eclipse-temurin:17
COPY . /app
WORKDIR /app
RUN ./mvnw package
CMD ["java", "-jar", "target/app.jar"]
```

E pronto: o Docker monta tudo sozinho, igualzinho, sempre.

---

## **Situações reais onde o Dockerfile substitui instalações manuais**

### **1. Configurar ambiente Java**

Sem Dockerfile:

* baixar JDK
* instalar Maven
* configurar JAVA_HOME
* compilar na mão
* rodar o jar

Com um Dockerfile:

* o próprio Docker já baixa a imagem com JDK
* compila automaticamente
* gera o JAR
* deixa tudo pronto num container limpo

---


