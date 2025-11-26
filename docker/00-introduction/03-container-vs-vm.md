# Antes do Docker

> *Docker is lightweight and fast. It provides a viable, cost-effective alternative to hypervisor-based virtual machines, so you can use more of your server capacity to achieve your business goals. Docker is perfect for high density environments and for small and medium deployments where you need to do more with fewer resources.*

---

## 1. Como o mundo isolava aplicações

Durante muito tempo, existiam dois jeitos de isolar aplicações:

### **1.1. “Isolamento manual” no próprio sistema operacional**

Era o jeito clássico:

* instalar o runtime (Java, Python, Node)
* instalar libs do sistema
* instalar banco na mesma máquina
* configurar tudo manualmente
* torcer pra outra pessoa conseguir replicar

Isso dava erro porque **ambiente é frágil**.
Atualizou uma lib? Quebrou outra coisa.
Mudou a versão do Java? Quebrou o build.

---

### **1.2. Virtual Machines (VMs)**

Para resolver o caos dos ambientes, veio a ideia de simular uma máquina inteira:

* Você roda um **sistema operacional inteiro dentro de outro**
* Cada app roda numa VM isolada
* VM = SO completo + libs + sua aplicação

Exemplos: VMWare, VirtualBox, Hyper-V.

Isso trouxe segurança e isolamento, mas tinha um problema gigante:

* VM é **pesada**
* Carrega um SO inteiro
* Lenta para subir
* Gasta muita memória
* Difícil de distribuir

Rodar 10 VMs = 10 sistemas operacionais completos.
Imagina rodar isso em produção.

---

## 2. Onde o Docker entra na história

Docker surge com uma ideia muito simples:

> *“E se a gente reutilizar o sistema operacional do host, mas isolar só o que importa?”*

Em vez de simular uma máquina inteira (VM), você só isola:

* o sistema de arquivos
* as dependências
* os processos
* as libs
* a rede

Mas **usa o kernel do host**.

Isso faz Docker ser:

* muito mais leve
* muito mais rápido
* muito mais fácil de distribuir
* escalável sem dor

Um contêiner sobe em **milissegundos**.
Uma VM sobe em **minutos**.

---

## 3. Diferença concreta entre VM e Docker

### **3.1. Virtual Machine**

* Carrega **um SO inteiro**
* Demora pra inicializar
* Consome muita RAM e CPU
* Pesada pra mover e duplicar
* Forte isolamento (bom para segurança extrema)
* Ótima quando você precisa de SO diferente (ex: rodar Linux dentro de Windows)

### **3.2. Docker (contêiner)**

* Usa o mesmo kernel do host
* Sobe instantaneamente
* Consome poucos recursos
* Fácil de replicar e distribuir
* Perfeito para microserviços
* Bom o suficiente para 99% das aplicações modernas

---

## 4. Por que Docker venceu?

Porque oferece três coisas que VMs não entregavam com essa leveza:

### **4.1. Imagens reprodutíveis**

Você descreve tudo num Dockerfile:

* versão do JDK
* libs
* comandos
* user
* portas

E qualquer pessoa, em qualquer máquina, consegue rodar **exatamente o mesmo ambiente**.

Isso matou o problema:

**“funciona na minha máquina”**.

---

### **4.2. Isolamento barato**

Com contêineres, você pode rodar:

* 10 instâncias do seu backend
* 3 instâncias do seu job scheduler
* 1 instância do worker de fila
* 1 Redis
* 1 Postgres
* 1 API gateway

Tudo isso em uma única máquina física sem explodir os recursos.

Fazer isso com VMs seria um massacre de RAM.

---

### **4.3. Cultura DevOps + CI/CD + Kubernetes**

Docker encaixou perfeitamente no que estava surgindo:

* Deploy contínuo
* Microserviços
* Infra como código
* Pipelines automáticos
* Escalabilidade horizontal
* Kubernetes como orquestrador

VMs não acompanham esse ritmo.

---

## 5. “Mas então VMs morreram?”

Não. Elas só têm outro propósito hoje.

### **Quando VMs ainda são usadas?**

* Ambientes que exigem **isolamento máximo** (bancos, fintechs, governo)
* Host de clusters Kubernetes (K8s roda em cima de VMs)
* Rodar SOs diferentes na mesma máquina (Windows host + Linux guest)
* Infra de cloud (AWS, GCP, Azure usam VMs como base dos servidores)
* Testar SOs diferentes
* Executar aplicações legadas que precisam do sistema operacional completo

VM é ótima quando você precisa de **um SO inteiro isolado**.

Docker é ótimo quando você precisa **isolar aplicações**.

