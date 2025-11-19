# Visualização do `git log` e o uso do pager `less`

Quando o comando `git log` é executado, o Git envia a saída para um pager chamado `less` sempre que o conteúdo é maior que o tamanho da tela. Isso permite navegar pelos commits de maneira interativa, em vez de simplesmente despejar tudo no terminal.

Ao visualizar o log dessa forma, aparece a indicação `(END)` quando o arquivo chegou ao final. O terminal não está travado — apenas esperando instruções. Para sair do pager, é usado o comando:

```
q
```

Outras teclas úteis dentro do `less`:

| Ação                 | Comando    |
| -------------------- | ---------- |
| Sair do pager        | `q`        |
| Avançar uma página   | `Space`    |
| Voltar uma página    | `b`        |
| Descer linha a linha | `↓` ou `j` |
| Subir linha a linha  | `↑` ou `k` |
| Ir ao topo           | `g`        |
| Ir ao final          | `G`        |

Se preferir visualizar o log sem o pager:

```
git --no-pager log
```

Ou para desativar o pager de forma global:

```
git config --global core.pager cat
```

---

Esse comportamento não é um erro, é apenas o Git usando um visualizador interativo para facilitar a navegação pelos commits.
