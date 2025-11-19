# GitFlow e Trunk-Based

---

# 1. **GitFlow — organização rígida, ciclos longos, muita cerimônia**

GitFlow é aquele modelo clássico cheio de ramificações.
Falamos de:

* `main` (produção)
* `develop` (linha de desenvolvimento)
* `feature/*` (funcionalidades)
* `release/*` (estabilização pra lançar uma versão)
* `hotfix/*` (correções emergenciais)

**Como funciona?**
Você cria branch pra tudo, faz merge pra develop, depois abre release, estabiliza, mergeia pra main, cria tag, mergeia de volta pra develop… todo um ritual.

**Vantagens:**

* Estrutura rígida = organização clara de versões.
* Bom pra projetos com **lançamentos fechados**, tipo software que gera “versões” oficiais.
* Dá previsibilidade pra times maiores que não querem que a produção vire uma bagunça.

**Desvantagens:**

* Pesado.
* Muita branch.
* Muita manutenção manual.
* Muita defasagem entre o que está sendo desenvolvido e o que está na main.
* Incentiva ciclos maiores, com merges enormes.

**Onde faz sentido usar:**

* Produtos com **releases programados** (tipo times que publicam versão nova toda semana).
* Times que preferem controle rígido e têm pouco deploy contínuo.
* Sistemas legados, com muita burocracia e validações manuais.

**Meu hot take:** GitFlow é um modelo que envelheceu mal. Resolve problemas do passado, quando não existia pipeline, testes automatizados ou deploy contínuo. Hoje ele atrapalha mais do que ajuda em boa parte dos casos.

---

# 2. **Trunk-Based — desenvolvimento contínuo, ciclos curtos, pragmatismo**

Trunk-Based é o oposto filosófico do GitFlow.
Nada de ramificação infinita.
O foco é simples:

* Trabalhar sempre perto da `main` (ou `trunk`).
* Criar branches **curtas**, de vida curta (às vezes horas ou dias).
* Fazer merge cedo e frequentemente.
* Usar feature flags quando precisa esconder funcionalidades inacabadas.

**Vantagens:**

* Integração contínua de verdade.
* Menos conflitos gigantes.
* Pipeline mais simples.
* Deploy contínuo se torna natural.
* O código de produção está sempre próximo do código real do dia a dia.

**Desvantagens:**

* Requer disciplina.
* Exige testes automatizados confiáveis (sem isso, vira suicídio).
* Times júnior ou bagunçados podem quebrar a produção com mais facilidade.

**Onde faz sentido usar:**

* Startups.
* Times ágeis.
* Ambientes de deploy frequente.
* Projetos modernos, que evoluem continuamente.
* Sistemas que confiam muito em CI/CD e testes avançados.

**Meu hot take:** Trunk-Based é o estilo que mais acompanha a realidade atual de desenvolvimento. É o modelo que aposta que seu time escreve código decente e que vocês têm automação suficiente pra pegar erros cedo.

---

# 3. **Comparação direta (clara e sem frescura)**

| Tema                       | GitFlow                                  | Trunk-Based                    |
| -------------------------- | ---------------------------------------- | ------------------------------ |
| Filosofia                  | Controle rígido                          | Entrega contínua               |
| Quantidade de branches     | Alta                                     | Baixa                          |
| Tempo de vida das branches | Longas                                   | Curto (dias ou horas)          |
| Conflitos                  | Grandes e demorados                      | Pequenos e frequentes          |
| Deploy                     | Lento e previsível                       | Frequente e automatizado       |
| Complexidade operacional   | Alta                                     | Baixa                          |
| Times ideais               | Empresas tradicionais, projetos big-bang | Times ágeis, produtos modernos |
| Testes automatizados       | Desejáveis, mas não essenciais           | Obrigatórios                   |

---

Ainda sim que muitos times profissionais não usem GitFlow, pelo menos ao pé da letra, é considerado uma boa prática criar uma branch para desenvolvimento `develop`.

Porque a branch **develop** não é sobre “seguir o GitFlow ao pé da letra” — é sobre **ter um lugar seguro pra integrar features antes de chegar na main**.

Mesmo em times que não usam GitFlow completo, muita gente mantém uma `develop` porque ela:

* evita sujeira na `main` (que deve estar sempre estável)
* permite juntar várias features, testar, revisar e só depois promover pra produção
* cria um “ambiente de integração” que não quebra nada em quem depende da main

Em times pequenos, dá pra viver só com `main` + feature branches.
Em times médios ou quando você quer mais segurança, a `develop` vira um amortecedor útil.
