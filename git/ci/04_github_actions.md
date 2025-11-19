# Configuração Pipeline CI via GitHub Actions II

---

### **1. GitHub Actions é a “fábrica de pipelines” do GitHub**

No mundo DevOps, a CI é feita por uma ferramenta que:

* detecta eventos (push, PR, tag…)
* roda scripts automatizados
* verifica o estado do código
* impede/ou libera merges

No GitHub, essa ferramenta é o **GitHub Actions**.

Ele funciona através de arquivos YAML que você coloca dentro de:

```
.github/workflows/
```

Cada arquivo dentro dessa pasta é um **workflow**, e cada workflow define **um pipeline**.

---

### **2. O que é um workflow no GitHub Actions?**

Pensa no workflow como:

> “Quando acontecer X, execute Y.”

Exemplo clássico:

* Quando alguém fizer push
* Quando alguém abrir PR
* Quando alguém criar uma tag
* Quando rodar manualmente pelo botão “Run workflow”

O workflow descreve:

* **quando** deve rodar (`on:`)
* **em qual ambiente** (`runs-on:`)
* **quais passos** devem ser executados (`steps:`)

---

### **3. E o pipeline? Onde entra?**

No GitHub Actions, o pipeline é simplesmente a **sequência de passos** dentro do workflow.

Quer ver a relação?

* **Workflow** = a receita.
* **Pipeline** = a execução dessa receita passo a passo.

Um workflow pode ter **1 pipeline** ou **vários jobs**, que podem rodar:

* em paralelo
* em ordem específica
* com condições de dependência

Mas pra começar, pensa assim:

> Workflow é o arquivo, pipeline é o que acontece quando ele roda.

---

### **4. Como isso se traduz na integração contínua (CI)?**

Na prática, CI com Actions significa:

1. O dev faz um push ou abre uma PR.
2. GitHub Actions dispara automaticamente.
3. O pipeline faz:

   * checkout do código
   * instala dependências
   * roda testes
   * roda build
   * faz lint
   * valida qualidade
4. Se tudo passar → PR ganha check verde.
5. Se algo falhar → PR fica bloqueada até corrigir.

Ou seja, **CI = rodar tudo automaticamente em cima de cada mudança enviada ao repositório.**

E tudo isso é controlado por um workflow do GitHub Actions.

---

### **Exemplo real, limpo, padrão de mercado (Java + Maven)**

Crie um arquivo:

```
.github/workflows/ci.yml
```

E coloque:

```yaml
name: CI

on:
  pull_request:
  push:
    branches:
      - main
      - develop

jobs:
  build-and-test:

    runs-on: ubuntu-latest

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Configurar Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17

      - name: Build com Maven
        run: mvn -B clean install

      - name: Rodar testes
        run: mvn test
```

Pronto.

Esse arquivo **criou sua integração contínua**.

O GitHub Actions agora:

* roda sempre que você faz push
* roda sempre que você abre PR
* monta o ambiente
* instala o Java
* executa build
* roda testes
* e bloqueia merges se algo falhar

Simples. Poderoso. Profissional.

---

### **6. O que cada bloco realmente significa?**

#### **`name:`**

Nome bonitinho que aparece no GitHub.

#### **`on:`**

Eventos que disparam o workflow.
Push, PR, tags, cron, manual…

#### **`jobs:`**

Cada job é um pipeline independente.

#### **`runs-on:`**

Ambiente onde o pipeline vai rodar (Ubuntu, Windows, Mac).

#### **`steps:`**

A sequência de comandos/passo-a-passo da CI.

#### **`uses:`**

Ação pronta de terceiros (bibliotecas da própria Actions).

#### **`run:`**

Comandos que você escreveria no terminal.

---

### **7. Como isso fecha com CI e DevOps?**

Com Actions + workflows você ganha:

* verificação automática em cada PR
* builds consistentes num ambiente limpo
* testes automatizados acionados sempre
* segurança contra regressão
* proteção da branch `main`
* confiança pra fazer deploy contínuo (CD)

Esse é exatamente o comportamento esperado em times profissionais.

---

### **8. Como isso se encaixa no seu workflow de GitHub?**

Fica assim:

1. Você cria issue
2. Cria branch
3. Implementa feature + testes
4. Faz push
5. Workflow (CI) roda automaticamente
6. Se verde → PR liberada
7. Se vermelho → você ajusta
8. Merge com squash
9. CI roda de novo na main (garantia final)

A máquina valida tudo pra você.
Esse é o poder da integração contínua.

---

## Construindo Pipeline

Um pipeline de CI no GitHub Actions é sempre composto de **4 blocos mentais**:

1. **Gatilhos**
2. **Ambiente**
3. **Ações** (steps)
4. **Ordem / Condições**

---

### 1. **GATILHOS (“quando isso vai rodar?”)**

Esse é sempre o primeiro bloco que você cria:

```yaml
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
```

Perguntas pra você mesma:

