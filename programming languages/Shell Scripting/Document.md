# 8. Estruturas de Repetição

Diferente das estruturas condicionais (`if`, `case`), que utilizam o próprio nome ao contrário para delimitar o fim do bloco de ação (`fi`, `esac`), as estruturas de repetição (`for`, `while`, `until` e `select`) determinam o seu bloco de execução usando as palavras **`do`** (faça) e **`done`** (feito).

## O laço `for`:

O `for` tradicional funciona de maneira semelhante a um *foreach* de linguagens como C++ e Java, iterando sobre os elementos de uma lista. Como a *shell* trata os dados fundamentalmente como textos, essa iteração ocorre varrendo uma sequência de *strings*. 

```bash
for {variavel} in {lista}; do
    {comandos}
done
```
> O ponto e vírgula antes de `do` permite colocar o comando na mesma linha do laço. Ele pode ser substituído por uma quebra de linha.

A `{lista}` que alimenta a repetição pode ser construída de várias maneiras graças ao mecanismo de expansões da *shell*:

- **Lista explícita:** Os valores são passados diretamente no código.
  ```bash
  for fruta in maçã laranja banana; do
      printf 'Fruta atual: %s\n' "$fruta"
  done
  ```
- **Expansão de chaves:** Muito utilizada para simular contagens incrementais.
  ```bash
  for numero in {1..10}; do
      printf 'Contagem: %s\n' "$numero"
  done
  ```
- **Expansão de variáveis:** A lista pode vir de informações previamente armazenadas em uma variável ou do resultado de um comando.
  ```bash
  arquivos="relatorio.txt notas.md"
  for arq in $arquivos; do
      cat "$arq"
  done
  ```
- **Listas do sistema:** É possível utilizar curingas (`*`) para iterar diretamente sobre os arquivos de um diretório.
  ```bash
  for script in *.sh; do
      chmod +x "$script"
  done
  ```

### Argumentos Posicionais Implícitos:

Uma curiosidade muito útil do `for` tradicional é que ele pode iterar automaticamente sobre os argumentos passados para o *script* na hora de sua execução. Para isso, basta omitir a lista de declaração (o que equivale a escrever `in "$@"`).

```bash
for argumento; do
    printf 'Processando o argumento: %s\n' "$argumento"
done
```

### Sintaxe Estilo C:

O Bash também oferece uma forma alternativa para o `for` com uma sintaxe baseada em expressões aritméticas, idêntica à programação em C. Nela, utilizam-se parênteses duplos e três expressões:

```bash
for (( {expr_1}; {expr_2}; {expr_3} )); do
    {comandos}
done
```

- **`expr_1`:** Avaliada antes da primeira iteração (usada para inicialização, ex: `i=0`).
- **`expr_2`:** Avaliada antes de cada iteração. Se a condição for verdadeira, o bloco é executado; se for falsa, o laço é encerrado (usada para limite/parada, ex: `i<10`).
- **`expr_3`:** Avaliada no final de cada iteração (usada para o incremento, ex: `i++`).

## Os laços `while` e `until`:

Essas estruturas funcionam avaliando o *status* de saída de um comando, de forma muito parecida com o `if`.

- **`while` (enquanto):** Testa um comando e, se ele retornar verdadeiro (sucesso, *status* `0`), o bloco de comandos é executado. Ele continua repetindo até que o teste resulte em falso.
- **`until` (até que):** É o oposto exato do `while`. Ele executa o bloco de comandos enquanto o teste for falso (diferente de zero). Assim que o teste retornar sucesso, a repetição é encerrada.

```bash
while {comando_testado}; do
    {comandos}
done

until {comando_testado}; do
    {comandos}
done
```

Por avaliarem o sucesso de comandos, é extremamente comum utilizá-los em conjunto com o comando `test` (representado por `[ ]`) para verificar variáveis e arquivos.

```bash
contador=1
while [ "$contador" -le 5 ]; do
    printf 'Número: %s\n' "$contador"
    ((contador++))
done
```

## Controle de Fluxo: `break` e `continue`

Assim como na maioria das linguagens de programação, é possível alterar o fluxo natural de uma repetição utilizando comandos específicos dentro do bloco de ação:

- **`break`:** Finaliza imediatamente a estrutura de repetição, ignorando qualquer comando restante no bloco e partindo para a execução do código que estiver após o `done`.
- **`continue`:** Interrompe apenas a iteração atual. Ele ignora o restante do código daquele ciclo e volta imediatamente para o início da estrutura (para testar a condição novamente ou pular para o próximo item da lista).

## Menus com `select`:

O `select` é uma estrutura voltada para interatividade. Ele exibe automaticamente um menu numerado a partir de uma lista de itens e aguarda que o usuário digite uma escolha. A sintaxe base é idêntica à do `for`:

```bash
select {variavel} in {lista}; do
    {comandos}
done
```

Assim como no `for`, se a lista for omitida, ele utilizará implicitamente os argumentos posicionais do *script* (`"$@"`).

Ao escolher um número, o conteúdo correspondente na lista é atribuído à `{variavel}` definida, e o bloco de comandos é executado. Como o menu repete a pergunta infinitamente por padrão, **é comum incluir uma opção de saída na lista e utilizar o comando `break` para encerrar o `select`**.

```bash
select fruta in maçã banana kiwi laranja sair; do
    if [ "$fruta" = "sair" ]; then
        printf 'Encerrando o menu...\n'
        break
    elif [ -n "$fruta" ]; then
        printf 'Você escolheu: %s\n' "$fruta"
    else
        printf 'Opção inválida. Tente novamente.\n'
    fi
done
```

> **Curiosidade:** O texto do *prompt* onde o usuário digita sua escolha (que por padrão é `#?`) é definido pela variável de ambiente `$PS3`. Você pode alterá-la antes do `select` (ex: `PS3="Escolha uma opção: "`) para personalizar a interface do seu *script*.