---

**Antes de Docker**
Ou você instalava apps direto no SO (caos)
ou usava VMs (pesado, lento).

**Docker mudou o jogo** oferecendo:

* isolamento real
* sem carregar um SO inteiro
* com imagens reprodutíveis
* rodando em milissegundos
* fácil de distribuir
* perfeito para pipelines e microserviços

**VMs ainda têm espaço**, mas não para o tipo de problema que Docker resolve — que é entregar **ambientes leves, rápidos e idênticos**, do dev à produção.

---

Antes do Docker, **era tudo mato** no quesito isolamento leve. Então, pra garantir que uma aplicação rodasse igualzinho em qualquer máquina, você *não tinha escolha*: **usava máquina virtual**. Por quê?

Aqui vai a explicação sucinta, mas mais profunda:

1. **O sistema operacional do host influenciava tudo.**
   Bibliotecas diferentes, versões diferentes, permissões diferentes… qualquer variação quebrava a aplicação. A VM resolvia isso porque trazia **um sistema operacional inteiro junto**, totalmente controlado.

2. **A VM criava um “mundo fechado” perfeito.**
   Mesmo que a máquina física fosse toda zoada, dentro da VM tudo era previsível: mesma distro, mesmos pacotes, mesma configuração. Isso dava **reprodutibilidade real**, mesmo que fosse pesado.

3. **Ambiente de produção e desenvolvimento alinhados.**
   Desenvolvimento rodava Ubuntu 14.04 e produção Ubuntu 12.04? Problema. A VM garantia que todo mundo rodava exatamente **a mesma imagem**. Sim, era lento, mas funcionava.

4. **Isolamento forte do sistema.**
   Não existiam containers populares e leves. Se você queria impedir que uma aplicação interferisse na outra, tinha que usar VM mesmo. O isolamento era **bala**, só que custava RAM, CPU e até sua paciência.

Agora o principal:

**Docker surgiu porque alguém pensou:**
"Por que estou rodando um sistema operacional inteiro só pra isolar uma aplicação?"

Docker reaproveita o kernel do host e isola via namespaces/cgroups. Então ele faz **o mesmo que as VMs faziam em termos de previsibilidade**, só que muito mais rápido e leve.

**Antes do Docker, VMs eram o único caminho para:**
– isolamento real
– previsibilidade
– empacotamento de ambiente
– rodar tudo igual em qualquer lugar

---

* **Imagine o cenário**: existe um serviço em uso do PostgreSQL na porta `5432` em `localhost`. Suponha que eu precise usar rodar outro serviço de banco

Com Docker v*ocê pode rodar **vários Postgres ao mesmo tempo**, cada um mapeando para uma porta diferente no host:

```
docker run -p 5433:5432 postgres
```

A porta *interna* do container (5432) continua igual, mas você escolhe **qual porta do seu localhost** aponta pra ele (5433, 5434, 6000, tanto faz).

Ou seja:

– Porta 5432 já está ocupada?
– Sem crise: mapeia esse novo Postgres pra 5433 e segue o baile.

Com VM seria possível também, mas muito mais burocrático. Docker deixa isso trivial.

---

* **Imagine o cenário**: um time de desenvolvimento padroniza a infraestrutura que desenvolve suas aplicações para o ambiente Linux/Ubutuntu através de uma VM. 

A VM resolve **parte** do problema:
— Padroniza o *sistema operacional*
— Garante que todo mundo está rodando “a mesma máquina base”

Mas ela **não resolve de verdade** o problema de versões de runtime, dependências internas, configurações específicas e tudo o que está *dentro* desse SO padronizado.

Exemplo direto:

Você entrega uma VM Ubuntu padrão pro time. Ótimo.
Mas dentro dela, cada dev pode acabar com:

– JDK 17 instalado
– Outro com JDK 21
– Outro com JDK 17 + variáveis mal configuradas
– Outro instalou Maven manualmente
– Outro mexeu no PATH
– Outro instalou uma lib do sistema a mais

Ou seja: **o SO é igual, mas o ambiente não é**.

A VM resolve a *base*, mas não resolve o *conteúdo*.
E é justamente aí que Docker mata a discussão:

Você descreve o ambiente inteiro num `Dockerfile`:
– JDK específico
– Versão do Maven
– Variáveis
– Libs
– Configs

E congela isso. Todo mundo roda **o mesmo ambiente bit a bit**.

Em VM, o ambiente é *replicado*;
Em Docker, o ambiente é *reproduzido* via receita imutável.

Resumo incisivo:

**VM padroniza a caixa.
Docker padroniza tudo que está dentro da caixa — até o último parafuso.**

