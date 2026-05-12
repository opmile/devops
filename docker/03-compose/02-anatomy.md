# Anatomia de um `compose.yml`

Os campos que aparecem em **praticamente todo projeto real**. Pula o que é raro ou avançado — esse é o vocabulário de primeiro contato e o que você verá repetidamente no mercado.

---

## `services:` — o bloco principal

Cada service vira um container. A chave do service vira o hostname interno (DNS) dentro da rede do Compose.

```yaml
services:
  api:       # ← outros containers acessam por "api"
    ...
  db:        # ← outros containers acessam por "db"
    ...
```

Dentro do bloco do service, você combina os campos abaixo.

---

## `image:` vs `build:`

Dois jeitos de definir de onde vem a imagem do container.

**`image:` — usa imagem pronta (registry):**

```yaml
services:
  db:
    image: postgres:16
```

Funciona quando o serviço é genérico (Postgres, Redis, Nginx, RabbitMQ). Não há código seu para empacotar.

**`build:` — constrói a partir de um Dockerfile:**

```yaml
services:
  api:
    build: .                    # contexto = diretório atual, lê ./Dockerfile
```

Forma curta. O Compose roda `docker build` no contexto indicado.

Forma estendida, com mais controle:

```yaml
services:
  api:
    build:
      context: .
      dockerfile: Dockerfile.dev
      args:
        GO_VERSION: 1.22
```

Quando usar cada um:

- **`image:` para infra genérica** (banco, cache, broker).
- **`build:` para o seu código** (API, worker, frontend).

---

## `ports:` — exposição para o host

Equivalente ao `-p` do `docker run`. Formato `host:container`.

```yaml
services:
  api:
    ports:
      - "8080:8080"             # localhost:8080 → container:8080
      - "127.0.0.1:5432:5432"   # bind só no loopback
```

**Importante:** services do Compose já se enxergam pelo nome internamente, **sem precisar de `ports:`**. `ports:` só é necessário quando você (no host) quer acessar o serviço de fora — pelo navegador, por um cliente DB local, etc.

Banco de dados consumido só pela API? Geralmente **não precisa de `ports:`**. Expor o Postgres no host só é útil em dev quando você quer conectar com pgAdmin/DBeaver da máquina hospedeira.

---

## `environment:` e `env_file:`

Configuração via variável de ambiente — padrão 12-factor, presente em qualquer app séria.

**Inline:**

```yaml
services:
  api:
    environment:
      DB_HOST: db
      DB_PORT: 5432
      LOG_LEVEL: debug
```

**Via arquivo:**

```yaml
services:
  api:
    env_file:
      - .env.local
```

Onde `.env.local` é um arquivo `chave=valor`:

```
DB_HOST=db
DB_PORT=5432
LOG_LEVEL=debug
```

Boa prática de mercado: variáveis sensíveis (passwords, API keys) vão em `.env.local` que está no `.gitignore`. Inline serve para configs não-sensíveis. Em produção real, secrets vão para sistemas dedicados (Vault, AWS Secrets Manager, K8s Secrets), nunca para o YAML.

---

## `depends_on:` — ordem de startup

```yaml
services:
  api:
    depends_on:
      - db
```

Garante que `db` sobe **antes** de `api`. Mas atenção à pegadinha clássica: `depends_on` só espera o container existir, **não** espera o serviço estar pronto. Postgres pode estar com o processo de pé mas ainda inicializando — sua API tenta conectar e falha.

Solução real no mercado: a aplicação implementa retry de conexão no startup. Compose tem `depends_on` com `condition: service_healthy` (combinado com `healthcheck:`), mas exige configurar o healthcheck explicitamente. Para começar, retry no código + `depends_on` simples já resolve 90% dos casos.

---

## `command:` e `entrypoint:` — override

Sobrescreve o `CMD` ou `ENTRYPOINT` definido na imagem.

```yaml
services:
  api:
    image: minha-api
    command: ["./api", "--debug", "--port=8080"]
```

Útil quando você quer rodar a mesma imagem com configurações diferentes (dev vs migração, por exemplo). No dia a dia, raramente precisa — o `CMD` do Dockerfile já cobre.

---

## `restart:` — política de reinício

```yaml
services:
  api:
    restart: unless-stopped
```

Valores comuns:

- **`no`** (default) — não reinicia.
- **`always`** — reinicia sempre, mesmo após `docker stop`.
- **`unless-stopped`** — reinicia sempre, exceto se você parou explicitamente. **Default de mercado para dev**.
- **`on-failure`** — só reinicia se sair com erro.

---

## `volumes:` — onde a coisa fica interessante

