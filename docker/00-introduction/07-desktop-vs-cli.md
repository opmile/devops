# Docker Desktop e Docker CLI 

---

## Onde entram os comandos da CLI?

Mesmo usando Docker Desktop, **quem realmente executa os comandos é o Docker Engine** (o daemon), e a **CLI é só a interface de texto** que conversa com ele.
O Docker Desktop apenas *fornece e gerencia* esse daemon no Windows/macOS.

Quando você digita:

```
docker pull
docker run
docker build
docker compose up
```

Isso está rodando **na CLI**, mas o Desktop está por trás fornecendo o ambiente onde o daemon vive.

Ou seja:

**Docker Desktop = gerenciador visual e “infraestrutura”
Docker CLI = onde você controla o Docker na prática**

---

## Quando você usa *Docker Desktop* diretamente?

Você usa o Desktop **quando quer visualizar e gerenciar** as coisas de forma gráfica:

– Ver containers rodando
– Ver logs de modo rápido
– Ver/pausar/remover containers
– Ver imagens, volumes e redes
– Resetar o ambiente
– Configurar recursos (RAM, CPU, disco)
– Ver informações do daemon
– Habilitar integrações (WSL, Kubernetes, etc.)

É a parte mais “painel de controle” da história.

---

## Quando você usa o *Docker CLI*?

**Quase sempre durante o desenvolvimento real**.

A CLI é usada para:

– Construir imagens (`docker build`)
– Rodar containers (`docker run`)
– Criar networks
– Criar volumes
– Subir stack com compose (`docker compose up`)
– Enviar imagem para registry (`docker push`)
– Inspecionar containers
– Fazer exec de comandos dentro de um container (`docker exec`)

Em resumo:
**Todo fluxo técnico de desenvolvimento usa CLI.
Docker Desktop é suporte, não ferramenta de trabalho principal.**

---

## A divisão simples pra guardar

**Docker Desktop**
– Ferramenta visual
– Gerencia o Docker Engine (especialmente no Windows/macOS)
– Facilita ver o que está rodando
– Configurações e diagnóstico
– Auxiliar do dev

**Docker CLI**
– Onde você realmente usa Docker
– Build, run, push, pull, compose
– Parte central do desenvolvimento
– Automatizável
– Usado em pipelines, scripts, automações

---

## Fluxo de desenvolvimento com Java + Docker

### 1. **Escreve o código Java**

Você trabalha normalmente no IntelliJ/Eclipse/VS Code.

Nenhum Docker aqui ainda.

---

### 2. **Testa localmente sem Docker (opcional)**

Roda sua aplicação com Maven/Gradle para garantir que compila e funciona.

Nenhum Docker aqui.

---

### 3. **Cria o Dockerfile**

Você define como sua aplicação será empacotada.

Ex.:

```
FROM eclipse-temurin:21-jdk
COPY target/app.jar /app.jar
CMD ["java", "-jar", "/app.jar"]
```

Ferramenta usada: **Editor de código**, não Desktop nem CLI.

---

### 4. **Constrói a imagem (CLI)**

```
docker build -t minha-api:latest .
```

Ferramenta: **CLI**

Docker Desktop apenas mostra visualmente que a imagem apareceu.

---

### 5. **Sobe dependências (CLI)**

Exemplo: Postgres

```
docker run -d --name db -p 5432:5432 -e POSTGRES_PASSWORD=123 postgres
```

Ferramenta: **CLI**

Docker Desktop só exibe que o container está rodando.

---

### 6. **Sobe sua aplicação usando Docker (CLI)**

```
docker run -d --name api --network host minha-api:latest
```

Ou com compose:

```
docker compose up --build
```

Ferramenta: **CLI**

Docker Desktop é apenas observador.

---

### 7. **Inspeciona logs, processos, métricas (CLI + Desktop)**

– Pelo terminal:

```
docker logs api
```

– Pelo Desktop: ver logs, reiniciar, parar, excluir container
*(mais prático para debug visual)*

Ferramentas: **CLI + Desktop**
Pagamento emocional: Desktop facilita muito quando você só quer olhar algo rápido.

---

### 8. **Gerencia containers/imagens (Desktop ou CLI)**

Opções:

**CLI**

```
docker ps
docker rm -f api
docker images
docker rmi minha-api
```

**Desktop**
– clicar em "stop", "delete", "logs", etc.

CLI = produtividade e automação
Desktop = conforto visual

---

### 9. **Publica a imagem (CLI)**

```
docker tag minha-api:latest meu-user/minha-api:1.0
docker push meu-user/minha-api:1.0
```

Ferramenta: **CLI**

Docker Desktop só mostra o estado final.

---

### 10. **Sobe em produção (CLI ou CI/CD)**

Os servidores usam Docker Engine diretamente.

Você nunca usa Docker Desktop aqui.

---

