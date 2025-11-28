# **Por que não basta rodar imagens prontas?**

Rodar imagens existentes funciona **só quando o que você precisa já existe** e **não precisa de nenhuma customização**.

Exemplos que funcionam sem Dockerfile:

* `docker run postgres`
* `docker run redis`
* `docker run nginx`

Essas imagens já vêm prontas, configuradas e opinadas.
Mas no momento em que você precisa colocar **o seu código** dentro do container… acabou a festa.

---

## **Quando o Dockerfile é realmente necessário**

O Dockerfile é obrigatório quando você precisa:

### **1. Empacotar a SUA aplicação**

Se você tem:

* um projeto Java
* um jar
* um Node app
* um Python script

Nada disso entra automaticamente em uma imagem já existente.
Você precisa criar **sua própria imagem** que:

* copie seu código
* instale dependências
* configure runtime
* defina como a aplicação roda

Sem Dockerfile você *não tem como criar essa imagem*.

---

### **2. Customizar comportamento de uma imagem pronta**

Às vezes a imagem oficial não é suficiente.
Você quer:

* instalar libs extras
* copiar arquivos de configuração
* ajustar permissões
* adicionar ferramentas de debug
* alterar entrypoint

Isso é impossível usando só `docker run`.
Para isso você cria uma imagem derivada:

```
FROM nginx
COPY meu-nginx.conf /etc/nginx/nginx.conf
```

Ou seja: **customização = Dockerfile obrigatório**.

---

### **3. Garantir reprodutibilidade**

Rodar `docker run` não cria ambientes fixos.

Exemplo:

“roda esta app java com jdk 17, copia o jar, expõe porta 8080, instala X, Y, Z…”

Sem Dockerfile, isso ficaria tudo na sua cabeça ou em documentos.
Com Dockerfile: **a receita inteira está automatizada e reprodutível**.

---

### **4. Automatizar builds em CI/CD**

Pipelines (GitHub Actions, GitLab CI, Jenkins) não mexem manualmente em containers.
Eles precisam de:

* um Dockerfile
* que diga exatamente como construir a imagem da sua aplicação

Sem Dockerfile → **não tem imagem automatizável**.

---

Você **usa imagens prontas** quando precisa de serviços genéricos (Postgres, Redis, Nginx).

Você **cria Dockerfiles** quando precisa:

* empacotar *seu código*
* customizar uma imagem
* definir como a aplicação deve rodar
* garantir reprodutibilidade e CI/CD
* automatizar a criação da sua imagem

Sem Dockerfile = sem imagem própria
Sem imagem própria = sua aplicação não roda em container.
