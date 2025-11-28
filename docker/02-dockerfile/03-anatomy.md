# **Anatomia dos principais comandos**

---

## **1. FROM — Diz de onde sua imagem começa**

Define a **base**.
É tipo dizer: “quero começar a construção da imagem em cima de uma outra imagem que já tem Java”.

Exemplo:

```Dockerfile
FROM openjdk:17-jdk-slim
```

Isso significa:
– pega uma imagem oficial do Java
– versão 17
– variante leve (“slim”)

Sem FROM, sua imagem seria um Linux vazio. Nada funcionaria.

---

## **2. WORKDIR — Define a pasta onde o resto dos comandos vão rodar**

É como dizer: “a partir de agora, faça tudo dentro dessa pasta”.

Exemplo:

```Dockerfile
WORKDIR /app
```

Sem isso, você trabalharia na raiz do container, o que é feio e bagunçado.

---

## **3. COPY — Copia arquivos do seu PC para dentro da imagem**

Exemplo:

```Dockerfile
COPY . .
```

Isso significa:
– copie tudo do diretório atual da sua máquina
– para o diretório atual definido no WORKDIR (`/app`)

Você pode ser mais específico:

```Dockerfile
COPY target/meuapp.jar app.jar
```

---

## **4. RUN — Executa comandos DURANTE a construção da imagem**

É só para **build time**, não para runtime.

Exemplo:

```Dockerfile
RUN javac Main.java
```

Isso roda no momento em que a imagem é criada.
Não é um comando que será executado toda vez que o container iniciar.

RUN serve pra instalar libs, compilar código, gerar pastas etc.

---

## **5. CMD — Define o comando padrão quando o container inicia**

Esse é **o comando final**, que vai rodar toda vez que você der:

```
docker run minha-imagem
```

Exemplo:

```Dockerfile
CMD ["java", "-jar", "app.jar"]
```

CMD é tipo: “quando o container subir, execute isso aqui”.

---

## **6. EXPOSE — Só documenta a porta**

Esse comando **NÃO abre porta**.
Ele só diz: “meu app usa essa porta”.
A abertura real é no `docker run -p`.

Exemplo:

```Dockerfile
EXPOSE 8080
```

Serve como dica para devs e ferramentas de orquestração.

---