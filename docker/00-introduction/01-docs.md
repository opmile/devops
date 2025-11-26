# O que é Docker?

> *Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker's methodologies for shipping, testing, and deploying code, you can significantly reduce the delay between writing code and running it in production.*

Docker te ajuda a empacotar sua aplicação de um jeito que ela rode igual em qualquer lugar, sem depender das “gambiarras” do ambiente onde ela está rodando.

---

## O que é “infraestrutura” afinal?

Infraestrutura, no contexto de software, é todo o **ambiente onde sua aplicação vive**.
Pensa em:

* Sistema operacional
* Versão do Linux
* Dependências instaladas no sistema
* Banco de dados
* Servidor web
* Configurações de rede
* Permissões
* Hardware (até certo ponto)

Ou seja: tudo aquilo que não é o código da sua aplicação em si, mas **tudo o que ela precisa pra funcionar**.

#### **Dependências do sistema**

São coisas que **o SO precisa ter instalado** pro seu app conseguir rodar:

* Versão específica do **Java/JDK**
* **Python** instalado no sistema
* **Bibliotecas nativas** (ex: `libssl`, `libxml2`, `libpq`, `zlib`)
* **Compiladores** (ex: `gcc`, `make`)
* **Serviços externos** instalados na máquina (ex: Redis, PostgreSQL, Nginx)
* Ferramentas de CLI (ex: `curl`, `git`, `wget`)
* Configurações de sistema (PATH, permissões, timezone)

Essas são dependências **do ambiente**, não do seu código.

---

* Dependências do `pom.xml` (ex: Spring Web, Jackson, Lombok)
  → São **dependências da aplicação**.

* Dependências do sistema (ex: JDK, libs nativas)
  → São **dependências do ambiente**.

Com Docker, você empacota **as duas coisas** juntas:

* Coloca o JDK certo dentro da imagem (dependência do sistema)
* Baixa as libs do `pom.xml` no build (dependência da aplicação)

E aí tudo fica rodando dentro de um contêiner isolado.

Sem Docker, sua aplicação depende do que está instalado na máquina.
Com Docker, você empacota isso.

---

## O que significa “separar a infraestrutura da sua aplicação”?

Significa **não depender mais do ambiente onde você está rodando**.
Sua aplicação não fica mais acoplada ao “Linux da empresa X”, ao “Ubuntu da sua máquina”, ou “àquela versão específica do Java que só funciona no servidor do fulano”.

Tudo isso vira um pacote fechado.

### Um exemplo prático ridiculamente comum

Você tem uma API em Java que depende de:

* JDK 21
* Maven
* Biblioteca nativa X
* Uma configuração de PATH específica
* Uma versão exata do sistema operacional

Sem Docker, pra rodar essa API em produção, o servidor precisa ter tudo isso instalado e configurado. E se o dev instala a JDK 21, mas no servidor tem só JDK 17? Já era — dá erro, quebra build, vira caos.

**Com Docker**, você faz:

* Cria um arquivo `Dockerfile` dizendo:
  “Quero uma imagem com Linux X, JDK 21, Maven, dependências Y e roda esse comando”.

Isso vira um contêiner.
O contêiner **sempre sobe exatamente igual**, independente da máquina.

Roda igual no seu notebook, no servidor, na nuvem, no notebook velho do seu tio, no Windows, no Linux, no Mac… tanto faz.

---

Sem Docker:

```
Máquina do dev (Ubuntu)
 ├── JDK 21
 ├── Maven
 ├── Variáveis ambientes do dev
 ├── Configs malucas
 └── App

Servidor (Amazon Linux)
 ├── JDK 17
 ├── Maven velho
 ├── Outras configs
 └── App (quebra porque o ambiente é outro)
```

Com Docker:

```
Contêiner (independente da máquina)
 ├── Linux próprio
 ├── JDK 21
 ├── Maven correto
 ├── Configs da própria imagem
 └── App

Máquina hospedeira
 └── Só roda Docker
```

O host não precisa ter JDK, Maven, nada.
A infra deixa de ser “o ambiente da máquina” e passa a ser “o ambiente dentro do contêiner”.

---

## Exemplo concreto do dia a dia

### Antes de Docker

Você manda um projeto para outra pessoa do time e diz:

> “Ah, pra rodar é tranquilo… só instalar o JDK 21, configurar o Maven 3.9, criar o PostgreSQL 15 na sua máquina, baixar o pgAdmin, criar o banco com o mesmo nome do meu, colocar as variáveis no `application.properties`, rodar o script SQL que está na pasta `/db/init`, instalar o Redis, e por fim rodar um `mvn clean install`. Funciona suave!”

E é ai que a pessoa perde a manhã inteira só pra tentar rodar o projeto e as tragédias aparecem:

* A máquina da pessoa usa JDK 17, e metade das features não compila.

* O PostgreSQL local dela já está rodando na porta 5432, ocupado por outro serviço.

* Ela está no Windows, você no Mac/Linux.

* O Redis simplesmente não inicia porque depende de outra DLL.

* O script SQL falha porque faltou encoding UTF-8.

* As libs nativas diferem entre SOs.

### Com Docker

Você manda:

```
docker compose up
```

E tudo aparece magicamente:

* O backend
* O banco de dados
* O frontend
* Subindo juntos, com as versões exatas que você definiu

Não existe “na minha máquina funciona”.

---

## Então, no fim das contas…

**Infraestrutura = tudo que sua aplicação precisa pra viver (ambiente, sistema, ferramentas, versões corretas).**
**Separar da aplicação = colocar tudo isso dentro de contêineres, pra que sua aplicação não dependa da máquina hospedeira.**

No mundo real, isso reduz atrito, acelera deploy, facilita testes, e elimina aquele drama infantil de “funciona no meu PC, juro”.


