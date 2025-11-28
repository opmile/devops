# Comandos Docker CLI

> https://docs.docker.com/get-started/docker_cheatsheet.pdf

---

## Comandos Nucleares

---

### 1. **docker run**

**O que faz:**
Cria **e executa** um container a partir de uma imagem.
É o comando mais importante do Docker.

**Exemplo:**

```
docker run -d -p 8080:80 nginx
```

**Tradução:**
– `-d` = detached mode. Roda esse container em segundo plano e te devolve o terminal.
– `-p 8080:80` = mapeia porta 8080 do host para a 80 do container
– `nginx` = imagem usada

**Situação prática:**
Você quer subir um servidor Nginx para testar seu front.
Roda o comando e acessa em `localhost:8080`.

---

### 2. **docker ps**

**O que faz:**
Lista todos os containers **ativos**, que estão rodando agora.

**Exemplo:**

```
docker ps
```

**Situação prática:**
Você quer descobrir se seu Postgres está rodando, ou qual container está usando qual porta.

**Dica:**
Para listar **todos**, inclusive parados:

```
docker ps -a
```

---

### 3. **docker images**

**O que faz:**
Lista todas as **imagens** que você tem baixadas/localmente.

**Exemplo:**

```
docker images
```

**Situação prática:**
Você quer saber se baixou a imagem do Java 21 ou se aquela versão antiga do Postgres ainda está ocupando espaço.

---

### 4. **docker pull**

**O que faz:**
Baixa uma imagem do registry (ex: Docker Hub) para sua máquina.

**Exemplo:**

```
docker pull postgres:16
```

**Situação prática:**
Você precisa testar sua API com a versão exata do Postgres da produção.
Você puxa essa versão e usa no `docker run`.

---

### 5. **docker stop**

**O que faz:**
Encerra um container em execução.

**Exemplo:**

```
docker stop api-container
```

Ou:

```
docker stop <container_id>
```

**Situação prática:**
Você quer parar o Postgres que está rodando para subir outro com parâmetros diferentes.
Ou quer parar seu app Java para reconstruir a imagem.

---

### 6. **docker rm**

**O que faz:**
Remove containers **parados**.

**Exemplo:**

```
docker rm api-container
```

Ou vários de uma vez:

```
docker rm container1 container2 container3
```

**Situação prática:**
Seu Docker está cheio de containers mortos, testes antigos, containers com erro, e você quer limpar a bagunça.

**Obs.:**
Para remover um container **rodando**, precisa forçar:

```
docker rm -f nome-container
```

---

### 7. **docker rmi**

**O que faz:**
Remove uma **imagem** da sua máquina.

**Exemplo:**

```
docker rmi postgres:16
```

**Situação prática:**
Você testou três versões diferentes do Postgres (ex.: 14, 15, 16) e agora quer liberar espaço no disco removendo as que não usa mais.

---

| Comando           | O que faz                       |
| ----------------- | ------------------------------- |
| **docker run**    | Cria e executa containers       |
| **docker ps**     | Lista containers rodando        |
| **docker ps -a**  | Lista todos (inclusive parados) |
| **docker images** | Lista imagens locais            |
| **docker pull**   | Baixa uma imagem                |
| **docker stop**   | Para um container               |
| **docker rm**     | Remove containers parados       |
| **docker rmi**    | Remove imagens                  |

---

## Comandos Intermediários

---

### **1. docker exec**

**O que faz:**
Executa um comando *dentro* de um container que já está rodando.
É o “abrir a porta dos fundos” do container.

**Exemplos:**

```bash
docker exec -it meu-container bash
```

Entra no container com um shell interativo.

```bash
docker exec meu-container ls /app
```

Lista arquivos dentro da pasta `/app`.

**Quando usar na prática:**

* Depurar erros dentro de containers.
* Conferir arquivos internos.
* Rodar migrações, scripts, ferramentas que só existem lá dentro.
* Inspecionar logs ou configurações sem parar o container.

---

### **2. docker logs**

**O que faz:**
Mostra a saída do STDOUT e STDERR de um container.
É basicamente “ver o console da aplicação”.

**Exemplos:**

```bash
docker logs meu-container
```

```bash
docker logs -f meu-container
```

`-f` = “follow”, igual ao `tail -f`.

**Quando usar:**

* Acompanhar o comportamento do container em tempo real.
* Ver erros que quebraram sua aplicação.
* Observar logs de início da aplicação durante o desenvolvimento.

---

### **3. docker inspect**

**O que faz:**
Mostra *tudo* sobre um container, imagem, rede, volume…
É uma “radiografia” completa em JSON.

**Exemplo:**

```bash
docker inspect meu-container
```

Para pegar só o IP interno do container:

```bash
docker inspect -f '{{ .NetworkSettings.IPAddress }}' meu-container
```

**Quando usar:**

* Ver configuração de rede.
* Checar portas mapeadas.
* Ver labels, binds, volumes.
* Depurar containers que não conectam entre si.

---

### **4. docker network ls**

**O que faz:**
Lista todas as redes Docker existentes.

