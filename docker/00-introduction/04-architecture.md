# Arquitura Docker

> *Docker uses a client-server architecture. The Docker client talks to the Docker daemon, which does the heavy lifting of building, running, and distributing your Docker containers. The Docker client and daemon can run on the same system, or you can connect a Docker client to a remote Docker daemon. The Docker client and daemon communicate using a REST API, over UNIX sockets or a network interface. Another Docker client is Docker Compose, that lets you work with applications consisting of a set of containers.*

![Arquitetura Docker](https://docs.docker.com/get-started/images/docker-architecture.webp)

---

## 1. Docker Client (`docker`)

É o **comando que você digita no terminal**.

Quando você roda:

```
docker run postgres
docker build .
docker ps
```

Você **não está executando containers diretamente**.
O client só manda **requisições** para o *daemon* em forma de chamadas à API do Docker.

Pensa nele como o “controle remoto”.

– Você aperta o botão
– O controle manda o comando
– A TV é quem realmente faz o trabalho

Aqui, a “TV” é o daemon.

O Docker client pode se comunicar com mais de um *daemon*.

---

## 2. Docker Daemon (dockerd)

É o **motor** do Docker. O processo que faz tudo acontecer.

O daemon é responsável por:

– Construir imagens
– Gerenciar containers
– Criar redes
– Gerenciar volumes
– Puxar imagens do registry
– Expor a API que o client usa

Ele roda **no Docker Host** (a máquina onde o Docker está instalado), e é ele quem implementa a lógica: criar, iniciar, parar, remover e isolar containers.

Resumindo:
**O client pede. O daemon executa.**

---

## 3. Docker Host

É simplesmente **a máquina que está rodando o Docker** – seu computador, um servidor Linux, uma máquina EC2, um VPS, o que for.

Dentro dele, você tem:

– O **daemon**
– As **imagens** salvas localmente
– Os **containers** em execução
– As **networks** criadas pelo Docker
– Os **volumes** persistentes

O host fornece os recursos físicos:

– CPU
– RAM
– Disco
– Rede

E o daemon orquestra tudo isso para rodar containers isolados.

---

## 4. Registries (Docker Hub, ECR, GCR etc.)

Um **registry é um repositório de imagens** — como um GitHub só que para *imagens Docker*, não código.

O Docker Hub é um registry aberto que todos podem usar, e o Docker procura por imagens no Docker Hub por padrão. Você pode até criar seu próprio registry.

Quando você faz:

```
docker pull postgres
```

O client fala com o daemon → o daemon baixa do registry → a imagem aparece no seu host.

* O Docker puxa as imagens solicitadas da configuração do seu registry

Quando você faz:

```
docker push minhaimagem:1.0
```

O daemon sobe a imagem para o registry remoto.

O registry **não executa nada**, só armazena imagens.

---

#### Docker Hub

Docker Hub é basicamente o “GitHub das imagens Docker”. É um serviço de registry público onde você encontra, baixa e publica imagens prontas de praticamente qualquer tecnologia — bancos, linguagens, servidores, ferramentas. Quando você faz `docker pull postgres` ou `docker pull openjdk`, é do Docker Hub que o daemon busca essa imagem. Ele funciona como um repositório central — oficial e público — cheio de imagens mantidas pela própria Docker (como `postgres`, `mysql`, `redis`, `openjdk`) e também por empresas e desenvolvedores, permitindo que equipes compartilhem, versionem e distribuam ambientes completos de forma padronizada e rápida.

---

1. Você roda um comando no **client**
2. O client manda uma requisição à API do **daemon**
3. O daemon faz o trabalho no **host**
4. Se precisar de uma imagem, o daemon baixa ela de um **registry**
5. O daemon cria e gerencia o container
6. O client só exibe o resultado pro usuário

---

Imagine que você rodou:

```
docker run -p 5433:5432 postgres
```

O que acontece?

1. `docker` (client) recebe o comando
2. Ele manda: “daemon, quero um container postgres”
3. O daemon verifica se a imagem existe
   – não existe? baixa do registry
4. O daemon cria um container isolado
5. Mapeia a porta
6. Liga o container
7. O client te mostra os logs ou o ID do container

---

## Por que essa divisão existe?

Porque deixa o Docker:

– **Modular** (você pode trocar o client, o daemon continua igual)
– **Remoto** (client e daemon podem estar em máquinas diferentes)
– **Seguro** (o daemon controla recursos sensíveis, não o client)
– **Escalável** (vários clientes podem controlar o mesmo daemon — cenário de CI/CD)

---

**Client**: onde você digita comandos (controle remoto)
**Daemon**: quem faz o trabalho (motor)
**Host**: onde containers realmente rodam (máquina real)
**Registry**: onde imagens moram (armazém)

---

