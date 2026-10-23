# 8. Estruturas de Repetição

Executam um bloco de comandos várias vezes, percorrendo uma lista ou avaliando uma condição. No Bash, `for`, `while`, `until` e `select` delimitam o corpo com **`do`** e **`done`**.

> O ponto e vírgula antes de `do` separa comandos na mesma linha. Ele pode ser substituído por uma quebra de linha, como ocorre antes de `then` nas estruturas condicionais.

## Laço `for`:

Percorre uma lista como um *foreach*, atribuindo cada elemento à variável e executando o corpo uma vez para ele. Uma lista vazia não produz iterações. A lista pode ser fornecida de diferentes formas:

- **Lista explícita:** `in maçã laranja banana` fornece os elementos diretamente; aspas preservam espaços dentro de um item.
- **Expansão de chaves:** `in {1..10}` gera os números de 1 a 10 antes da execução do laço. Como visto em expansões, `{1..$n}` não utiliza o valor de `n` como limite.
- **Array:** `in "${arquivos[@]}"` percorre os elementos preservando seus limites, inclusive espaços dentro de nomes.
- **Expansão de nomes de arquivos:** `in *.sh` percorre os nomes correspondentes no diretório atual.

```bash
arquivos=("relatorio final.txt" "notas.md")
for arq in "${arquivos[@]}"; do
    cat -- "$arq"
done
```

Uma variável simples sem aspas, como em `for arq in $arquivos`, pode dividir nomes com espaços. O uso de `"${arquivos[@]}"` preserva os elementos, conforme explicado no capítulo 6.

> Em `for script in *.sh`, cada nome encontrado é preservado; no corpo, utiliza-se `"$script"`. Sem correspondências, `*.sh` permanece literal por padrão, podendo exigir um teste de arquivo antes de processá-lo.

### Argumentos Posicionais:

Omitir `in {lista}` equivale a utilizar `in "$@"`, percorrendo os argumentos posicionais separadamente:

```bash
for argumento; do
    printf 'Processando o argumento: %s\n' "$argumento"
done
```

### Forma Aritmética:

O Bash oferece uma forma semelhante ao `for` de C, com **inicialização**, **condição** e **atualização**:

```bash
for ((i=1; i<=5; i++)); do
    printf 'Número: %s\n' "$i"
done
```

`i=1` inicializa o contador uma vez; `i<=5` é avaliada antes de cada iteração; `i++` ocorre depois do corpo. A condição permite continuar enquanto seu resultado numérico for diferente de zero. O limite também pode ser uma variável, como em `i<=limite`.

## Laços `while` e `until`:

Avaliam o *status* de um comando antes de cada iteração, podendo executar **zero vezes**. Como no `if`, podem utilizar testadores ou outros comandos:

- **`while` (enquanto):** Executa o corpo enquanto o comando retorna sucesso (`0`).
- **`until` (até que):** Executa o corpo enquanto o comando retorna um *status* diferente de zero; encerra quando ele retorna sucesso.

```bash
contador=1
while [ "$contador" -le 5 ]; do
    printf 'Número: %s\n' "$contador"
    ((contador++))
done
```

Substituir a linha `while [ "$contador" -le 5 ]; do` por `until [ "$contador" -gt 5 ]; do` produz a mesma contagem: repete até que o contador ultrapasse 5.

### Leitura de Arquivos por Linha:

O `read` retorna sucesso ao ler uma linha, permitindo controlar a repetição. `IFS= read -r` preserva espaços e barras invertidas:

```bash
while IFS= read -r linha; do
    printf 'Linha: %s\n' "$linha"
done < arquivo.txt
```

O redirecionamento após `done` fornece a entrada para todo o laço, inclusive linhas vazias. Já `for linha in $(cat arquivo.txt)` separa palavras, perdendo os limites das linhas.

