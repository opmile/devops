# Métricas de Uso de Containers

Container = processo isolado por **cgroups** + **namespaces**. Não é VM.
Compartilha kernel com host. Se um container come toda RAM/CPU, **afeta
vizinhos**. Métrica = visibilidade desse impacto.

> **cgroups** (Control Groups) são um recurso do kernel Linux que **limita,
> contabiliza e isola** o uso de hardware (CPU, memória, I/O de disco, rede)
> por um grupo de processos. Funcionam como um "administrador": definem o teto
> de RAM que o container pode usar, ditam o que acontece se ele tentar
> exceder, e limitam tempo de CPU ou número de cores que o container pode
> consumir — impedindo que um container monopolize o sistema. Toda métrica
> que `docker stats` mostra vem de leitura direta dos cgroups.

---

## Por que isso importa

- **Capacity planning:** quanto cabe na máquina antes de saturar?
- **Detectar leak:** RAM subindo monotonicamente = bug (memória, goroutine,
  conexão não fechada).
- **Right-sizing:** setar `--memory`, `--cpus` com base em dado real, não chute.
- **Debug de performance:** lento por CPU? I/O? rede? Métrica isola a camada.
- **Custo:** cloud cobra por recurso reservado. Subutilizar = dinheiro
  desperdiçado. Sobreutilizar = OOMKill em produção.

---

## `docker stats` — métricas em tempo real

Stream ao vivo, atualiza ~1s. Lê de cgroups (`/sys/fs/cgroup/...`).

```bash
docker stats
```

Saída típica:

```
CONTAINER   CPU %   MEM USAGE / LIMIT   MEM %   NET I/O       BLOCK I/O   PIDS
abc123      12.5%   245MiB / 2GiB       11.9%   1.2MB / 800kB 50MB / 10MB 23
```

### Campo a campo

#### CPU %
Percentual de **CPU host total disponível ao container**.
- `100%` = 1 core saturado.
- `400%` num host de 8 cores = 4 cores em uso.
- Vem de `cpuacct.usage` (nanosegundos consumidos) vs tempo real decorrido.
- Sustained alto = workload CPU-bound. Pode ser normal (worker batch) ou bug
  (loop quente, GC pressure).

#### MEM USAGE / LIMIT
- **Usage:** RSS + page cache + swap usado pelo cgroup.
- **Limit:** valor de `--memory`, ou RAM total do host se sem limite.
- Inclui **page cache** — número infla, não é leak necessariamente.
- Chegar perto do limit = risco de **OOMKill** (kernel mata o processo).
- Diferente de `top` rodado dentro do container (que pré-cgroupsv2 enxergava
  host inteiro).

#### MEM %
`Usage / Limit`. Útil para alerta (acima de 80% investigar).

#### NET I/O
Bytes **RX/TX acumulados** desde start do container.
- **RX (Receive):** bytes recebidos — dados entrando no container (request
  chegando, resposta de API externa, download).
- **TX (Transmit):** bytes enviados — dados saindo do container (response
  voltando, request pra serviço externo, upload).
- É **normal** RX e TX serem desbalanceados. Servidor web típico: RX baixo
  (requests pequenos), TX alto (responses com HTML/JSON/assets). Worker que
  consome fila e envia métrica: o oposto.
- Soma de todas interfaces do namespace de rede do container.
- Útil para detectar: exfiltração de dados, gargalo de rede, serviço chatty.
- **Não diferencia** tráfego interno (entre containers) vs externo.

#### BLOCK I/O
Bytes read/write em **block devices**. Acumulado.
- **Block device** = dispositivo de armazenamento que lê/escreve em blocos
  fixos com acesso aleatório (SSD, HD, USB, CD-ROM). É a categoria de I/O
  usada por arquivos persistentes — em oposição a *character devices*
  (teclado, mouse, áudio) que streamam bytes sequencialmente.
- Mede I/O em volumes/disco real.
- **Não conta** tmpfs ou overlayfs em memória (essas não tocam block device).
- Write contínuo alto = log mal configurado, DB sem cache, swap thrashing.

