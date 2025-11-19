# Templates

---

# TEMPLATE PARA ISSUES

*(Use para bugs, features, melhorias.)*

```
## Título
[Tipo] Descrição curta do problema ou funcionalidade
Ex: [BUG] Cálculo de média incorreto no módulo de alunos

## Descrição
Explique claramente o problema ou a feature.
O que está acontecendo? O que deveria acontecer?

## Passos para Reproduzir (bugs)
1. ...
2. ...
3. ...
Resultado atual:
Resultado esperado:

## Escopo da Task
- O que precisa ser implementado, corrigido ou investigado.

## Critérios de Aceite
- [ ] O erro não ocorre mais / funcionalidade implementada
- [ ] A solução é testável e validada
- [ ] Código segue padrões do projeto

## Contexto Adicional
Logs, prints, links, anotações.
```

---

# TEMPLATE PARA ABERTURA DE PULL REQUEST

```
## Descrição
Explique o que foi implementado, corrigido ou alterado.
Se estiver resolvendo uma issue, use a sintaxe:
Fixes #<numero-da-issue> ou Closes #<numero-da-issue>

## O que foi alterado
- ...
- ...
- ...

## Como testar
Passo a passo simples para validar a mudança.
1. ...
2. ...
3. ...

## Checklist
- [ ] Código compila sem erros
- [ ] Testes (quando houver) passam
- [ ] Não quebrou comportamento existente
- [ ] Seguiu padrões do projeto (nomenclaturas, estrutura, etc.)
- [ ] Documentação/README atualizado (se necessário)

## Screenshots / Evidências
Se for visual ou tiver output relevante, coloque aqui.

## Observações
Algo importante que o reviewer precisa saber.
```

---

# TEMPLATE PARA MENSAGENS DE COMMIT (BÔNUS)

Se quiser ficar ainda mais redonda nos padrões:

```
<tipo>: descrição curta da mudança

Ex:
feat: adiciona cálculo corrigido de média no módulo aluno
fix: corrige crash ao cadastrar aluno sem notas
refactor: melhora organização do módulo de cadastro
docs: atualiza README com exemplos
```

---

