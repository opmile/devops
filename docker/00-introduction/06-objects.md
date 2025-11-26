# Objetos Docker

---

# 1. Imagens (Docker Images)

A **imagem** é o *molde*, a *receita*, o *modelo imutável* do ambiente que você quer rodar.

Ela contém:

– Sistema de arquivos base (Ubuntu, Alpine, JDK, etc.)
– Binários necessários
– Configurações
– Dependências
– Seu código
– O comando padrão para iniciar a aplicação

Uma imagem **não muda** depois de criada.
Ela é como um *snapshot* de um ambiente pronto.

Exemplo mental fácil:

**Imagem = Classe no Java**
**Container = Objeto instanciado dessa classe**

Ou seja:
A imagem é a definição.
O container é a execução.

> Uma imagem Docker é um template somente leitura que define tudo o que é necessário para criar um container: sistema base, dependências, configurações e instruções de execução. Ela costuma ser construída em camadas, muitas vezes partindo de outra imagem — como usar ubuntu e adicionar Apache, seu código e ajustes necessários. Essas camadas são definidas por um Dockerfile, onde cada instrução vira uma parte da imagem. Como apenas as camadas modificadas precisam ser reconstruídas, as imagens permanecem leves, rápidas de montar e fáceis de distribuir. Você pode usar imagens prontas de registries ou criar as suas próprias para moldar exatamente o ambiente que sua aplicação precisa.

---

# 2. Containers

O **container** é uma **instância de uma imagem** rodando em isolamento.

Ele tem:

– Processos rodando
– Sistema de arquivos criado a partir da imagem
– Rede isolada
– Mapeamentos de porta
– Volumes opcionais para persistência

Enquanto a imagem é estática, o container é **dinâmico** — ele vive, roda, gera logs, consome CPU e pode morrer.

Exemplo simples:

```
docker run postgres
```

Isso cria **um container** com base na **imagem do Postgres**.

Criar outro:

```
docker run postgres
```

Agora você tem **dois containers diferentes**, rodando a partir da mesma imagem.

---

# 3. Networks (Docker Networks)

Uma **network** do Docker é um ambiente de rede isolado onde containers podem conversar entre si.

Pensa assim:

– Sem network, cada container vive num mundinho próprio.
– Com network, eles podem se enxergar por nome e trocar tráfego.

Isso resolve um monte de dor de cabeça, porque evita o caos de ter que mapear mil portas no host.

Exemplo:

Você roda:

```
docker network create minha-rede
```

Depois sobe dois containers:

```
docker run --network=minha-rede --name=db postgres
docker run --network=minha-rede --name=api minha-api
```

A API consegue acessar o banco por:

```
host = db
port = 5432
```

Simples e limpo.

---

# Como esses objetos se relacionam

## **Imagem → Container**

A imagem é a receita.
O container é o prato servido.

Uma imagem pode gerar **quantos containers você quiser**.

---

## **Container → Network**

Containers podem ser conectados a networks.
Isso permite:

– Comunicação entre eles
– DNS interno pelo nome do container
– Isolamento de tráfego (cada network é um ambiente à parte)

Containers em redes distintas não se enxergam — a menos que você permita.

---

## Outros objetos

– **Volumes:** armazenam dados persistentes
(containers morrem, dados ficam)

– **Dockerfile:** receita para construir imagens

– **Registries:** onde as imagens ficam armazenadas (Docker Hub, etc.)

Mas os três pilares para entender o Docker de verdade são:

**image → container → network**

---
