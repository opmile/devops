

# Port Mapping

---

Para criar e executar containers, em específico uma imagem `nginx` para subir o front na porta `8080`, usamos o comando:

```
docker run -d -p 8080:80 nginx
```

---

## **A lógica do `-p` (port mapping) — o que realmente está rolando ali**

Quando você roda:

```
docker run -d -p 8080:80 nginx
```

Você está dizendo *basicamente* o seguinte:

> “Ei Docker, expõe a porta **80** que está **dentro do container** na porta **8080 do meu computador**.”

Ou seja:
**porta 80 = porta onde o NGINX roda internamente**
**porta 8080 = porta pela qual *você*, no seu host, acessa esse NGINX**

---

### **Por que isso existe?**

Porque containers são isolados.
Eles não ficam automaticamente acessíveis pelo seu computador.

Dentro do container:

* o NGINX está servindo na porta **80**
* mas essa porta só vive **lá dentro**, inacessível de fora

O `-p` abre um “tunelzinho”:

```
porta-da-sua-máquina -> porta-do-container
```

Imagine que o container é uma casa e a porta 80 é a porta da frente.
Só que essa casa mora dentro de um condomínio fechado (o Docker).
Você não consegue bater nessa porta diretamente.
O `-p` funciona como:

* criar um portão no condomínio (ex: 8080)
* e redirecionar esse portão pra porta da casa (80)

Assim, quando você acessa:

```
http://localhost:8080
```

você está, na verdade, acessando a porta 80 lá dentro do container.

---

```
-p <porta-do-host>:<porta-do-container>
```

* **porta no host** → como sua máquina acessa
* **porta no container** → onde o serviço realmente roda

---

### 1. A porta do container tem que ser a porta que o serviço realmente usa

NGINX = 80
Postgres = 5432
Redis = 6379
etc.

### 2. A porta do host pode ser qualquer porta livre

Se 8080 já estiver ocupada?
Troca:

```
docker run -d -p 9000:80 nginx
```

Agora entra pelo `localhost:9000`.

### 3. Você pode rodar vários containers iguais mudando só a porta externa

```
docker run -d -p 8080:80 nginx
docker run -d -p 8081:80 nginx
docker run -d -p 8082:80 nginx
```

Cada um acessado por uma porta diferente do host.

---

### **Quando o `-p` é obrigatório?**

Sempre que você precisa que o container seja acessível **de fora**.
Aplicação web → sim
API → sim
Banco de dados → quase sempre sim
Processo interno isolado → não

---

## "Qual porta meu serviço usa?"

Se você não sabe qual porta um container usa **internamente**, basta rodar:

```
docker inspect <imagem-ou-container>
```

Na saída, procure pela seção:

```
"ExposedPorts": {
    "80/tcp": {}
}
```

Isso significa que **a aplicação dentro do container escuta na porta 80**.
A partir disso, você decide como mapear para fora:

```
docker run -p 8080:80 imagem
```

Ou seja: você acessa pelo **8080** (ou 9000, ou qualquer porta do seu interesse), o serviço roda no container na **80**.