#### PIDS
Processos + threads no container.
- Cada thread do OS conta 1.
- Limite via `--pids-limit`. Default depende do runtime.
- Fork bomb / leak de threads (em apps Go: goroutines presas em syscalls) =
  sobe sem parar.

### Flags úteis

```bash
docker stats --no-stream             # snapshot único, não streaming
docker stats --format "{{json .}}"   # JSON pra parsing programático
docker stats abc123 def456           # filtrar containers específicos
```

---

## `docker inspect` — config + estado (snapshot estático)

Não é métrica viva. É **metadado** completo em JSON. Par natural do `stats`:
`stats` diz "consome 1.9GiB"; `inspect` diz "limite é 2GiB" → entende risco.

```bash
docker inspect <container>
```

### Campos relevantes pra métricas

#### `HostConfig` — limites configurados

```json
"Memory": 2147483648,        // limit em bytes (--memory)
"MemorySwap": -1,            // swap permitido
"NanoCpus": 2000000000,      // 2 CPUs (--cpus)
"CpuShares": 1024,           // peso relativo entre containers
"PidsLimit": 100,
"BlkioWeight": 500           // peso de I/O
```

#### `State` — sinais de problema

```json
"OOMKilled": true,           // kernel matou por OOM — sinal vermelho
"ExitCode": 137,             // 137 = SIGKILL (geralmente OOM)
"RestartCount": 12,          // crash loop em andamento
"StartedAt": "..."
```

#### `NetworkSettings`
IPs, portas, redes anexadas. Não tem throughput aqui (isso é `stats`).

### Filtros para evitar JSON inteiro

```bash
docker inspect --format '{{.State.OOMKilled}}' <ct>
docker inspect --format '{{.HostConfig.Memory}}' <ct>
```

---

## Docker Desktop — Resource Usage extension

GUI em cima dos mesmos dados de cgroups. Mostra:

- Gráficos temporais (não só snapshot).
- Por container e agregado.
- Histórico curto (não persiste muito).

Diferença prática: enxerga **tendência**. CLI mostra "agora". Extension mostra
"últimos minutos" → vê spike, leak crescente, padrão cíclico.

Limitação: dev-only. Em produção usa-se Prometheus + cAdvisor + Grafana
(mesma fonte — cgroups — mas com retenção longa, alerting, multi-host).

---

## Mapa de leitura — métrica → significado

| Métrica                  | Sinal de alerta            | Possível causa                                       |
| ------------------------ | -------------------------- | ---------------------------------------------------- |
| CPU % alto sustained     | >80% por minutos           | loop quente, GC pressure, container undersized       |
| MEM crescendo monotonic  | nunca cai                  | leak (goroutine, conn, buffer não liberado)          |
| MEM ~limit + OOMKilled   | exit code 137              | limit baixo OU leak real                             |
| NET I/O explodindo       | TX alto sem motivo aparente| exfiltração, retry storm, log shipping mal config    |
| BLOCK I/O write alto     | sustained                  | log verboso, DB sem WAL adequado, swap thrashing     |
| PIDS subindo             | nunca estabiliza           | leak de threads/goroutines presas em syscalls        |

---

## Conexão Golang

Goroutines **não** são 1:1 com PIDs. Runtime Go usa **scheduler M:N** — N
goroutines multiplexadas em M threads OS.

- 10k goroutines podem rodar em ~8 threads OS → `PIDS` no `docker stats` baixo.
- Mas se goroutines bloqueiam em syscalls (I/O de rede, disco), o runtime cria
  threads OS adicionais → `PIDS` sobe.
- `PIDS` crescendo monotônico em app Go containerizado = goroutines presas em
  syscall = bug de timeout ou leak de conexão.

Para o lab de socket: instrumentar com `expvar` ou `net/http/pprof`
**complementa** as métricas de container. Container enxerga "de fora" (recursos
do SO). `pprof` enxerga "por dentro" (goroutines, heap, blocking profile).

---

## Próximo passo prático

Rodar em algum container do lab:

```bash
docker stats --no-stream
docker inspect --format '{{json .HostConfig}}' <ct> | jq
```

Comparar o que `stats` mostra (uso real) vs limites em `inspect` (config).
Aí decide se precisa apertar `--memory`/`--cpus` ou se está com folga.
