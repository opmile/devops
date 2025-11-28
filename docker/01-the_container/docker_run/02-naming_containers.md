# Nomeando Containers

---

**Por que o Docker cria containers com nomes diferentes (agitated_swirles → dreamy_agnesi)?**

Porque **toda vez que você roda `docker run`**, o Docker **cria um container novo**, do zero.
Ele **nunca “reinicia”** automaticamente o container anterior.
`docker run` **sempre cria**.

E se você não especifica o nome com `--name`, ele gera um nome aleatório e engraçado mesmo.

---

* A *imagem* é um **molde**, tipo um arquivo ZIP estático.
* Um *container* é **uma instância desse molde em execução**.

Quando você usa:

```
docker run -d -p 8080:80 docker/welcome-to-docker
```

Você está dizendo:

> “Cria um novo container usando essa imagem e roda ele.”

Por isso ele não reaproveita o antigo.

---

Primeiro, pare o container:

```
docker stop agitated_swirles
```

Depois, em vez de rodar `docker run` de novo, você dá:

```
docker start agitated_swirles
```

Isso **não cria um novo**, apenas **liga** o mesmo container anterior.

---

**Como impedir nomes aleatórios?**

Basta nomear o container você mesma:

```
docker run --name meu_welcome -d -p 8080:80 docker/welcome-to-docker
```

A partir daí:

* parar → `docker stop meu_welcome`
* iniciar → `docker start meu_welcome`
* ver logs → `docker logs meu_welcome`
* entrar nele → `docker exec -it meu_welcome sh`

---