> Para incluir um último trecho sem quebra de linha, utiliza-se a condição `IFS= read -r linha || [ -n "$linha" ]`. O redirecionamento também evita a *subshell* normalmente criada por `cat arquivo.txt | while ...`, mantendo as alterações de variáveis no contexto atual.

## Controle da Repetição:

- `break` - Encerra o laço atual imediatamente e continua depois do `done`.
- `continue` - Ignora o restante da iteração atual e passa à seguinte. No `for` por lista, avança para o próximo elemento; no aritmético, executa a atualização e testa novamente; em `while` e `until`, retorna ao teste.

```bash
for ((i=1; i<=5; i++)); do
    if ((i == 3)); then
        continue
    fi
    printf '%s\n' "$i"
done
# Imprime 1, 2, 4 e 5, um por linha.
```

> Em um `while`, um `continue` colocado antes da atualização do contador pode impedir que a condição de parada seja alcançada. No `for` aritmético acima, `i++` faz parte da própria estrutura e continua sendo executado.

## Menus com `select`:

Exibe uma lista numerada e aguarda uma escolha. Sua sintaxe é semelhante à do `for`; se a lista for omitida, utiliza os argumentos posicionais (`"$@"`).

Um número válido atribui o item correspondente à variável; uma entrada inválida deixa essa variável vazia. **`REPLY`** guarda o texto digitado. Após executar o corpo, `select` aguarda outra escolha, até encontrar um `break` ou o fim da entrada. Uma linha vazia reapresenta o menu sem executar o corpo.

```bash
PS3="Escolha uma opção: "
select fruta in maçã banana kiwi laranja sair; do
    if [ "$fruta" = "sair" ]; then
        printf 'Encerrando o menu...\n'
        break
    elif [ -n "$fruta" ]; then
        printf 'Você escolheu: %s\n' "$fruta"
    else
        printf 'Opção inválida: %s\n' "$REPLY"
    fi
done
```

> **`PS3`** é a variável da *shell* que define o *prompt* do `select`, cujo valor padrão é `#? `. Não precisa ser exportada. O menu e o *prompt* são escritos na saída de erro; a escolha é lida da entrada padrão.

---

# 9. Expressões Regulares

Uma **expressão regular** (*regex*) descreve um padrão de texto: letras esperadas, posições, alternativas e repetições. É como definir um conjunto de textos possíveis; uma sequência que atende às regras **corresponde** à expressão. Ferramentas como `grep`, `sed` e `awk` utilizam esses padrões para buscar ou transformar texto.

> Regex e padrões da *shell* (*globs*) têm regras diferentes. Em `*.txt`, o `*` representa qualquer sequência no padrão da *shell*; em regex, repete o elemento anterior. Os *globs* também aparecem em `case` e em operações sobre variáveis, não apenas na expansão de nomes de arquivos.

## Sintaxes Básica e Estendida:

As sintaxes **BRE** (*Basic Regular Expressions*) e **ERE** (*Extended Regular Expressions*) diferem na escrita de algumas operações. `grep` e `sed` utilizam BRE por padrão; `grep -E`, `sed -E`, `awk` e o operador `=~` do Bash utilizam ERE.

| Construção | BRE | ERE |
| --- | --- | --- |
| Grupo | `\(...\)` | `(...)` |
| Repetição delimitada | `\{n,m\}` | `{n,m}` |
| Parênteses literais | `(texto)` | `\(texto\)` |

Em ERE, `+`, `?` e `|` também são operadores. GNU `grep` e GNU `sed` aceitam `\+`, `\?` e `\|` em BRE como extensões, mas essas formas não são universais de POSIX BRE. Para esses recursos, os exemplos seguintes utilizam ERE.

As aspas simples em `grep -E 'padrao' arquivo.txt` protegem o padrão da interpretação da *shell*. A barra invertida que chega à ferramenta é então interpretada conforme a sintaxe da regex.

