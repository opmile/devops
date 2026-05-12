# Rede no Docker Compose

Compose tem um modelo de rede bem ergonômico: na maioria dos casos você **não precisa configurar nada** e os containers já se enxergam. Mas em alguns cenários (especialmente labs de baixo nível e inspeção de tráfego), o comportamento default não basta.

---

## A rede default — você ganha de graça

Quando você sobe um `compose.yml`, o Compose cria automaticamente uma **rede bridge isolada** para essa stack e conecta todos os services nela.

```yaml
services:
  api:
    image: minha-api
  db:
    image: postgres:16
```

Ao subir, o Compose:

1. Cria uma rede nomeada (ex.: `<nome-do-projeto>_default`).
2. Coloca `api` e `db` nessa rede.
3. Configura um DNS interno onde **cada service vira um hostname**.

Resultado prático:

```
Dentro do container "api":
  - "db" resolve para o IP do container do Postgres.
  - "api" resolve para o próprio IP.

A API conecta no Postgres com: postgres://db:5432
Nada de IPs, nada de `localhost`, nada de portas externas mapeadas.
```

Cada container ainda tem **seu próprio NET namespace** — IP próprio, interface própria, tabela de sockets própria. Eles conversam **através da rede bridge** que o Compose criou.

---

## `networks:` — definindo redes customizadas

Se você quer múltiplas redes (por exemplo, separar frontend público de backend privado), declara explicitamente:

```yaml
services:
  api:
    image: minha-api
    networks:
      - backend
      - frontend

  db:
    image: postgres:16
    networks:
      - backend       # db só na rede backend, isolado do frontend

  web:
    image: nginx
    networks:
      - frontend

networks:
  backend:
  frontend:
```

Resultado: `web` **não enxerga** o `db` — não estão na mesma rede. Só `api` consegue falar com ambos.

Esse padrão (separar redes por camada/sensibilidade) é comum em setups mais maduros e é a base mental para chegar em Kubernetes Network Policies depois.

---

## `network_mode: "service:X"` — compartilhar o NET namespace

Aqui o jogo muda. Em vez de criar uma rede onde dois containers conversam, você faz dois containers **usarem o mesmo NET namespace**.

```yaml
services:
  server:
    build: .

  inspector:
    build: .
    network_mode: "service:server"
```

Consequências:

- `inspector` **não tem rede própria**. Não tem IP próprio, não tem interface própria.
- `inspector` usa **a mesma interface `lo`**, **o mesmo IP**, **a mesma tabela de sockets** do `server`.
- `cat /proc/net/tcp` dentro do `inspector` mostra os sockets do `server`.
- Comunicação entre eles é via `localhost` (não via nome de service) — porque, para o kernel, eles são "a mesma máquina de rede".

Isso é radicalmente diferente do default. Não é "duas redes conversando" — é **uma rede só usada por dois containers**.

---

## A distinção que confunde iniciantes

Duas formas de "permitir comunicação" entre containers — e elas significam coisas diferentes.

| Aspecto                          | Mesma rede (default ou `networks:`)        | `network_mode: "service:X"`          |
|----------------------------------|---------------------------------------------|--------------------------------------|
| NET namespace                    | Um por container                            | Compartilhado entre containers       |
| IPs                              | IP próprio para cada um                     | Mesmo IP                             |
| Interface de rede                | Cada um tem a sua (`eth0`)                  | Ambos veem a mesma                   |
| Comunicação                      | `db:5432` (DNS interno → IP do outro)       | `localhost:5432`                     |
| Tabela de sockets (`/proc/net`)  | Distinta por container                      | Compartilhada                        |
| Caso de uso típico               | App + banco, app + cache, microserviços     | Sidecar de inspeção, debug, proxy    |

**Analogia:**

- Mesma rede = dois apartamentos no mesmo prédio, falam pelo interfone.
- `network_mode: "service:X"` = duas pessoas no mesmo apartamento, gritam pelo corredor.

**Quando usar `network_mode: "service:X"` no mundo real:**

- Sidecar de inspeção (você quer um container B vendo a rede do A).
- Sidecar de proxy (Istio, Linkerd, e companhia, com adaptações).
- Debug pontual: subir um container com `tcpdump`/`ss`/`netstat` no mesmo namespace de outro container para observar tráfego.
- Casos de teste de baixo nível onde você quer simular dois processos "na mesma máquina de rede".

No dia a dia comum de aplicação web/API, você raramente toca nisso. É um recurso poderoso para casos específicos.

---

## `ports:` revisitado — exposição vs comunicação interna

Vale martelar, porque é onde iniciantes mais escorregam:

- **Containers no mesmo Compose já conversam entre si.** Sem `ports:`, sem nada. Nome do service vira hostname interno.
- **`ports:` só é necessário para acessar o serviço A PARTIR DO HOST** (seu navegador, seu cliente DB local).

Antipadrão comum: declarar `ports: ["5432:5432"]` no Postgres "porque a API precisa acessar". A API roda dentro da rede do Compose — ela já acessa o Postgres pelo hostname `db`. Expor a porta no host só serve para conectar pgAdmin/DBeaver da máquina local.

Em produção, o normal é:

- Frontend público: `ports:` exposto (ou Nginx fazendo proxy).
- Tudo o resto: comunicação interna apenas, **sem `ports:`**. Reduz superfície de ataque.

---

## Resumo prático

| Você quer…                                                           | Solução                                              |
|----------------------------------------------------------------------|------------------------------------------------------|
| Dois containers conversando como serviços distintos                  | Default. Não configura nada. Usa nome do service.    |
| Separar containers em zonas (frontend público / backend privado)     | Múltiplas redes em `networks:`                       |
| Acessar um serviço do navegador/cliente local                        | `ports: ["host:container"]`                          |
| Dois containers compartilhando IP, sockets, interface de rede        | `network_mode: "service:X"`                          |
| Container ver a rede do host inteiro (raro, perigoso)                | `network_mode: "host"` (não funciona em macOS)       |

Comece com o default. Suba para redes customizadas quando precisar isolar. Use `network_mode: "service:X"` só quando for esse o objetivo (compartilhar namespace, não conversar via rede).
