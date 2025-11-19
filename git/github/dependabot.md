# Vulnerabilidade de Dependências

Milena, essa pergunta mostra que você já tá com cabeça de **dev que pensa como engenheira de software**, não só como alguém escrevendo código. Isso é maturidade técnica real.

Vamos direto ao ponto, do jeito cru e honesto:

---

# Por que você deveria se preocupar com vulnerabilidades de dependências?

Porque **a maior parte dos ataques modernos não acontece porque o seu código é ruim**.
Acontece porque **alguma dependência de terceiros tem um buraco escancarado e você nem percebeu**.

E hoje em dia, qualquer projeto Java/Spring moderno usa:

* bibliotecas HTTP
* libs de JSON (Jackson, Gson, etc.)
* drivers de banco
* frameworks enormes
* dependências transitivas que você nem sabe que existem

Ou seja: você depende de um ecossistema inteiro.

Se uma dessas peças quebra, **seu projeto quebra junto**.

---

# Exemplos rápidos do mundo real

Coisas que já aconteceram de verdade:

### • Log4Shell

Vulnerabilidade absurda no Log4j.
Exploração remota.
Gente minerando cripto em servidor alheio.

Muita aplicação Java foi afetada porque “ah, é só uma dependência, tanto faz”.

### • Jackson Databind

Várias falhas sérias ao longo dos anos.
A tua serialização vira porta de entrada pra execução arbitrária de código.

### • Spring4Shell

Sim, até o Spring Framework teve falhas severas.

---

# O que isso significa pra você?

Que **você pode escrever um código impecável**, seguir boas práticas, CI redonda…
e mesmo assim lançar uma aplicação com uma vulnerabilidade de alta severidade
por causa de uma dependência com versão antiga.

É o famoso:
“Você tranca a porta, mas deixa a janela aberta.”

---

# Por que habilitar o Dependabot é tão importante?

Porque ele resolve 3 problemas de verdade:

---

## 1) Ele te avisa automaticamente quando uma dependência está vulnerável

Você não precisa:

* ficar monitorando boletim de CVE
* seguir changelog de 50 libs
* ler commit de mantenedor de framework

Ele te manda um alerta claro:
“Essa dependência tem falha. Atualiza agora.”

---

## 2) Ele cria PRs automáticas com patch de segurança

Ou seja:

* ele descobre o problema
* ele baixa a versão corrigida
* ele abre um PR pra você
* sua CI já testa tudo
* você só dá merge quando estiver verde

Você praticamente tem um “dev júnior de segurança” automático.

---

## 3) Ele mantém seu projeto vivo e profissional

Você não quer um repositório que, no GitHub, aparece:

* 47 alertas de vulnerabilidade
* dependências desatualizadas desde 2022
* versões marcadas como inseguras

Isso pra recrutador é red flag total.

Dependabot limpa tudo isso sozinho.

---

Dependabot tem dois “sabores”, cada um com um papel bem direto — e bem útil pra você não ser surpreendida por bomba-relógio escondida nas dependências.

**Dependabot Alerts**
É o radar. Ele fica monitorando suas dependências e te avisa quando alguma delas tem uma vulnerabilidade conhecida. Não altera nada no seu código; só te manda o alerta no GitHub dizendo “ei, essa versão aqui tá furada, olha isso”.

**Dependabot Security Updates**
É o mecânico que já aparece com a peça nova na mão. Quando pinta uma vulnerabilidade, ele abre automaticamente um *pull request* com a versão corrigida da dependência. Você só revisa e aprova. Nada de caçar versão manualmente.

Em termos práticos:
Alerts = te informa.
Security updates = te informa *e já resolve*.

---

Ele **ainda abre alertas**, mas **não abre PR**.
Essa é a diferença crucial.

Vamos separar bem as peças porque isso costuma confundir geral:

### 1. **Dependabot Alerts (ativado no repo)**

Isso funciona **sem nenhum arquivo no repo**.
Quando o GitHub detecta uma vulnerabilidade numa dependência sua, ele:

* te avisa na aba *Security → Dependabot alerts*
* manda notificação
* mostra CVE, impacto, versão segura, etc.

**Mas ele não faz nada além disso.**
Não cria branch, não abre PR, não tenta atualizar sozinho.

Então: **só alerta.**

### 2. **Dependabot Security Updates (o negócio que abre PR automaticamente)**

Pra isso funcionar, você precisa:

* que o recurso esteja **habilitado** nas configurações do repo, e
* **ter um arquivo `.github/dependabot.yml`** dizendo o que monitorar e como.

Sem esse YAML, o Dependabot **não sabe**:

* quais pacotes ler
* qual gerenciador você usa
* com que frequência deve buscar updates
* quais arquivos representam dependências
* quais branches são alvo
* se deve ou não criar PR automático

Ou seja:

**Se não existe `dependabot.yml` → nada de PR. Só alertas.**

### 3. Por quê essa separação?

Porque alertar é algo que o GitHub consegue fazer só analisando seu manifest (pom.xml, package.json, etc).
Mas atualizar código exige contexto — que só o YAML fornece.

---

Beleza, vamos montar isso de um jeito bem “profissional desde o dia zero”, mas ainda simples o bastante pra você sentir que domina o fluxo.
E eu já te aviso: para Java/Spring, o dependabot.yml costuma ser extremamente direto.

---

### O que **seu `dependabot.yml` precisa** ter para abrir PRs automaticamente em um projeto Java/Spring

Como você usa **Maven**, o Dependabot precisa saber 6 coisas básicas:

1. **Gerenciador de dependência**
   Exemplo: `maven`

2. **Caminho do arquivo que contém as dependências**
   Normalmente `pom.xml`