> Por padrão, `grep` imprime as **linhas** que contêm correspondências. A opção `-o` mostra apenas os trechos encontrados, um por linha; `-F`, já apresentado no capítulo 1, procura texto literal.

## Literais, Metacaracteres e Âncoras:

Caracteres **literais** representam a si mesmos, como `a` e `7`. **Metacaracteres** organizam o padrão; seu significado depende da posição e da sintaxe utilizada.

- `.` - Corresponde a um caractere. Nas buscas por linha com `grep`, não atravessa a separação entre linhas.
- `[caracteres]` - Corresponde a um caractere do conjunto. `[abc]` aceita `a`, `b` ou `c`; `[a-z]` indica uma faixa.
- `[[:classe:]]` - Utiliza uma classe POSIX, como `[[:digit:]]` para um dígito e `[[:alpha:]]` para uma letra. As classes e a influência do idioma e região foram apresentadas no capítulo 5.
- `[^caracteres]` - Nega o conjunto quando `^` aparece logo após `[`. Assim, `[^[:alnum:]]` corresponde a um caractere que não seja alfanumérico.
- `^` e `$` - Ancoram o padrão no início e no final do texto considerado. Nas buscas por linha, `^erro$` exige exatamente `erro`; sem âncoras, também encontra esse trecho em `ocorreu um erro`.
- `\` - Altera a interpretação do próximo caractere conforme a sintaxe. `\.` procura um ponto literal; em BRE, `\(` inicia um grupo. Portanto, o escape não serve apenas para remover significado especial.

> Delimitadores como `/` em `sed 's/padrao/novo/'` pertencem à sintaxe da ferramenta. Não fazem parte obrigatória da regex: `grep 'padrao' arquivo.txt` não os utiliza.

## Repetições, Agrupamentos e Alternância:

Os **quantificadores** indicam quantas vezes o elemento anterior pode ocorrer. Na sintaxe ERE:

| Quantificador | Quantidade de Ocorrências |
| --- | --- |
| `*` | Zero ou mais. |
| `+` | Uma ou mais. |
| `?` | Zero ou uma. |
| `{n}` | Exatamente `n`. |
| `{n,m}` | De `n` a `m`. |
| `{n,}` | No mínimo `n`. |

As chaves desses quantificadores fazem parte da expressão real; somente os valores `n` e `m` são substituídos. O operador `*` também funciona sem escape em BRE.

> Para “até `m` vezes”, utiliza-se `{0,m}`. A forma abreviada `{,m}`, aceita pelo GNU `grep`, é uma extensão e não deve ser presumida em qualquer ferramenta.

Em ERE, **`(...)`** agrupa elementos e **`|`** separa alternativas. Um quantificador após o grupo se aplica ao conjunto, não apenas ao último caractere:

```bash
grep -E '\.(bz|gz|zip)$' lista_arquivos.txt
# Lista de nomes, um por linha: aceita arquivo.zip, mas não arquivo.zip.bak.
```

O ponto escapado é literal, o grupo reúne as extensões alternativas e `$` exige que estejam no final.

```bash
grep -Eo '[[:upper:]][[:alpha:]]*' texto.txt
# Extrai trechos com uma maiúscula seguida de zero ou mais letras.
```

Esse padrão encontra `This` em `This works`, mas também `Bar` dentro de `fooBar`; não exige, sozinho, o início de uma palavra.

O exemplo de telefone combina alternativas e quantificadores:

```bash
grep -E '^(\([0-9]{2}\)|[0-9]{2}) [0-9]{4,5}-?[0-9]{4}$' contatos.txt
# Aceita (31) 9999-9999, 31 99999999 e 31 99999-9999.
```

A primeira parte aceita dois dígitos **com o par de parênteses ou sem ambos**. Depois, exige um espaço, quatro ou cinco dígitos, um hífen opcional e mais quatro dígitos. As âncoras exigem que a linha inteira siga esse formato; o teste não verifica se o telefone existe.

### Comportamento Guloso (Greedy):

No modelo POSIX, procura-se a correspondência que começa mais à esquerda e, nessa posição, a mais longa possível. Assim, uma repetição pode abranger separadores intermediários enquanto o padrão completo continuar correspondendo.

Considere `this (rug) is not what it once was (a long time ago), is it?`:

- `th.*is` corresponde a `this (rug) is not what it once was (a long time ago), is`, indo até o último `is` possível.
- Em **ERE**, `\(.*\)` corresponde a `(rug) is not what it once was (a long time ago)`, do primeiro `(` ao último `)`. Em BRE, os parênteses escapados teriam função de agrupamento.

```bash
printf '%s\n' 'this (rug) is not what it once was (a long time ago), is it?' |
    grep -Eo '\(.*\)'
```

## Substituição de Texto com `sed`:

O comando de substituição procura um padrão e troca o trecho correspondente por outro texto:

- `sed 's/{padrao}/{substituicao}/' {arquivo}` - Substitui a primeira ocorrência de cada linha.
- `sed 's/{padrao}/{substituicao}/g' {arquivo}` - Substitui todas as ocorrências de cada linha.

Nesses modelos, as chaves indicam valores a substituir; `s` seleciona a operação e `/` delimita seus campos. O resultado vai para a saída padrão; **esses comandos não alteram o arquivo original**.

### Correspondência Completa com `&`:

No texto de substituição do `sed`, **`&`** representa todo o trecho encontrado. Permite reaproveitá-lo sem precisar conhecer seu valor antecipadamente.

```bash
printf '%s\n' '/bin/bash' | sed 's/[^/]*sh$/__&__/'
# Saída: /bin/__bash__
```

O padrão encontra a parte final do caminho terminada em `sh`, sem incluir `/`; a substituição envolve esse trecho com `__`.

> Para inserir um `&` literal na substituição, utiliza-se `\&`. Essas regras pertencem ao campo de substituição do `sed`; não são operadores universais de toda linguagem de regex.

### Grupos e Referências (Retrovisores):

Grupos permitem separar partes da correspondência e reutilizá-las. São numerados pela ordem de abertura, da esquerda para a direita; na substituição, `\1`, `\2` etc. recuperam seus conteúdos.

Considere `megadeth.txt` no formato “Sobrenome, Nome”:

```text
Mustaine, David S.
Menza, Nick
```

```bash
sed 's/^\([^,]*\), \(.*\)$/\2 \1/' megadeth.txt
```

- `^\([^,]*\)` - Primeiro grupo: do início da linha até antes da vírgula (sobrenome).
- `, ` - Vírgula e espaço literais, fora dos grupos.
- `\(.*\)$` - Segundo grupo: o restante da linha (nome).
- `\2 \1` - Recoloca o nome antes do sobrenome, separados por um espaço.

```text
David S. Mustaine
Nick Menza
```

> Com `sed -E`, os grupos passam a usar parênteses sem escape; as referências na substituição continuam sendo `\1`, `\2` etc. O `&` recupera a correspondência completa, enquanto cada referência recupera um grupo.

## Expressões Regulares nos Testes do Bash:

O operador **`=~`** utiliza uma expressão regular estendida em `[[ ... ]]`, conforme introduzido no capítulo 7. O teste retorna sucesso quando encontra uma correspondência; âncoras permitem exigir que todo o valor siga o padrão.

```bash
numero="12345"
padrao='^[0-9]+$'
if [[ $numero =~ $padrao ]]; then
    printf 'O texto contém apenas dígitos.\n'
fi
```

As aspas na atribuição preservam a regex como texto. No teste, **`$padrao` fica sem aspas** para que seus operadores sejam interpretados como regex; `"$padrao"` faria uma comparação literal com o conteúdo expandido. Nesse contexto, não ocorre separação de palavras nem expansão de nomes de arquivos.
