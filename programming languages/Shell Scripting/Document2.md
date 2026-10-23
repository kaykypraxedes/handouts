# 9. Expressões Regulares

Expressões regulares (*Regex* ou *POSIX Regular Expressions*) são notações utilizadas para **identificar e buscar padrões de texto**. 

Em vez de procurar por uma palavra exata, uma expressão regular define um conjunto de regras e estruturas. Qualquer texto que siga esse padrão — seja por iniciar com determinadas letras, possuir um tamanho específico, conter apenas números ou apresentar repetições — é capturado pela expressão. Ela meio que define um grupo onde estão incluidos todos os textos que seguem essa regra.

As expressões regulares são amplamente suportadas no ambiente *Linux* e em programação, estando presentes em:

- **Ferramentas de linha de comando:** `grep`, `sed`, `awk`.
- **Editores de texto no terminal:** `vim`, `emacs`.
- **Linguagens de programação:** *Perl*, *Python*, *JavaScript*, entre outras.

> **Importante:** Não confunda expressões regulares com os caracteres curingas do *shell* (*globs*, como o `*` em `ls *.txt`). Embora utilizem alguns símbolos parecidos, os curingas servem para casar nomes de arquivos no terminal, enquanto as expressões regulares servem para analisar o conteúdo interno de textos de forma muito mais abrangente e precisa.

## Caracteres Literais e Especiais:

Nas expressões regulares, praticamente qualquer dado que não seja uma quebra de linha (`\n`) é interpretado como um caractere. Eles se dividem em dois grupos:

*   **Literais:** São interpretados como eles mesmos (ex: a letra `a` casa com a letra "a" no texto).
*   **Especiais (Metacaracteres):** Possuem significados específicos e são utilizados para estruturar casamentos mais complexos.

> Alguns programas e ferramentas (como o `sed`, o `awk` e o `vim`) utilizam a barra normal (`/`) em suas sintaxes para delimitar onde começa e onde termina uma expressão regular (ex: `/padrao/`). No entanto, isso é uma regra da ferramenta, e não das expressões regulares em si. O `grep`, por exemplo, recebe o padrão sem essas barras.

## Expressões Regulares Básicas:

Os caracteres especiais que formam a estrutura básica de uma expressão regular são:

*   `.` (Ponto): Casa **qualquer caractere único**, de maneira muito semelhante ao funcionamento do curinga `?` na expansão de nomes de arquivos da *shell*.
*   `[ ]` (Colchetes): Define uma classe de caracteres. Casa um **único caractere** que pertença ao conjunto delimitado. Assim como na expansão de arquivos, permite definir caracteres específicos, faixas (como `[a-z]`) e classes POSIX (como `[:alnum:]`, `[:digit:]`, `[:blank:]`, etc.).
    *   **Negação:** Se o primeiro caractere dentro dos colchetes for um acento circunflexo (`^`), ele não atua como âncora, mas inverte a lógica do colchete, casando qualquer coisa que **não** esteja no conjunto. Por exemplo, `[^[:alnum:]]` procura tudo que não seja um caractere alfanumérico.