Volumes são como a aplicação **persiste e compartilha dados**. Container é efêmero — sobe, morre, é recriado. Sem volume, todo dado escrito dentro dele evapora junto. Há três tipos práticos:

### 1. Bind mount — pasta do host dentro do container

Mapeia um diretório (ou arquivo) do seu computador para dentro do container.

```yaml
services:
  api:
    build: .
    volumes:
      - ./src:/app/src           # host:container
      - ./config.yml:/etc/app/config.yml
```

**Quando usar (uso massivo em dev):**

- **Hot reload em desenvolvimento.** Você edita o código no editor (host), o container vê a mudança na hora. Sem precisar rebuildar imagem a cada salvamento.
- Injetar arquivos de configuração específicos do ambiente local.
- Compartilhar logs do container para uma pasta do host.

**Quando NÃO usar:**

- **Em produção.** Bind mount acopla o container ao layout de arquivos do host. Frágil, não portável, dependente de paths absolutos.
- Para dados que precisam sobreviver sem depender de uma pasta específica do host (use named volume).

### 2. Named volume — volume gerenciado pelo Docker

Volume com nome próprio, armazenado em um diretório interno do Docker (`/var/lib/docker/volumes/`).

```yaml
services:
  db:
    image: postgres:16
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:                       # declara o volume top-level
```

**Quando usar (uso de mercado padrão):**

- **Persistência de banco de dados.** O caso clássico. Container Postgres morre? Você sobe outro com o mesmo volume e os dados estão lá.
- Cache que vale a pena preservar entre restarts.
- Dados de aplicação que devem sobreviver a `docker compose down` (mas não a `down -v`).

**Vantagens sobre bind mount:**

- Não depende de paths do host.
- Portável entre máquinas (com `docker volume` é possível backup/restore).
- Performance melhor em macOS/Windows (bind mount nesses sistemas atravessa uma camada de virtualização e fica lento).

### 3. Anonymous volume — esquece, raramente usado

Volume sem nome, criado automaticamente. Gera lixo difícil de rastrear. Evita.

---

### Volume na sintaxe curta vs longa

A maioria dos projetos usa a forma curta `host:container`:

```yaml
volumes:
  - ./src:/app/src
  - db-data:/var/lib/postgresql/data
```

Compose decide se é bind mount ou named volume pelo formato:

- Começa com `.` ou `/` → bind mount (caminho do host).
- Começa com nome simples (`db-data`) → named volume (precisa estar declarado no bloco `volumes:` top-level).

Forma longa permite read-only, opções extras:

```yaml
volumes:
  - type: bind
    source: ./config.yml
    target: /etc/app/config.yml
    read_only: true
```

Use a longa quando precisar de `read_only: true` ou opções específicas. Caso contrário, sintaxe curta.

---

### Pegadinha clássica: bind mount sobrescreve

```yaml
services:
  api:
    build: .
    volumes:
      - ./src:/app/src
```

Se o Dockerfile fez `COPY ./src /app/src` durante o build, o bind mount em runtime **substitui** esse conteúdo pelo `./src` do host. Em dev é o que você quer (hot reload). Mas se você esquecer um bind mount em produção, ele pode mascarar arquivos da imagem.

---

### Regra prática de mercado

| Cenário                                       | Tipo                          |
|-----------------------------------------------|-------------------------------|
| Código fonte em dev (hot reload)              | Bind mount                    |
| Arquivo de config específico do ambiente      | Bind mount (read-only)        |
| Dados de banco (Postgres, MySQL, Mongo)       | Named volume                  |
| Cache compartilhado entre containers          | Named volume                  |
| Logs que você quer inspecionar no host        | Bind mount para `./logs`      |
| Qualquer coisa em produção                    | Named volume (ou storage K8s) |

---

## Exemplo juntando tudo

Uma stack típica de mercado em dev:

```yaml
services:
  api:
    build: .
    ports:
      - "8080:8080"
    environment:
      DB_HOST: db
      DB_PORT: 5432
      REDIS_HOST: cache
    env_file:
      - .env.local
    volumes:
      - ./src:/app/src           # bind mount: hot reload
    depends_on:
      - db
      - cache
    restart: unless-stopped

  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: appdb
    volumes:
      - db-data:/var/lib/postgresql/data   # named volume: persistência
    restart: unless-stopped

  cache:
    image: redis:7
    restart: unless-stopped

volumes:
  db-data:
```

Sobe tudo:

```bash
docker compose up -d
```

E você tem API + Postgres + Redis conversando entre si, código com hot reload, dados de banco persistidos entre restarts. Esse esqueleto é praticamente universal — vai encontrar variações dele em qualquer projeto que use Compose em dev.
