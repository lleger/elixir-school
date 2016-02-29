---
layout: page
title: Operador Pipe
category: basics
order: 6
lang: pt
---

O operador pipe `/>` passa o resultado de uma expressão como o primeiro parâmetro de outra expressão.

## Sumário

- [Introdução](#introducao)
- [Exemplos](#exemplos)
- [Boas Práticas](#boas-praticas)

## <a name="introducao"></a>Introdução

Programação pode se tornar algo bem confuso. Tão confuso que o fato de chamadas em funções podem ficar tão incorporadas a outras chamadas de função, tornando-se muito difícil de seguir. Tome em consideração as seguintes funções aninhadas:

```elixir
foo(bar(baz(new_function(other_function()))))
```

Aqui, nós estamos passando o valor `other_function/1` para `new_function/1`, e `new_function/1` para `baz/1`, `baz/1` para `bar/1`, e finalmente o resultado de `bar/1` para `foo/1`. Elixir possui um modo pragmático para esse caos sintático, permitindo-nos a utilização do operador pipe. O operador pipe é representado por `|>`, *recebe o resultado de uma expressão e passa ele adiante*. Vamos dar mais uma olhada no trecho de código acima reescrito com o operador pipe.

```elixir
other_function() |> new_function() |> baz() |> bar() |> foo()
```

O pipe pega o resultado da esquerda e o passa para o lado direito.

## Exemplos

Por este conjunto de exemplos, nós iremos usar o módulo String de Elixir.

- Tokenize String (vagamente)

```shell
iex> "Elixir rocks" |> String.split
["Elixir", "rocks"]
```

- Converte palavras para letras maiúsculas

```shell
iex> "Elixir rocks" |> String.split |> Enum.map( &String.upcase/1 )
["ELIXIR", "ROCKS"]
```

- Checa terminação de palavra

```shell
iex> "elixir" |> String.ends_with?("ixir")
true
```

## <a name="boas-praticas"></a>Boas Práticas

Se a aridade de uma função é mais do que 1, certifique-se de usar parêntesis. Isso não importa muito para Elixir, porém é importante para outros programadores que podem interpretar mal o seu código. Se tomarmos o nosso segundo exemplo, e retirar os suportes do `Enum.map/2`, nos encontramos com o seguinte aviso:

```shell
iex> "Elixir rocks" |> String.split |> Enum.map &String.upcase/1
iex: warning: you are piping into a function call without parentheses, which may be ambiguous. Please wrap the function you are piping into in parenthesis. For example:

foo 1 |> bar 2 |> baz 3

Should be written as:

foo(1) |> bar(2) |> baz(3)

["ELIXIR", "ROCKS"]
```