*   `*` (Asterisco): Representa **zero ou mais ocorrências** do elemento imediatamente anterior a ele.
*   `^` e `$` (Âncoras): Marcam posições específicas no texto. O `^` (quando usado fora de colchetes) ancora o casamento no **início de uma linha**, e o `$` ancora no **término de uma linha**.
*   `\` (Barra Invertida): Atua como caractere de escape. Remove o significado especial do caractere seguinte, fazendo com que ele seja lido literalmente. Por exemplo, para procurar pela frase literal "texto [com colchetes]", deve-se usar `\[` para escapar o caractere especial.

### Comportamento Guloso (*Greedy*):

Uma regra fundamental do casamento de padrões é que ele sempre tenta englobar o **maior trecho possível** que satisfaça a expressão. 

Por exemplo, considere a seguinte frase:
`this (rug) is not what it once was (a long time ago), is it?`

- A expressão `th.*is` não vai parar no primeiro "is" da palavra "this". Ela casará desde o primeiro "th" até o **último** "is" encontrado na frase: `this (rug) is not what it once was (a long time ago), is`.
- A expressão `\(.*\)` não casará apenas com `(rug)`. O asterisco fará o padrão engolir tudo até o último parêntese, casando com: `(rug) is not what it once was (a long time ago)`.

## Substituição de Texto:

Editores de texto (como o Vim) e ferramentas de fluxo (como o `sed`) usam expressões regulares não apenas para encontrar padrões, mas para substituí-los. Nesses comandos, você pode referenciar o texto exato que foi casado para reaproveitá-lo na string de substituição.

### O caractere `&`:

O "E comercial" (`&`) na string de substituição representa **toda a *string* que casou** com a expressão regular. Ele insere exatamente o que foi encontrado.

```bash
# A expressão procura o nome do executável no final do caminho.
# Se a variável for "/bin/bash", o sed substitui "bash" por "__bash__".
echo "$SHELL" | sed 's/[^/]*sh$/__&__/'
# Saída: /bin/__bash__
```

### Agrupamentos e Retrovisores (`\1`, `\2`...):

É possível fatiar o padrão casado e isolar partes específicas para usá-las separadamente na substituição. Para isso, os trechos de interesse devem ser agrupados entre parênteses. Em expressões regulares básicas, esses parênteses precisam ser escapados: `\(` e `\)`.

A ferramenta numera os grupos na ordem em que eles são abertos, da esquerda para a direita. Na string de substituição, eles são chamados por referências como `\1`, `\2`, `\3`, etc.

**Exemplo prático: Reordenando nomes de uma lista**
Considere um arquivo `megadeth.txt` organizado no formato "Sobrenome, Nome":

```text
Mustaine, David S.
Ellefson, David W.
Friedman, Martin A.
Menza, Nick
```

Para inverter a ordem para "Nome Sobrenome", podemos usar o `sed` com retrovisores:

```bash
cat megadeth.txt | sed 's/\([^,]*\), \(.*\)/\2 \1/'
```

**Entendendo o comando:**
- `\([^,]*\)` captura o primeiro grupo (`\1`): Tudo do início até a vírgula (Sobrenome).
- `, ` casa literalmente com a vírgula e o espaço. O que está aqui não será guardado em nenhum grupo.
- `\(.*\)` captura o segundo grupo (`\2`): O restante da linha (Nome).
- `\2 \1` define que a substituição será o segundo grupo capturado seguido pelo primeiro grupo capturado.

**Saída:**
```text
David S. Mustaine
David W. Ellefson
Martin A. Friedman
Nick Menza
```

## Expressões Regulares Estendidas (ERE):

As Expressões Regulares Estendidas introduzem novos metacaracteres (como `?`, `+`, `|`, `{`, `}`) que facilitam buscas complexas. 

A principal diferença na prática está em como os programas interpretam esses símbolos:
* **Expressões Básicas (ex: `grep`, `sed`):** Esses símbolos são lidos como texto comum. Para ativarem suas funções especiais, eles precisam ser "escapados" com uma barra invertida (ex: `\?`, `\|`, `\+`).
* **Expressões Estendidas (ex: `grep -E`, `egrep`, `awk`):** Esses símbolos já possuem suas funções especiais por padrão. A barra invertida só é usada se você quiser transformá-los em texto literal.

### Alternância (`|`):

Funciona como um operador lógico *OU*. Permite que a expressão case com um conjunto de padrões, e não apenas um.

```bash
# Casa com linhas que contenham "AAAA" ou "BBBB"
grep -E 'AAAA|BBBB' arquivo.txt
```

É muito comum usar a alternância junto com **agrupamentos** `( )` para restringir quais partes da expressão são alternativas.
```bash
# Procura por arquivos com as extensões bz, gz ou zip.
# O ponto é escapado (\.) para ser lido como um ponto literal.
grep -E '\.(bz|gz|zip)' lista_arquivos.txt
```

### Quantificadores:

Eles definem quantas vezes o caractere, classe ou grupo imediatamente anterior deve ocorrer para que o padrão seja validado.

* `?` (Opcional - Zero ou uma vez): Torna o elemento precedente opcional.
  **Exemplo:** Buscar números de telefone. O DDD pode vir com ou sem parênteses literais (escapados como `\(` e `\)`), e o traço separador também pode ser opcional.
  ```bash
  # Casa com "(31) 9999-9999", "31 99999999", etc.
  grep -E '\(?[0-9]{2}\)? [0-9]{4,5}-?[0-9]{4}' contatos.txt
  ```

* `*` (Zero ou mais vezes): Embora faça parte das expressões básicas, é muito combinado com classes de caracteres.
  **Exemplo:** Buscar palavras que obrigatoriamente comecem com uma letra maiúscula, seguidas por qualquer quantidade (inclusive zero) de letras maiúsculas ou minúsculas.
  ```bash
  # Casa com "This" (de "This works"), mas ignora "this" (de "this does not").
  grep -E '[[:upper:]][[:upper:][:lower:]]*' texto.txt
  ```

* `+` (Uma ou mais vezes): Semelhante ao asterisco, mas exige que a ocorrência aconteça **pelo menos uma vez**.

* `{ }` (Repetição específica): Permite definir exatamente a quantidade de vezes que o padrão deve se repetir.
  * `{n}`: Exatamente *n* vezes.
  * `{n,m}`: Uma faixa de no mínimo *n* e no máximo *m* vezes.
  * `{n,}`: No mínimo *n* vezes (sem limite máximo).
  * `{,m}`: No máximo *m* vezes (de zero até *m*).