**Exemplo:**

```bash
docker network ls
```

**Quando usar:**

* Ver se sua app está na rede certa.
* Conferir redes bridge criadas por aplicações compostas.
* Organizar microserviços que precisam se comunicar.

---

### **5. docker volume ls**

**O que faz:**
Lista os volumes persistentes criados pelo Docker.

**Exemplo:**

```bash
docker volume ls
```

**Quando usar:**

* Ver onde estão os dados persistentes.
* Conferir se volumes antigos ficaram largados no seu sistema.
* Diagnosticando containers que têm dados inconsistentes.

---

### **6. Fluxo essencial com docker build**

Agora a parte em que você realmente cria **suas próprias imagens**.

## **Comando: docker build**

**O que faz:**
Constrói uma imagem a partir de um `Dockerfile`.

**Exemplo básico:**

```bash
docker build -t minha-api .
```

`-t` = nome e tag
`.` = contexto (normalmente a pasta atual)

**Ver imagens criadas:**

```bash
docker images
```

**Rodar sua imagem:**

```bash
docker run -p 8080:8080 minha-api
```

**Fluxo real no dia a dia:**

1. você edita seu código;
2. altera o Dockerfile (se necessário);
3. executa `docker build`;
4. roda a imagem com `docker run`;
5. testa;
6. faz ajustes;
7. rebuild;
8. repete.

Esse ciclo é o core da vida com Docker.

---

### **7. Outros comandos intermediários que você TEM que conhecer**

## **docker network create**

Cria redes personalizadas.

```bash
docker network create minha-rede
```

Quando usar:

* Microserviços que precisam conversar.
* Containers que precisam isolamento.
* Separar ambiente local em redes independentes.

---

#### **docker volume create**

Cria volumes persistentes manualmente.

```bash
docker volume create meus-dados
```

Quando usar:

* Bancos de dados com persistência real.
* Compartilhar dados entre containers.

---

#### **docker rm -f**

Remove containers **forçadamente**.

```bash
docker rm -f meu-container
```

Bom pra:

* Limpar ambientes rapidamente.
* Remover containers travados.

---

#### **docker system prune**

Limpeza geral (containers parados, imagens órfãs, cache).

```bash
docker system prune
```

Para apagar volumes também:

```bash
docker system prune --volumes
```

Ótimo quando seu Docker começa a “engordar”.

---

#### **docker tag**

Renomeia ou cria tags para uma imagem.

```bash
docker tag minha-api:latest meuuser/minha-api:v1
```

Importante para:

* Enviar pro Docker Hub / registries.
* Organizar versões.

---

#### **docker push**

Envia uma imagem para um registry (Docker Hub, etc.).

```bash
docker push meuuser/minha-api:v1
```

---

#### **docker version**

Exibe as informações de versões do Docker instalado na máquina

```
docker version
```
---

#### **docker info**

Exibe as informações de versões do sistema Docker na máquina

```
docker info
```
---

| **Comando**               | **O que faz**                                        | **Exemplo**                                 | **Quando usar**                                               |
| ------------------------- | ---------------------------------------------------- | ------------------------------------------- | ------------------------------------------------------------- |
| **docker exec**           | Executa comandos dentro de um container em execução. | `docker exec -it api bash`                  | Depurar, inspecionar arquivos, rodar scripts internos.        |
| **docker logs**           | Mostra os logs (STDOUT/STDERR) do container.         | `docker logs -f api`                        | Ver erros, acompanhar inicialização, monitorar comportamento. |
| **docker inspect**        | Exibe detalhes completos em JSON.                    | `docker inspect api`                        | Consultar rede, portas, volumes, metadata.                    |
| **docker network ls**     | Lista todas as redes Docker.                         | `docker network ls`                         | Ver redes disponíveis, checar isolamento e comunicação.       |
| **docker network create** | Cria uma nova rede.                                  | `docker network create minha-rede`          | Organizar microserviços, separar ambientes.                   |
| **docker volume ls**      | Lista volumes persistentes.                          | `docker volume ls`                          | Ver dados persistidos, diagnosticar volumes perdidos.         |
| **docker volume create**  | Cria um volume manualmente.                          | `docker volume create dados-db`             | Persistência de banco de dados, pastas compartilhadas.        |
| **docker build**          | Constrói uma imagem a partir de um Dockerfile.       | `docker build -t minha-api .`               | Criar imagens próprias, preparar deploy.                      |
| **docker tag**            | Cria/renomeia tags em uma imagem.                    | `docker tag minha-api meuuser/minha-api:v1` | Versionar e preparar para push.                               |
| **docker push**           | Envia imagem para um registry.                       | `docker push meuuser/minha-api:v1`          | Compartilhar, fazer deploy remoto.                            |
| **docker rm -f**          | Remove um container à força.                         | `docker rm -f api`                          | Limpar ambiente ou containers travados.                       |
| **docker system prune**   | Limpa recursos não usados.                           | `docker system prune --volumes`             | Liberar espaço, resetar ambiente.                             |


 






