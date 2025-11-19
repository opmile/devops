# Usando o `mvn`

Usar `mvn` no terminal não é um ritual místico dev-friendly. É simplesmente uma habilidade que vai te deixar mais solta, mais rápida e mais independente da IDE. E isso importa especialmente porque, à medida que você entra no mundo Java sério (principalmente quando bate na porta do Spring), vai perceber que **todas as ferramentas, pipelines, servidores, contêineres e automações giram em torno do Maven no terminal**, não da IDE.

---

## Por que usar `mvn` no terminal é tão relevante

### 1. **Você aprende Maven de verdade**

IDE é confortável, mas esconde o que está acontecendo.
No terminal, você passa a entender:

* como o projeto é construído,
* como são resolvidas as dependências,
* como funciona o ciclo de vida (`clean`, `compile`, `package`, `test`…),
* como rodar testes, plugins, perfis, etc.

Isso é o tipo de conhecimento que diferencia alguém que “usa Java” de alguém que **entende Java**.

### 2. **Ambientes profissionais não têm IDE pra te salvar**

Pipelines de CI, servidores, contêineres Docker, execução em SSH, scripts…
Tudo isso espera que você rode:

```
mvn clean install
mvn package
mvn test
```

Se você sabe operar Maven só clicando num botão da IDE, você se complica na hora que o mundo real aparece.

### 3. **Você fica livre**

Quer testar uma build rápido? Quer limpar dependências? Quer rodar só um módulo? Quer criar um projeto novo?
Pelo terminal, você resolve em segundos, sem depender de interface gráfica e sem abrir um projeto inteiro no editor.

### 4. **IDE quebra. Terminal não.**

Seu IntelliJ pode pirar, indexar pra sempre, travar, ficar bugado.
O Maven pelo terminal simplesmente roda.

E rodando pelo terminal, você ainda consegue entender se o problema está no projeto ou na ferramenta.

---

## O que você precisa aprender pra começar a usar `mvn` com naturalidade

Vou te guiar no caminho mínimo viável, sem enrolação.

---

# **PASSO 1 — Garantir que o Maven está instalado**

No macOS (que é o seu caso), instale via Homebrew:

```
brew install maven
```

Verifique:

```
mvn -v
```

Se isso funcionar, você tá pronta.

---

# **PASSO 2 — Abrir o terminal NA RAIZ do projeto**

Isso é importante.

Entre na pasta do seu projeto Java:

```
cd /caminho/do/seu/projeto
```

A raiz é onde fica o `pom.xml`.

---

# **PASSO 3 — Aprender os 5 comandos que você vai usar 90% das vezes**

Esses aqui são ouro:

### **1. Limpar build**

```
mvn clean
```

### **2. Compilar**

```
mvn compile
```

### **3. Rodar testes**

```
mvn test
```

### **4. Empacotar (gera o .jar)**

```
mvn package
```

### **5. Build completo + instalar no repositório local**

```
mvn clean install
```

Esse é o que você vê em pipelines de integração contínua.

---

# **PASSO 4 — Abrir o leque com o tempo**

Sem pressa. Mas você vai querer aprender:

### Rodar aplicação Spring Boot:

```
mvn spring-boot:run
```

### Rodar um único teste:

```
mvn -Dtest=NomeDoTeste test
```

### Usar perfis:

```
mvn clean package -Pdev
```

### Criar projetos novos (adapta mais tarde):

```
mvn archetype:generate ...
```

---


