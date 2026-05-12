# Docker Compose

Docker Compose é a ferramenta oficial para **orquestrar múltiplos containers de forma declarativa**, num único arquivo `docker-compose.yml` (ou `compose.yml`). Em vez de subir cada container manualmente com `docker run` longos, você descreve toda a stack em YAML e sobe tudo com um comando.

---

## O problema que o Compose resolve

Sem Compose, uma aplicação minimamente real exige rituais cansativos na CLI:

```bash
docker network create app-net
docker volume create db-data

docker run -d --name db \
  --network=app-net \
  -v db-data:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=secret \
  postgres:16

docker run -d --name api \
  --network=app-net \
  -p 8080:8080 \
  -e DB_HOST=db \
  minha-api:latest

docker run -d --name redis \
  --network=app-net \
  redis:7
```

Três containers, uma rede, um volume, variáveis de ambiente. Aí você reinicia a máquina, ou troca de branch, ou outro dev clona o projeto. Refazer tudo isso na mão é um pesadelo — e suscetível a erro humano.

Com Compose, isso vira:

```bash
docker compose up -d
```

Um arquivo YAML versionado no git descreve toda a stack. Qualquer pessoa no time roda o mesmo comando e ganha o mesmo ambiente.

---

## O que o Compose faz por baixo

Nada de mágico. Compose lê o YAML e traduz para chamadas à API do Docker daemon:

- Cria as redes que você declarou (ou usa a default).
- Cria os volumes que você declarou.
- Constrói imagens (`build:`) ou puxa imagens (`image:`).
- Cria os containers com as flags equivalentes (`-p`, `-v`, `-e`, `--network`, etc.).
- Inicia tudo na ordem certa.

Ou seja: Compose **não é um novo motor**. É um *wrapper declarativo* sobre o Docker Engine. Mesmo daemon, mesmos containers, mesmas redes, mesmos volumes.

---

## v1 vs v2

Detalhe que confunde iniciantes:

- **v1 (legado):** comando era `docker-compose` (com hífen), binário Python separado. Saindo de cena.
- **v2 (atual):** `docker compose` (sem hífen), plugin nativo do Docker CLI, escrito em Go. Padrão hoje.

Outra diferença prática: arquivos antigos começavam com `version: "3.8"` no topo. Na v2, esse campo virou obsoleto — você pode omitir.

```yaml
# antigo (v1, ainda funciona mas obsoleto)
version: "3.8"
services:
  ...

# moderno (v2)
services:
  ...
```

Se ver `version:` em projetos antigos, não estranha. Em projetos novos, não coloca.

---

## Quando usar Compose

**Bom para:**

- **Desenvolvimento local.** Subir backend + banco + cache + frontend num comando. Caso de uso número 1.
- **Testes de integração em CI.** Pipeline sobe a stack toda, roda os testes, derruba tudo.
- **Demos e POCs.** Manda o `compose.yml` e o outro dev sobe tudo sozinho.
- **Labs e ambientes de estudo.** Reprodutível, isolado, descartável.

**Ruim para:**

- **Produção em escala real.** Compose roda num único host. Produção quer múltiplos hosts, auto-scaling, self-healing, rolling updates. Isso é Kubernetes, não Compose.
- **Aplicações que exigem alta disponibilidade.** Compose não tem orquestração de cluster.

Existe Docker Swarm, que estende Compose para múltiplos hosts, mas perdeu a guerra para o Kubernetes. No mercado, o fluxo padrão hoje é:

```
Compose (dev local + CI)  →  Kubernetes (staging + produção)
```

Os manifests Kubernetes são mais verbosos, mas o conceito de "descrever a stack declarativamente" é o mesmo. Aprender Compose primeiro é a porta de entrada natural para K8s depois.

---

## Estrutura mínima de um `compose.yml`

```yaml
services:
  api:
    build: .
    ports:
      - "8080:8080"
    environment:
      DB_HOST: db
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

Três blocos top-level que você verá repetidamente:

- **`services:`** — define os containers. Cada chave (`api`, `db`) é um service e vira o hostname do container na rede interna.
- **`volumes:`** — declara volumes nomeados gerenciados pelo Docker (persistência).
- **`networks:`** — (opcional) declara redes customizadas. Se omitido, todos os services compartilham uma rede default automática.

---

## Comandos básicos

| Comando                             | O que faz                                          |
|-------------------------------------|----------------------------------------------------|
| `docker compose up`                 | Sobe a stack (foreground, mostra logs).            |
| `docker compose up -d`              | Sobe em detached (background).                     |
| `docker compose up --build`         | Sobe forçando rebuild das imagens.                 |
| `docker compose down`               | Derruba a stack e remove containers + redes.       |
| `docker compose down -v`            | Igual, **mas também remove volumes**. Cuidado.     |
| `docker compose ps`                 | Lista containers da stack.                         |
| `docker compose logs -f`            | Stream dos logs de todos os services.              |
| `docker compose logs -f api`        | Logs só do service `api`.                          |
| `docker compose exec api bash`      | Abre shell dentro de um container rodando.         |
| `docker compose build`              | Só constrói as imagens, não sobe.                  |
| `docker compose restart api`        | Reinicia um service específico.                    |

`docker compose up -d` + `docker compose logs -f` + `docker compose down` é o tripé do dia a dia. O resto entra conforme a necessidade.
