# Configuração Pipeline CI via GitHub Actions I

**Sem workflow de CI configurado, o GitHub não testa absolutamente nada pra você.**

O repositório fica “morto” nesse sentido:
push → entra direto
PR → passa direto
merge → ninguém valida nada
main → pode quebrar a qualquer momento

O GitHub *não* adivinha que você quer integração contínua.
Ele só roda pipelines **se você criar workflows dentro de `.github/workflows/`**.

---

### O que acontece num repositório sem CI configurada?

* Push sem testes? Entra.
* PR sem testes? Entra.
* Código quebrado? Entra.
* Merge que destrói a aplicação? Entra também.

É como dirigir sem cinto, sem airbag e com farol queimado.
Você só descobre o problema quando bate.

---

### E quando você adiciona CI?

Aí o jogo muda completamente:

1. Cada push → pipeline roda
2. Cada PR → pipeline roda
3. Merge só acontece se tudo estiver verde
4. main fica **sempre** saudável
5. Refatorar deixa de ser assustador
6. Repositório passa a ter “comportamento profissional”

---