* Quero rodar só em PR?
* Quero rodar quando faço push?
* Em quais branches?
* Quero rodar em tags?
* Quero rodar manual (workflow_dispatch)?
* Quero rodar automático (cron)?

Se você entender isso, você já controla “o quando”.

---

### 2. **AMBIENTE (“onde isso vai rodar?”)**

Você decide o “sistema operacional” da máquina virtual do Actions:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

Alternativas:

* `ubuntu-latest` (padrão, mais leve, mais rápido)
* `windows-latest`
* `macos-latest`

Quase sempre: **Ubuntu**.

---

### 3. **AÇÕES / STEPS (“o que vai acontecer?”)**

Esse é o coração do pipeline. Aqui você monta tudo que quiser.

O segredo é:
**cada step é um comando ou uma ação encapsulada.**

Você pensa assim:

**Preciso baixar o código?**

```yaml
- uses: actions/checkout@v4
```

**Preciso de Java?**

```yaml
- uses: actions/setup-java@v4
  with:
    distribution: temurin
    java-version: 17
```

**Preciso rodar Maven?**

```yaml
- run: mvn -B clean install
```

**Preciso rodar testes?**

```yaml
- run: mvn test
```

**Preciso fazer cache?**

```yaml
- uses: actions/cache@v4
  with:
    path: ~/.m2
    key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
```

**Preciso enviar relatório de cobertura?**

```yaml
- run: mvn jacoco:report
```

**Preciso enviar o relatório para o Sonar?**

```yaml
- uses: sonarsource/sonarcloud-github-action@v2
```

Você vai combinando steps até formar o pipeline ideal pra você.

---

### 4. **ORDEM E CONDIÇÕES (“isso roda antes daquilo?”)**

Você pode colocar vários jobs e decidir dependências:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps: [...]

  test:
    needs: build
    runs-on: ubuntu-latest
    steps: [...]
```

Assim o job `test` só roda **depois** do build.

Dá pra colocar condições:

```yaml
if: github.event_name == 'pull_request'
```

Ou:

```yaml
if: runner.os == 'Linux'
```

Isso faz parte de pipelines mais avançados.

---

Sempre pense na ordem:

1. **Quando isso deve rodar?**
2. **Onde vai rodar?**
3. **O que quero que aconteça?**
4. **Em que ordem/condição?**

Se você sabe essas 4 respostas, você monta qualquer pipeline.

---

## Granularidade de Pipelines com `mvn` 

* `mvn clean install` já executa:

  * limpeza
  * compilação
  * testes (`test`)
  * empacotamento (`package`)
  * instalação no repositório local (`install`)

Ou seja: **o ciclo completo**.

Mas quando alguém está **montando o próprio pipeline**, é importante entender que você *pode* separar estágios, e isso é bem comum em pipelines reais.

### Pipeline 1 (mais comum em projetos pequenos)

```
mvn -B clean install
```

Acabou. Já testou, já buildou tudo.

### Pipeline 2 (mais profissional / granular / corporações)

Separar etapas te dá controle e clareza:

**Etapa 1 — Limpar + Compilar**

```
mvn -B -q clean compile
```

(Isso falha rápido se tiver erro de compilação.)

**Etapa 2 — Testes (sem build ainda)**

```
mvn -B -q test
```

(Aqui você evita empacotar/buildar uma aplicação com testes quebrando.)

**Etapa 3 — Build**

```
mvn -B -q package
```

**Etapa 4 — Instalar/Deploy/Upload**

```
mvn -B -q install
```

ou
deploy automático pra algum repositório/artefato.

Isso deixa o pipeline mais modular, mais fácil de observar falhas e mais eficiente.

```yml
name: CI

on:
  pull_request:
    branches: [ "main" ]
  push:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Setup Java
        uses: actions/setup-java@v3
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Clean + Compile
        run: mvn -B -q clean compile

      - name: Run Tests
        run: mvn -B -q test

      - name: Package
        run: mvn -B -q package

      - name: Verify
        run: mvn -B -q verify
```

---

**`mvn -B clean install`**

O `-B` significa:

**Batch Mode** (modo silencioso / não interativo)

É usado porque o GitHub Actions NÃO tem interface pra “perguntar coisas” ao Maven.

Sem `-B`, em algumas situações Maven pode:

* esperar input
* travar o pipeline
* gerar output interativo poluído

Com `-B`, ele:

* não pede interação
* produz logs limpos
* roda mais rápido
* não trava por falta de input

É prática padrão da comunidade.

---

**`mvn -B -q test`**

O `-q` significa quiet mode.

Em CI isso é bem valorizado porque:

* o log fica mais limpo

* erros aparecem com mais clareza

* você não tem 300 linhas de coisa irrelevante passando na tela

* facilita debug em pipelines longas

Mas cuidado: não use quiet demais quando está aprendendo.
Use `-q` quando você quer logs mais “cirúrgicos”.

---