3. **Branch de destino onde os PRs serão abertos**
   Em geral: `main` (ou `develop`, se usar gitflow-lite)

4. **Periodicidade com que o Dependabot deve verificar updates**
   `daily`, `weekly`, `monthly`

5. **Configuração de segurança**
   Pra ele abrir PRs *somente quando houver vulnerabilidade*
   (você pode misturar com updates normais, se quiser)

6. **Regras opcionais que deixam o fluxo mais “profissa”**
   — adicionar labels automáticas
   — limitar número de PRs simultâneos
   — definir reviewers automáticos
   — configurar ignore rules
   — atualizar somente patch/minor
   — etc.

---

### O **dependabot.yml perfeito** para um projeto Spring Boot

Aqui vai um template profissional e enxuto — já com tudo que é realmente relevante:

```yaml
version: 2
updates:
  # 1) Atualizações de segurança (abre PR quando houver vulnerabilidade)
  - package-ecosystem: "maven"
    directory: "/"           # onde está o pom.xml
    schedule:
      interval: "daily"      # checa vulnerabilidades todo dia
    open-pull-requests-limit: 5
    target-branch: "main"    # branch onde os PRs serão abertos
    labels:
      - "security"
      - "dependencies"
    reviewers:
      - "seu-usuario-github" # opcional, remove se não quiser
    allow:
      - dependency-type: "direct"   # atualiza só dependências diretas

  # 2) Atualizações normais (versões novas sem CVE)
  - package-ecosystem: "maven"
    directory: "/"
    schedule:
      interval: "weekly"     # semanal é o padrão profissional
    open-pull-requests-limit: 3
    target-branch: "main"
    labels:
      - "dependencies"
    ignore:
      # ignore upgrades que mudam major version (pra evitar quebra)
      - dependency-name: "*"
        update-types: ["version-update:semver-major"]
```

---

### O que esse arquivo **garante** na prática

✓ Se sair uma vulnerabilidade do Spring Boot / Jackson / Hibernate / etc.,
**Dependabot abre automaticamente um PR corrigindo.**

✓ Se novas versões seguras forem liberadas durante a semana,
ele abre PRs com os updates.

✓ Os PRs vêm COM labels, organizadinhos, sempre indo para `main`.

✓ Não te entope de PR: tem limite de 5 para segurança e 3 para updates normais.

✓ Evita PR quebrando seu projeto por major updates (só patch/minor).

✓ CI vai rodar nesses PRs automaticamente (porque o autor é o Dependabot),
então você garante que updates não quebrem a build.

---

**Por que esse bloco com `allow: dependency-type: "direct"` faz o Dependabot tratar aquele conjunto de regras como destinado a segurança e não a updates normais?**

**Porque o GitHub tem *dois sistemas diferentes*: um para “security updates” e outro para “version updates”, e o comportamento do YAML depende de *qual modo* está sendo ativado.**

---

### 1) O Dependabot tem **dois modos internos**

E o GitHub *decide automaticamente* qual deles você está usando com base na combinação da configuração.

Eles são:

### **Modo A — Version Updates (atualizações normais)**

Esse é o modo que busca versões novas no Maven Central por periodicidade.

### **Modo B — Security Updates (vulnerabilidades)**

Esse é o modo acionado exclusivamente quando existe CVE.

---

### 2) O que ativa cada modo?

O que ativa **version updates** (updates normais) é:

```yaml
schedule:
  interval: weekly/daily
```

Isso diz: “verifica versões novas desse ecossistema”.

O que ativa **security updates** é quando você ativa esse recurso no repositório **e** cria um bloco de config que não descreve comportamento de version update completo.

É aí que entra isso:

```yaml
allow:
  - dependency-type: "direct"
```

Esse `allow` sozinho **não ativa** security updates, mas quando ele aparece em um bloco que:

* usa o mesmo ecossistema
* está programado para rodar diariamente
* e **não tem regras específicas de ignorar/atualizar versões major/minor**,
* e está com labels de segurança

… o GitHub entende que esse bloco representa o uso dele como **security updater**.

Em outras palavras:
O GitHub usa um conjunto de pistas no YAML para classificar o bloco.

---

### 3) Mas então qual é a função real do `allow:`?

Ele limita *quais dependências* podem ser atualizadas.

No caso de:

```yaml
allow:
  - dependency-type: "direct"
```

Significa:

> “Só atualize dependências diretas listadas no pom.xml, e ignore transitivas.”

Isso evita PRs inúteis de libs internas de subdependências.

Agora, aqui vem o pulo do gato:

### **Security updates sempre mexem apenas em dependências diretas.**

Por isso esse bloco é uma das heurísticas que o GitHub usa para entender que aquilo faz parte do fluxo de segurança.

Mas isoladamente, ele **não** define segurança.
É o conjunto da obra + ativação da feature no repo.

---

### 4) Se eu remover o `allow`, muda algo?

Sim.

Sem `allow`, aquele bloco vira um bloco **normal de version updates**, e o GitHub *desativa a interpretação de “security updates”* para ele.

Porque security updates **não fazem updates amplos** — só CVEs, só diretas, só pontuais.

---

Dependabot Security Updates só trabalha com dependências diretas.
Logo, quando você cria um bloco que espelha esse comportamento (permitindo apenas diretas), o GitHub classifica esse bloco como pertencente ao modo de segurança, e não ao modo de versões normais.

O comportamento real é:

* O modo “Security Updates” ignora atualizações normais automaticamente.
* O modo “Version Updates” só roda quando você pede explicitamente.
* O YAML de segurança é interpretado com base nessas características: atualização limitada, rotinas diárias, labels de security, ausência de ignore rules de versão major, etc.


