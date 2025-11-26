# Funcionamento Interno do Docker

Docker funciona aproveitando recursos nativos do kernel Linux para criar isolamento leve — sem precisar simular um sistema operacional inteiro, como faria uma VM. 

A base disso são os **namespaces**, que separam tudo o que o container enxerga: processos, sistema de arquivos, rede, usuários, pontos de montagem. Quando você roda um container, o Docker cria um conjunto próprio de namespaces para ele, garantindo que os processos dentro dele só vejam aquilo que pertence ao container, mesmo compartilhando o mesmo kernel com o host. Ele também usa **cgroups** para controlar quanto de CPU, memória e I/O cada container pode usar. 

O resultado é um ambiente isolado, consistente e extremamente leve, porque não há virtualização pesada — apenas isolamento inteligente feito pelo próprio kernel.

---

## Detalhamento

---

### O que é o **kernel Linux**

Pensa no kernel como **o núcleo do sistema operacional** — o “chefe” que fala diretamente com o hardware.
Ele decide:

– quem usa CPU
– quem usa memória
– como acessar disco
– como funciona rede
– como criar e gerenciar processos

O Ubuntu, Debian, Fedora, etc., são “roupas” diferentes em cima do mesmo kernel.
O kernel é a parte que realmente controla a máquina.

---

### O que é um **namespace**

Um **namespace** é uma funcionalidade do kernel Linux que cria uma “visão filtrada” do sistema para um processo.

É como se o kernel dissesse:

“Esse processo aqui não precisa enxergar tudo, só isso.”

Cada namespace isola um aspecto diferente:

– **PID namespace:** isola processos
→ dentro do container, ele só vê os próprios processos, não os do host.

– **NET namespace:** isola rede
→ cada container tem sua interface de rede separada.

– **MNT namespace:** isola o sistema de arquivos
→ cada container vê seu próprio root (`/`), mesmo que esteja mapeado de outro lugar.

– **UTS namespace:** isola hostname
→ container pode ter seu próprio nome de máquina.

– **USER namespace:** isola usuários
→ um “root” dentro do container não é root no host.

Ou seja: um namespace cria um **mini-mundo particular** para aquele conjunto de processos.
É isso que faz o container parecer um sistema independente, mesmo sendo só um processo isolado.

---

### Como isso se relaciona com Docker

Docker não inventou contêineres — ele usa recursos do kernel Linux.

Quando você executa:

```
docker run ...
```

O Docker Daemon faz o seguinte:

1. Cria um processo
2. Coloca esse processo dentro de vários namespaces
3. Aplica limites de uso de recursos (via **cgroups**)
4. Monta um filesystem isolado (imagem do Docker)
5. Conecta numa rede isolada

Resultado:
Esse processo passa a viver num universo próprio, parecendo uma máquina separada — mesmo rodando dentro do mesmo kernel do host.

---
