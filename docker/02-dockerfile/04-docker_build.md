# `docker build`

**`docker build` é o comando que cria uma imagem Docker a partir do seu Dockerfile**.
Aquele `docker build -t minha-app:latest .` é o “feitiço” básico pra transformar seu projeto em uma imagem real.

Ele lê o **Dockerfile** no diretório que você indicar, executa cada instrução (FROM, COPY, RUN, CMD…), e no fim gera **uma imagem Docker**.

---

## **`-t minha-app:latest` → tagueando a imagem**

`-t` significa **tag**.
É onde você dá nome e versão para a imagem.

### **Formato:**

```
nome-da-imagem:tag
```

**Exemplos**:

* `minha-app:latest` → versão “latest”, a mais comum.
* `minha-api:v1` → primeira versão.
* `backend-spring:2025-01` → versionamento por data.
* `meuusuario/minha-app:prod` → já preparado pra mandar pro Docker Hub.

### Por que isso importa?

Porque:

* **`docker run minha-app`** só funciona se você nomeou a imagem.
* **`docker push`** só funciona se você tiver uma tag.
* Ter versões diferentes evita que você se embarace com imagens velhas.

---

## **`.` → contexto de build**

Esse ponto final (`.`) é essencial.
Ele significa:

> “Use este diretório como **contexto de build**.”

O Docker vai enviar esse diretório inteiro (todos os arquivos dentro dele) para o daemon durante o build.

### Se você faz:

```
docker build .
```

Ele procura o Dockerfile **neste diretório**
e copia tudo dentro dele para o build.

### Se você fizer:

```
docker build -t app ../
```

Aí ele usa a pasta de cima como contexto, etc.

O contexto é importante porque:

* É onde o Dockerfile vai começar a procurar arquivos que você copia com `COPY` e `ADD`.
* Você não deve enviar lixo (como node_modules, target, .idea) — por isso existe `.dockerignore`.

---

1. Docker pega a pasta atual (`.`)
2. Envia para o Docker daemon como contexto de build
3. Lê o Dockerfile
4. Executa cada instrução em camadas
5. Gera a imagem com o nome (`-t minha-app:latest`)
6. Salva a imagem localmenteboo

---

* `docker build` = cria imagem
* `-t` = nome + versão
* `minha-app:latest` = nomeando sua imagem
* `.` = contexto de build (pasta atual)

---
