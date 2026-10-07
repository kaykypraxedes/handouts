```
 .´‾‾‾‾‾‾‾`.
/     _____|
▏   /        
▏   ▏      
▏   \      
\     ‾‾‾‾‾|
 `.______.´
```

# 0. Introdução

> Esta apostila utiliza o **C17** como referência.

A biblioteca padrão de C reúne funções, tipos e *macros* para operações como entrada e saída, manipulação de *strings*, alocação e cálculos matemáticos. Seus recursos são organizados em **cabeçalhos**, adicionados por `#include`.

O cabeçalho apresenta as declarações ao compilador; a implementação das funções é fornecida pela biblioteca.

Os trechos pressupõem a inclusão do cabeçalho correspondente e podem ser colocados em `main`, salvo indicação contrária. O uso de cada função envolve três cuidados: **tipos dos argumentos, retorno e limites da memória utilizada**.

---

# 1. Entrada e Saída - `<stdio.h>`

O cabeçalho `<stdio.h>` reúne operações sobre **fluxos** (*streams*): sequências de dados recebidas ou produzidas pelo programa. Três fluxos estão disponíveis na execução de programas em ambientes hospedados:

| Fluxo | Finalidade | Associação Habitual |
|---|---|---|
| `stdin` | Entrada padrão | Teclado |
| `stdout` | Saída padrão | Terminal |
| `stderr` | Erros e diagnósticos | Terminal |

Essas associações podem mudar por redirecionamento. Assim, `printf` escreve em `stdout`, mesmo quando a saída é destinada a um arquivo.

## Saída Formatada - `printf`:

`printf(formato, ...)` combina texto e especificadores, correspondentes aos argumentos na mesma ordem. Retorna a quantidade de caracteres escritos ou um valor negativo em caso de erro.

| Formato | Uso em `printf` |
|---|---|
| `%d` / `%i` | `int` em decimal |
| `%u`, `%o`, `%x` / `%X` | `unsigned int` em decimal, octal ou hexadecimal |
| `%ld` / `%lu` | `long` / `unsigned long` |
| `%lld` / `%llu` | `long long` / `unsigned long long` |
| `%zu` | `size_t`, como o resultado de `sizeof` |
| `%f`, `%e`, `%g` | `double`: decimal, científico ou escolha entre ambos |
| `%Lf` | `long double` |
| `%c` / `%s` | Caractere recebido como `int` / *string* terminada em `'\0'` |
| `%p` | Ponteiro convertido para `void *` |
| `%%` | Símbolo `%`, sem consumir argumento |

A **largura** define um campo mínimo; `-` alinha à esquerda e `0` permite preenchimento numérico com zeros. A **precisão**, em `%f`, indica casas decimais; em `%s`, limita os bytes escritos. `#` solicita formas alternativas, como `0x` em hexadecimal não nulo.

```c
const char *produto = "Resistor";
int quantidade = 12;
double preco = 0.35;
printf("%-10s | %04d | %6.2f\n", produto, quantidade, preco);
// Resistor   | 0012 |   0.35
printf("Hexadecimal: %#x; progresso: %d%%\n", 42u, 75);
// Hexadecimal: 0x2a; progresso: 75%
```

A largura não corta valores maiores: `%4d` imprime todos os dígitos de `123456`. A formatação altera a apresentação, não a variável. Valores `float` são promovidos a `double` nessa chamada, permitindo `%f` para ambos.

> Formatos incompatíveis com os argumentos podem causar comportamento indefinido. Para exibir texto externo, `printf("%s", texto)` mantém o conteúdo separado do formato, mesmo que contenha `%`.

## Caracteres e Saída Simples:

| Função | Operação | Retorno |
|---|---|---|
| `puts(texto)` | Escreve em `stdout` e acrescenta `\n`; não interpreta formatos | Não negativo no sucesso; `EOF` na falha |
| `putchar(c)` | Escreve um caractere em `stdout` | Caractere escrito; `EOF` na falha |
| `getchar()` | Lê um caractere de `stdin` | Caractere lido; `EOF` por fim da entrada ou erro |

Nos retornos de caracteres, o valor é convertido de `unsigned char` para `int`. O resultado de `getchar` permanece em `int` até ser comparado com `EOF`; armazená-lo antes em `char` pode perder essa distinção.

```c
int caractere;
puts("Texto:");
while ((caractere = getchar()) != '\n' && caractere != EOF) {
    putchar(caractere); // Copia a primeira linha.
}
putchar('\n');
```

`EOF` é um indicador de retorno, não um caractere gravado no final do texto. A comparação utiliza a *macro*, cujo valor não precisa ser `-1`.

## Leitura Formatada - `scanf`:

`scanf(formato, ...)` lê de `stdin` e armazena os resultados nos **endereços recebidos**. Retorna a quantidade de atribuições realizadas: `0` indica que nenhuma ocorreu; `EOF` indica falha de entrada antes de concluir a primeira conversão.

Os formatos são semelhantes aos de `printf`, mas os argumentos são ponteiros. A distinção mais importante é `%f` para `float *` e `%lf` para `double *`; `%Lf` recebe `long double *`.

```c
char produto[20], categoria;
int quantidade;
double preco;
// Entrada: Resistor 12 0.35 A
if (scanf("%19s %d %lf %c", produto, &quantidade, &preco, &categoria) == 4) {
    printf("%s: %d unidades a %.2f; categoria %c\n",
           produto, quantidade, preco, categoria);
} else {
    puts("Entrada incompleta ou invalida.");
}
```

O nome do *array* já fornece o endereço para `%s`. O limite `19` reserva espaço para `'\0'`; essa conversão lê apenas uma palavra, parando no próximo espaçamento.

Conversões como `%d`, `%f` e `%s` ignoram espaços iniciais; `%c` não. O espaço antes de `%c` no exemplo consome espaçamentos pendentes. Um espaço no formato pode corresponder a espaços, tabulações e quebras de linha.

Formatos terminados em espaço ou `\n`, como `"%d\n"`, podem continuar esperando outro caractere na entrada interativa. Após uma falha de conversão, o caractere incompatível pode permanecer no fluxo e provocar novas falhas.

> Conferir o retorno não valida a faixa numérica: um valor que não cabe no destino pode causar comportamento indefinido. `fgets` com conversões como `strtol`, de `<stdlib.h>`, oferece mais controle nessa validação.

## Leitura de Linhas e Interpretação - `fgets` e `sscanf`:

`fgets(buffer, capacidade, fluxo)` lê até `capacidade - 1` caracteres e acrescenta `'\0'` no sucesso. Para ao ler `\n`, atingir o limite ou encontrar o fim da entrada; a quebra de linha é preservada quando lida.

Retorna o endereço do *buffer* no sucesso ou `NULL` em caso de erro ou fim da entrada sem caracteres lidos. Uma linha maior que o *buffer* permanece parcialmente no fluxo. A ausência de `\n` também pode indicar uma última linha sem quebra final.

`sscanf(texto, formato, ...)` interpreta uma *string* já armazenada, seguindo os formatos e retornos de `scanf`. Assim, a leitura da linha e a interpretação dos campos podem ser separadas:

```c
char linha[80], codigo[8];
int quantidade;
double preco;
if (fgets(linha, sizeof linha, stdin) != NULL) {
    // Entrada: R07 12 0.35
    if (sscanf(linha, "%7s %d %lf", codigo, &quantidade, &preco) == 3) {
        printf("%s: %d unidades a %.2f\n", codigo, quantidade, preco);
    } else {
        puts("Registro incompleto ou invalido.");
    }
} else {
    puts("Nenhuma linha foi obtida.");
}
```

Para texto livre, como um nome completo, basta utilizar `linha` diretamente, sem `sscanf`. A interpretação não modifica a *string*, e obter os três campos não garante que não exista conteúdo adicional após eles. Os cuidados de faixa numérica de `scanf` também se aplicam.

Ao alternar `scanf` e `fgets`, uma quebra de linha pendente pode ser lida como uma linha vazia. Quando a intenção for abandonar **todo o restante da linha atual**, o descarte pode ser explícito:

```c
int caractere;
while ((caractere = getchar()) != '\n' && caractere != EOF) {
    // Descarta os caracteres restantes, inclusive outros dados da linha.
}
```

> `fflush(stdin)` não é uma forma válida de limpar a entrada em C17. Manter a leitura organizada por linhas com `fgets` costuma simplificar esse controle.

## Formatação em *Strings* - `sprintf` e `snprintf`:

Ambas formatam valores em um *buffer*. `sprintf(destino, formato, ...)` depende de espaço suficiente; `snprintf(destino, capacidade, formato, ...)` limita a escrita à capacidade informada, incluindo o terminador.

```c
char etiqueta[32];
int tamanho = snprintf(etiqueta, sizeof etiqueta, "Quantidade: %d", 12);
if (tamanho < 0) {
    puts("Falha na formatacao.");
} else if ((size_t)tamanho >= sizeof etiqueta) {
    puts("Espaco insuficiente: texto truncado.");
} else {
    puts(etiqueta); // Quantidade: 12
}
```

Sem erro, `snprintf` retorna o tamanho que o texto completo teria, **sem contar `'\0'`**. Um retorno maior ou igual à capacidade indica truncamento. Com capacidade positiva e sem erro, o conteúdo armazenado termina em `'\0'`.

A capacidade deve corresponder à memória disponível. `sizeof etiqueta` funciona porque o objeto é um *array*; aplicado a um ponteiro, `sizeof` fornece o tamanho do ponteiro. `sprintf` retorna a quantidade escrita, sem o terminador, ou um valor negativo em caso de erro.

## Manipulação de Arquivos:

`FILE` representa o controle de um fluxo: modo de acesso, posição, armazenamento temporário e indicadores de estado.

> Um `FILE *` referencia esse controle, não diretamente os bytes do arquivo.

`fopen(nome, modo)` abre o arquivo e retorna `NULL` na falha. `fclose(arquivo)` encerra o fluxo, libera seus recursos e tenta encaminhar a saída pendente; retorna `0` no sucesso ou `EOF` na falha.

### Modos de Abertura:

| Modo | Acesso | Se Não Existir | Conteúdo Existente |
|---|---|---|---|
| `"r"` | Leitura | Falha | Preservado |
| `"w"` | Escrita | Cria | Apagado na abertura |
| `"a"` | Escrita ao final | Cria | Preservado |
| `"r+"` | Leitura e escrita | Falha | Preservado |
| `"w+"` | Leitura e escrita | Cria | Apagado na abertura |
| `"a+"` | Leitura; escrita ao final | Cria | Preservado |

O caractere `b` seleciona modo binário: `"rb"`, `"wb"`, `"ab"`, `"r+b"`, `"w+b"` ou `"a+b"`. O modo texto pode traduzir representações como quebras de linha; o binário evita essas traduções. A extensão do arquivo não define o modo.

> `fclose` não equivale a `free` nem elimina a variável ponteiro. Após a chamada, o fluxo não pode ser reutilizado, mesmo se o fechamento informar erro.

### Texto e Representação Binária:

A sequência de trabalho é comum: **abrir, transferir dados, verificar os resultados e fechar**. A diferença está na representação escolhida e nas funções de transferência.

| Funções | Transferência | Retorno |
|---|---|---|
| `fprintf` / `fscanf` | Convertem entre valores e texto, como `printf` / `scanf` | Caracteres escritos ou negativo / quantidade de atribuições ou `EOF` |
| `fputs` / `fgets` | Escrevem uma *string* / leem um trecho de linha | Não negativo ou `EOF` / *buffer* ou `NULL` |
| `fputc` / `fgetc` | Escrevem / leem um caractere | Caractere como `unsigned char` convertido para `int`, ou `EOF` |
| `fwrite` / `fread` | Transferem a representação dos objetos | Quantidade de elementos completos transferidos |

`fputs(texto, arquivo)` não acrescenta quebra de linha. `fgets` funciona como nos exemplos com `stdin`, substituindo-o pelo fluxo aberto. `fgetc(arquivo)` e `fputc(c, arquivo)` permitem escolher o fluxo das operações de caracteres.

Em texto, o inteiro `10` pode ocupar os caracteres `'1'` e `'0'`. Com `fwrite`, são gravados os bytes de sua representação em memória. **O binário não identifica tipos automaticamente:** a leitura precisa conhecer a organização dos dados.

### Exemplo Comparativo:

O programa utiliza o mesmo registro e a mesma sequência de operações. `binario = 1` seleciona a representação em memória; `binario = 0` seleciona texto formatado.

```c
#include <stdio.h>

int main(void) {
    struct Registro { int codigo; double preco; };
    struct Registro original = {10, 15.50}, lido;
    int binario = 1;
    FILE *arquivo = fopen(binario ? "dados.bin" : "dados.txt",
                          binario ? "w+b" : "w+");
    if (arquivo == NULL) {
        return 1;
    }
    int falhou;
    if (binario) {
        falhou = fwrite(&original, sizeof original, 1, arquivo) != 1;
    } else {
        falhou = fprintf(arquivo, "%d %.2f\n", original.codigo, original.preco) < 0;
    }
    // Retorna ao início e permite passar da escrita para a leitura.
    if (!falhou && fseek(arquivo, 0L, SEEK_SET) != 0) {
        falhou = 1;
    }
    if (!falhou) {
        if (binario) {
            falhou = fread(&lido, sizeof lido, 1, arquivo) != 1;
        } else {
            falhou = fscanf(arquivo, "%d %lf", &lido.codigo, &lido.preco) != 2;
        }
    }
    if (fclose(arquivo) == EOF) {
        falhou = 1;
    }
    if (falhou) {
        return 1;
    }
    printf("Codigo: %d; preco: %.2f\n", lido.codigo, lido.preco);
    return 0;
}
```

`fwrite(origem, tamanho, quantidade, arquivo)` e `fread(destino, tamanho, quantidade, arquivo)` contam **elementos completos**, não necessariamente bytes. O exemplo transfere um registro por operação e só utiliza o resultado após confirmar o sucesso.

A gravação direta depende de tamanhos, ordem dos bytes, representação numérica e possíveis preenchimentos da estrutura. Ela atende a ambientes compatíveis; um formato portável precisa definir esses detalhes. Gravar um ponteiro também não grava seu destino.

> Binário preserva a representação do objeto sem conversão textual; texto permite inspeção direta e formatação, que pode reduzir a precisão - como `%.2f`. Modo de abertura e função são escolhas distintas: abrir com `b` não transforma a saída de `fprintf` em números binários.

### Posição e Estado do Fluxo:

As operações avançam um indicador de posição, como um marcador dentro do arquivo. Reposicioná-lo não significa incrementar a variável `FILE *`.

| Recurso | Finalidade |
|---|---|
| `ftell(arquivo)` | Obtém uma indicação de posição; retorna `-1L` na falha |
| `fseek(arquivo, deslocamento, referência)` | Solicita reposicionamento; retorna `0` no sucesso |
| `rewind(arquivo)` | Solicita o início e limpa os indicadores de erro e fim; não retorna estado |
| `feof(arquivo)` | Consulta se uma leitura anterior detectou fim do fluxo |
| `ferror(arquivo)` | Consulta se ocorreu erro no fluxo |

As referências de `fseek` são `SEEK_SET` (início), `SEEK_CUR` (posição atual) e `SEEK_END` (final). Nem todo fluxo permite reposicionamento.

```c
long posicao = ftell(arquivo);
if (posicao == -1L || fseek(arquivo, posicao, SEEK_SET) != 0) {
    puts("Falha na consulta ou no reposicionamento.");
}
```

Em texto, `ftell` não fornece uma contagem geral de bytes: a posição pode ser guardada e restaurada com `SEEK_SET`. De forma portável, `fseek` em texto utiliza deslocamento zero ou uma posição obtida por `ftell` no mesmo arquivo, com `SEEK_SET`.

Em modos com `+`, **escrita seguida de leitura** exige `fflush` adequado ou posicionamento. **Leitura seguida de escrita** exige posicionamento, exceto quando a leitura encontrou o fim do arquivo. O exemplo comparativo utiliza `fseek` para essa transição.

Em anexação, as escritas continuam no final mesmo após reposicionamento; em `"a+"`, reposicionar permite escolher onde ler. `fflush` encaminha saída pendente nos casos previstos para saída e atualização, sem atuar como limpeza geral de entrada.

> O retorno da leitura deve controlar o processamento: `while (fgets(linha, sizeof linha, arquivo) != NULL)`, por exemplo. `while (!feof(arquivo))` pode processar uma tentativa malsucedida, pois `feof` não prevê a próxima leitura. Após a repetição, `ferror` permite verificar se houve erro.

---

# 2. Utilidades Gerais - `<stdlib.h>`

O cabeçalho `<stdlib.h>` reúne conversões numéricas, alocação dinâmica, ordenação, busca e outras operações de uso geral. Apesar do nome, ele é apenas um dos cabeçalhos da biblioteca padrão.

## Conversões Entre Texto e Números:

Converter uma *string* significa interpretar seus caracteres e produzir um valor numérico. Um *cast* como `(int)` não realiza essa operação: converter `"120"` exige uma função que interprete o texto.

| Função | Tipo Produzido | Controle da Conversão |
|---|---|---|
| `atoi`, `atol`, `atoll` | `int`, `long`, `long long` | Conversão decimal sem diagnóstico suficiente |
| `atof` | `double` | Conversão sem diagnóstico suficiente |
| `strtol`, `strtoll` | `long`, `long long` | Base, ponto de parada e erro de faixa |
| `strtoul`, `strtoull` | `unsigned long`, `unsigned long long` | Base, ponto de parada e erro de faixa |
| `strtof`, `strtod`, `strtold` | `float`, `double`, `long double` | Ponto de parada e erros de faixa |

`atoi("12abc")` produz `12`: essas funções interpretam a parte inicial conversível. O retorno zero, sozinho, não distingue uma entrada válida de uma conversão que não ocorreu. Quando há necessidade de validação, a família `strto...` oferece mais controle.

### Inteiros - `strtol` e Relacionadas:

`strtol(texto, &fim, base)` devolve o valor e coloca em `fim` o endereço do primeiro caractere não convertido. A base pode variar de `2` a `36`; com `0`, reconhece decimal, octal iniciado por `0` e hexadecimal iniciado por `0x` em C17.

A validação combina o ponto de parada, `errno` e os limites do destino. O exemplo aceita espaços antes e depois do número, incluindo a quebra de linha normalmente recebida por `fgets`.

```c
#include <stdlib.h>
#include <stdio.h>
#include <errno.h>  // errno e ERANGE
#include <limits.h> // INT_MIN e INT_MAX
#include <ctype.h>  // isspace

int converter_int(const char *texto, int *resultado) {
    char *fim;
    errno = 0;
    long valor = strtol(texto, &fim, 10);
    if (fim == texto || errno == ERANGE || valor < INT_MIN || valor > INT_MAX) {
        return 0;
    }
    while (isspace((unsigned char)*fim)) {
        fim++;
    }
    if (*fim != '\0') {
        return 0;
    }
    *resultado = (int)valor;
    return 1;
}

int main(void) {
    int quantidade;
    if (converter_int(" 120\n", &quantidade)) {
        printf("Quantidade: %d\n", quantidade); // Quantidade: 120
    } else {
        puts("Inteiro invalido ou fora da faixa.");
    }
    return EXIT_SUCCESS;
}
```

`fim == texto` indica ausência de conversão; `ERANGE` sinaliza valor fora da faixa de `long`. Os limites `INT_MIN` e `INT_MAX` verificam se o resultado também cabe em `int`, antes do *cast*.

O teste final rejeita restos como `"abc"`. `isspace`, de `<ctype.h>`, reconhece espaçamentos; o *cast* para `unsigned char` mantém seu argumento válido. A função pressupõe que `resultado` aponta para um `int` gravável.

> `errno`, de `<errno.h>`, é zerado antes da conversão porque uma chamada bem-sucedida não precisa apagar erros anteriores. As funções sem sinal, como `strtoul`, também aceitam sinal negativo na entrada; se isso for proibido pela aplicação, o sinal precisa ser validado separadamente.

### Ponto Flutuante - `strtod` e Relacionadas:

`strtod(texto, &fim)` segue a mesma ideia, sem argumento de base. Reconhece representações como `"3.5"` e `"3.5e2"`; o separador decimal depende da configuração regional, sendo `.` na configuração inicial `"C"`.

```c
// Cabeçalhos: stdlib.h, stdio.h e errno.h.
const char *texto = "3.5e2";
char *fim;
errno = 0;
double valor = strtod(texto, &fim);
if (fim == texto || *fim != '\0' || errno == ERANGE) {
    puts("Conversao invalida ou erro de faixa.");
} else {
    printf("%.1f\n", valor); // 350.0
}
```

Esse exemplo exige o fim imediato da *string*, portanto rejeita espaços finais. `strtof` e `strtold` atendem aos outros tipos de ponto flutuante.

`ERANGE` indica erro de faixa; valores pequenos demais também podem provocar essa sinalização. Representações de infinito e NaN podem ser aceitas: exigir um número finito é uma validação adicional, com recursos como `isfinite`, de `<math.h>`.

## Alocação Dinâmica:

As funções abaixo administram blocos de memória cujo tempo de vida é controlado pelo programa. A organização da memória e os conceitos de propriedade e tempo de vida foram apresentados na apostila de C.

| Função | Operação |
|---|---|
| `malloc(bytes)` | Aloca um bloco sem inicializar seu conteúdo |
| `calloc(quantidade, tamanho)` | Aloca espaço para os elementos e zera todos os bits |
| `realloc(ponteiro, bytes)` | Redimensiona um bloco, podendo movê-lo |
| `free(ponteiro)` | Libera o bloco; não retorna valor |

Para tamanhos positivos, as três funções de alocação retornam `NULL` na falha. Em C, o retorno `void *` pode ser atribuído diretamente ao ponteiro adequado, sem *cast*.

### Alocação, Expansão e Liberação:

O exemplo cria quatro contadores zerados, amplia o espaço para seis e inicializa a parte acrescentada.

```c
#include <stdlib.h>
#include <stdio.h>

int main(void) {
    size_t quantidade = 4, nova_quantidade = 6;
    int *contadores = calloc(quantidade, sizeof *contadores);
    if (contadores == NULL) {
        return EXIT_FAILURE;
    }
    contadores[0] = 3;
    int *novo = realloc(contadores, nova_quantidade * sizeof *contadores);
    if (novo == NULL) {
        free(contadores);
        return EXIT_FAILURE;
    }
    contadores = novo;
    for (size_t i = quantidade; i < nova_quantidade; i++) {
        contadores[i] = 0;
    }
    quantidade = nova_quantidade;
    for (size_t i = 0; i < quantidade; i++) {
        printf("%d ", contadores[i]); // 3 0 0 0 0 0
    }
    putchar('\n');
    free(contadores);
    return EXIT_SUCCESS;
}
```

`realloc` preserva o conteúdo até o menor dos tamanhos antigo e novo; a parte acrescentada não é inicializada. Se falhar com tamanho positivo, o bloco original permanece válido. Por isso, o retorno passa por um ponteiro temporário.

Após o sucesso, o acesso deve utilizar o ponteiro retornado: o ponteiro anterior e eventuais referências para dentro do bloco deixam de ser válidos, mesmo se o endereço numérico não mudar.

`malloc(quantidade * sizeof *contadores)` seria uma alternativa à alocação inicial, mas exigiria inicializar os contadores antes de lê-los. `calloc` zera os bits; isso produz zero nos inteiros, mas não garante universalmente ponteiros nulos ou zero de ponto flutuante.

> Para quantidades variáveis, a multiplicação precisa caber em `size_t`: antes de `quantidade * sizeof *ponteiro`, a condição é `quantidade <= SIZE_MAX / sizeof *ponteiro`, com `SIZE_MAX` de `<stdint.h>`. Quantidades zero podem ser tratadas separadamente; em C17, `realloc(p, 0)` não substitui `free(p)` de maneira portável.

`free(NULL)` não faz nada. Para os demais valores, `free` recebe o início de um bloco válido ainda não liberado. A função não altera a variável ponteiro; atribuir `NULL` depois pode evitar seu reaproveitamento acidental, mas não corrige outras referências ao bloco.

## Ordenação e Busca - `qsort` e `bsearch`:

Essas funções trabalham com *arrays* de diferentes tipos. O programa informa o endereço inicial, a quantidade de elementos, o tamanho de cada elemento e uma **função comparadora**.

| Retorno do Comparador | Relação Entre os Elementos |
|---|---|
| Negativo | Primeiro vem antes do segundo |
| Zero | Equivalentes pelo critério escolhido |
| Positivo | Primeiro vem depois do segundo |

O comparador recebe `const void *`, interpreta os objetos conforme seu tipo e define a ordem. Não precisa retornar exatamente `-1` ou `1`: o sinal é suficiente.

```c
#include <stdlib.h>
#include <stdio.h>

int comparar_int(const void *a, const void *b) {
    int x = *(const int *)a;
    int y = *(const int *)b;
    return (x > y) - (x < y);
}

int main(void) {
    int valores[] = {40, 10, 30, 20};
    size_t quantidade = sizeof valores / sizeof valores[0];
    qsort(valores, quantidade, sizeof valores[0], comparar_int);
    for (size_t i = 0; i < quantidade; i++) {
        printf("%d ", valores[i]); // 10 20 30 40
    }
    putchar('\n');
    int chave = 30;
    int *encontrado = bsearch(&chave, valores, quantidade,
                             sizeof valores[0], comparar_int);
    if (encontrado != NULL) {
        printf("Encontrado: %d\n", *encontrado); // Encontrado: 30
    }
    return EXIT_SUCCESS;
}
```

`qsort` modifica o próprio *array* e não retorna valor. Apesar do nome, C não exige uma implementação por *quicksort* nem garante estabilidade: elementos equivalentes podem trocar de ordem.

`bsearch` procura uma chave no *array*, que deve estar organizado conforme o critério de comparação usado na busca. Retorna um ponteiro para um elemento correspondente ou `NULL`; entre duplicatas, não garante o primeiro ou o último.

> `return x - y` pode estourar a faixa de `int`. A expressão `(x > y) - (x < y)` compara sem esse risco. Para estruturas, o mesmo princípio permite comparar um campo; na busca, o comparador recebe primeiro a chave e depois um elemento do *array*.

## Operações Inteiras - Valor Absoluto e Divisão:

| Função | Tipos Utilizados | Resultado |
|---|---|---|
| `abs`, `labs`, `llabs` | `int`, `long`, `long long` | Valor absoluto no mesmo tipo |
| `div`, `ldiv`, `lldiv` | `int`, `long`, `long long` | Estrutura com quociente e resto |

As estruturas `div_t`, `ldiv_t` e `lldiv_t` possuem os membros `quot` e `rem`. São úteis quando as duas partes da divisão interessam, como ao repartir uma quantidade em grupos.

```c
int pecas = 17;
div_t caixas = div(pecas, 5);
printf("Caixas completas: %d; restantes: %d\n", caixas.quot, caixas.rem);
// Caixas completas: 3; restantes: 2
printf("Modulo: %d\n", abs(-7)); // Modulo: 7
```

O quociente é truncado em direção a zero, como `/` entre inteiros em C17. O resto não nulo tem o sinal do numerador: `div(-17, 5)` produz quociente `-3` e resto `-2`.

> O resultado precisa caber no tipo. O valor absoluto de `INT_MIN` pode não ser representável; divisão por zero e quociente fora da faixa também causam comportamento indefinido. Para ponto flutuante, o valor absoluto é obtido com a família `fabs`, de `<math.h>`.

## Números Pseudoaleatórios - `rand` e `srand`:

`rand()` produz um inteiro entre `0` e `RAND_MAX`, inclusive. `srand(semente)` inicializa a sequência; a mesma semente reproduz os mesmos resultados na mesma implementação.

```c
srand(1234); // Semente fixa: permite repetir o teste.
for (int i = 0; i < 3; i++) {
    printf("%d\n", rand()); // Os valores exatos dependem da implementação.
}
```

Sem `srand`, a sequência equivale à inicializada com semente `1`. A inicialização normalmente ocorre uma vez, antes da geração; reiniciá-la a cada chamada pode repetir os mesmos valores.

A expressão `rand() % 6 + 1` produz valores de `1` a `6`, mas pode introduzir viés: alguns resultados recebem mais possibilidades de origem que outros. A biblioteca também não garante uma distribuição de alta qualidade.

> `rand` atende a exemplos e testes simples, mas não é adequado para senhas, *tokens* ou outros usos que exijam imprevisibilidade.

## Encerramento do Programa:

`EXIT_SUCCESS` e `EXIT_FAILURE` representam estados portáveis de sucesso e falha. Podem ser usados no retorno de `main`, como nos exemplos, ou como argumento de `exit`.

`exit(estado)` encerra o programa inteiro, mesmo quando chamado fora de `main`. Executa as funções registradas com `atexit`, encaminha a saída pendente dos fluxos e fecha os arquivos abertos. Um `return` em uma função comum apenas devolve o controle à função chamadora.

`atexit(funcao)` registra uma função sem argumentos e sem retorno para o encerramento normal. Retorna zero no sucesso, e as funções registradas são executadas na ordem inversa do registro.

```c
#include <stdlib.h>
#include <stdio.h>

void finalizar(void) {
    puts("Encerramento concluido.");
}

int main(void) {
    if (atexit(finalizar) != 0) {
        return EXIT_FAILURE;
    }
    puts("Processamento concluido.");
    return EXIT_SUCCESS; // Depois, executar finalizar.
}
```

O registro não substitui a liberação de recursos quando deixam de ser necessários durante a execução. Já `abort()` provoca término anormal e não executa as funções registradas com `atexit`.

---

# 3. Classificação e Conversão de Caracteres - `<ctype.h>`

O cabeçalho `<ctype.h>` permite identificar propriedades de caracteres e converter entre maiúsculas e minúsculas. As operações são individuais; uma *string* é processada percorrendo seus caracteres.

As funções recebem `int`, mas o argumento precisa ser `EOF` ou um valor representável por `unsigned char`. Para caracteres obtidos de uma *string*, a forma usual é `funcao((unsigned char)texto[i])`, evitando valores negativos quando `char` possui sinal.

## Classificação:

As funções de classificação retornam **zero quando a condição é falsa** e **um valor não zero quando é verdadeira**. Por isso, podem ser utilizadas diretamente em condições, como `if (isdigit(c))`.

| Função | Verifica se o Caractere É | Exemplos Verdadeiros |
|---|---|---|
| `isalpha(c)` | Letra | `'A'`, `'b'` |
| `isdigit(c)` | Dígito decimal | `'0'`, `'8'` |
| `isalnum(c)` | Letra ou dígito decimal | `'A'`, `'7'` |
| `isupper(c)` | Letra maiúscula | `'F'` |
| `islower(c)` | Letra minúscula | `'c'` |
| `isxdigit(c)` | Dígito hexadecimal | `'9'`, `'A'`, `'f'` |
| `isspace(c)` | Espaçamento | `' '`, `'\t'`, `'\n'` |
| `isblank(c)` | Espaçamento horizontal | `' '`, `'\t'` |
| `ispunct(c)` | Caractere imprimível que não é alfanumérico nem espaço | `'!'`, `'_'` |
| `isprint(c)` | Caractere imprimível, incluindo espaço | `'A'`, `'!'`, `' '` |
| `isgraph(c)` | Caractere imprimível, exceto espaço | `'A'`, `'!'` |
| `iscntrl(c)` | Caractere de controle | `'\n'`, `'\t'` |

Na configuração inicial `"C"`, `isspace` reconhece espaço, `\t`, `\n`, `\r`, `\f` e `\v`; `isblank` reconhece apenas espaço e tabulação horizontal.

`isdigit` verifica um **caractere**, não um número completo. Assim, `'-'` e `'.'` não são dígitos, embora possam aparecer em representações numéricas como `"-3.5"`.

## Conversão Entre Maiúsculas e Minúsculas:

| Função | Operação |
|---|---|
| `toupper(c)` | Retorna a maiúscula correspondente, quando aplicável |
| `tolower(c)` | Retorna a minúscula correspondente, quando aplicável |

Ambas retornam `int`. Quando não existe conversão aplicável, o valor é devolvido sem alteração. A função não modifica a variável recebida: a mudança precisa ser armazenada.

```c
printf("%c %c %c\n", toupper('u'), tolower('W'), toupper('7'));
// U w 7
```

Não é necessário testar `islower` antes de `toupper`, nem `isupper` antes de `tolower`. As próprias funções tratam os caracteres que não precisam de conversão.

### Classificação e Conversão de uma *String*:

O exemplo conta letras, dígitos e espaçamentos enquanto transforma o texto em maiúsculas. As comparações com zero normalizam os resultados para `0` ou `1`, permitindo somá-los aos contadores.

```c
#include <ctype.h>
#include <stdio.h>

int main(void) {
    char texto[] = "Peca A7: 12 unidades.";
    int letras = 0, digitos = 0, espacos = 0;
    for (size_t i = 0; texto[i] != '\0'; i++) {
        unsigned char c = (unsigned char)texto[i];
        letras += isalpha(c) != 0;
        digitos += isdigit(c) != 0;
        espacos += isspace(c) != 0;
        texto[i] = (char)toupper(c);
    }
    puts(texto); // PECA A7: 12 UNIDADES.
    printf("Letras: %d; digitos: %d; espacos: %d\n",
           letras, digitos, espacos);
    // Letras: 13; digitos: 3; espacos: 3
    return 0;
}
```

O *array* `texto` é modificável. Alterar diretamente uma *string* literal, como a apontada por `char *texto = "Peca"`, causa comportamento indefinido.

## Codificação e Limites:

A classificação e a conversão podem depender da configuração regional (*locale*). Os exemplos utilizam caracteres básicos, compatíveis com a configuração inicial `"C"`.

Essas funções não processam automaticamente caracteres Unicode compostos por vários bytes. Em UTF-8, uma letra acentuada pode ocupar mais de um byte; aplicar `toupper` separadamente a cada byte não realiza a conversão completa dessa letra.

> Para resultados de `getchar` ou `fgetc`, o valor permanece em `int` até a verificação de `EOF`. Convertê-lo antes para `unsigned char` elimina a distinção entre esse indicador e um byte válido.

---

# 4. *Strings* e Blocos de Memória - `<string.h>`

O cabeçalho `<string.h>` reúne operações de cópia, concatenação, comparação e busca. As funções para *strings* reconhecem `'\0'` como terminador; as operações `mem...` trabalham com uma quantidade explícita de bytes, inclusive bytes de valor zero.

Essas funções não alocam automaticamente o destino. O espaço disponível, a validade dos ponteiros e os limites de acesso permanecem sob responsabilidade do programa. Os exemplos utilizam `<string.h>` e `<stdio.h>`.

## Comprimento - `strlen`:

`strlen(texto)` retorna um `size_t` com a quantidade de bytes anteriores ao primeiro `'\0'`. Não inclui o terminador nem informa a capacidade do *buffer*.

```c
char texto[20] = "Feliz";
printf("Comprimento: %zu; capacidade: %zu\n", strlen(texto), sizeof texto);
// Comprimento: 5; capacidade: 20
```

`sizeof` fornece a capacidade nesse caso porque `texto` é um *array*; aplicado a um ponteiro, fornece o tamanho do ponteiro. `strlen` exige uma *string* terminada dentro da memória acessível.

> Em UTF-8, uma letra pode ocupar vários bytes. Portanto, o resultado de `strlen` não representa necessariamente a quantidade de caracteres percebidos na leitura.

## Cópia e Concatenação:

Os argumentos seguem a ordem **destino, origem**. As funções abaixo retornam o endereço do destino.

| Função | Operação | Terminador |
|---|---|---|
| `strcpy(destino, origem)` | Copia toda a *string* | Copia também `'\0'` |
| `strncpy(destino, origem, n)` | Escreve `n` posições, copiando até o fim da origem e completando com zeros | Pode não acrescentar `'\0'` se a origem tiver pelo menos `n` bytes antes do terminador |
| `strcat(destino, origem)` | Acrescenta a origem ao final do destino | Inclui `'\0'` ao final |
| `strncat(destino, origem, n)` | Acrescenta até `n` bytes da origem | Acrescenta também `'\0'` |

Para concatenar, o destino já precisa conter uma *string* válida, mesmo que vazia: `char texto[32] = "";`. A cópia e a concatenação exigem regiões de origem e destino sem sobreposição.

```c
char mensagem[32], inicio[6];
strcpy(mensagem, "Feliz");
strcat(mensagem, " aniversario"); // Os textos conhecidos cabem no destino.
strncat(mensagem, "!", sizeof mensagem - strlen(mensagem) - 1);
strncpy(inicio, mensagem, sizeof inicio - 1);
inicio[sizeof inicio - 1] = '\0';
printf("%s | %s\n", mensagem, inicio);
// Feliz aniversario! | Feliz
```

`strncpy` não é uma versão que simplesmente garante uma cópia segura: se o limite for atingido antes do terminador, o resultado não será uma *string* terminada. No exemplo, a última posição é preenchida explicitamente com `'\0'`.

Em `strncat`, `n` limita o conteúdo acrescentado, **não a capacidade total do destino**. A expressão `capacidade - strlen(destino) - 1` calcula o espaço restante, desde que o destino já seja uma *string* válida dentro desse *buffer*.

Limitar a cópia pode produzir truncamento. Quando o texto completo for necessário, a capacidade deve ser verificada antes da operação; para `strcat`, ela precisa comportar `strlen(destino) + strlen(origem) + 1` bytes.

## Comparação - `strcmp` e `strncmp`:

`strcmp(a, b)` compara *strings* até a primeira diferença ou o terminador. `strncmp(a, b, n)` compara no máximo `n` bytes, também respeitando o fim das *strings*.

| Retorno | Significado |
|---|---|
| Negativo | `a` vem antes de `b` pela ordem dos bytes |
| Zero | Conteúdo equivalente na parte comparada |
| Positivo | `a` vem depois de `b` pela ordem dos bytes |

```c
printf("Iguais: %d\n", strcmp("Abc", "Abc") == 0); // Iguais: 1
printf("Prefixo igual: %d\n", strncmp("Abc", "Abd", 2) == 0); // Prefixo igual: 1
printf("Diferentes: %d\n", strcmp("Casa", "casa") != 0); // Diferentes: 1
```

A comparação distingue maiúsculas de minúsculas e interpreta os bytes como `unsigned char`. O resultado não precisa ser exatamente `-1` ou `1`, e essa ordem não equivale necessariamente à ordem alfabética de um idioma.

> `a == b`, quando ambos são ponteiros, compara endereços. Para verificar o conteúdo das *strings*, a condição é `strcmp(a, b) == 0`. Um prefixo igual com `strncmp` não garante igualdade completa.

## Busca de Caracteres e Trechos:

| Função | Procura |
|---|---|
| `strchr(texto, c)` | Primeira ocorrência do caractere |
| `strrchr(texto, c)` | Última ocorrência do caractere |
| `strstr(texto, trecho)` | Primeira ocorrência da *substring* |

As três retornam um ponteiro para a ocorrência ou `NULL` quando não encontram. O ponteiro referencia a própria *string*: não há criação de uma cópia.

```c
const char *nome = "arquivo.tar.gz";
const char *primeiro = strchr(nome, '.');
const char *ultimo = strrchr(nome, '.');
const char *trecho = strstr(nome, "tar");
if (primeiro != NULL) printf("%s\n", primeiro); // .tar.gz
if (ultimo != NULL) printf("%s\n", ultimo);     // .gz
if (trecho != NULL) printf("%s\n", trecho);     // tar.gz
```

A impressão continua até o terminador original. Se a intenção for modificar o conteúdo encontrado, a *string* de origem precisa estar em memória modificável; uma *string* literal não pode ser alterada.

## Operações Sobre Blocos de Memória:

As funções `mem...` não interpretam *strings* nem param em `'\0'`. Seus limites são expressos em **bytes**, permitindo trabalhar com *arrays* e outras representações em memória.

| Função | Operação |
|---|---|
| `memcpy(destino, origem, n)` | Copia `n` bytes; as regiões não podem se sobrepor |
| `memmove(destino, origem, n)` | Copia `n` bytes, permitindo sobreposição |
| `memset(destino, valor, n)` | Preenche `n` bytes com o valor convertido para `unsigned char` |
| `memcmp(a, b, n)` | Compara `n` bytes e retorna negativo, zero ou positivo |

`memcpy`, `memmove` e `memset` retornam o endereço do destino. Origem e destino precisam permitir os acessos indicados pelo tamanho; `memmove` trata a sobreposição, mas não amplia o *buffer*.

```c
unsigned char origem[] = {0x10, 0x00, 0x20};
unsigned char copia[sizeof origem];
memcpy(copia, origem, sizeof origem); // Copia também o byte de valor zero.
printf("Blocos iguais: %d\n", memcmp(origem, copia, sizeof origem) == 0);
// Blocos iguais: 1
memset(copia, 0, sizeof copia); // Todos os bytes passam a zero.

char texto[] = "ABCDE";
memmove(texto, texto + 2, strlen(texto + 2) + 1); // Inclui o terminador.
puts(texto); // CDE
```

Na última operação, origem e destino pertencem ao mesmo *array* e se sobrepõem. `memmove` preserva os bytes necessários durante a cópia; substituir essa chamada por `memcpy` causaria comportamento indefinido.

`memset` repete um **byte**, não um valor do tipo dos elementos. Portanto, `memset(vetor, 1, sizeof vetor)` não preenche um *array* de `int` com o número `1`. Zerar os bits também não garante universalmente ponteiros nulos ou zero de ponto flutuante.

> `memcmp` compara representações, não valores abstratos. Estruturas com campos iguais podem ter bytes de preenchimento diferentes; para comparar seus dados, a comparação dos campos é mais adequada.

---

# 5. Tipos Inteiros Padronizados - `<stdint.h>`

O cabeçalho `<stdint.h>` fornece nomes de tipos inteiros com requisitos explícitos de largura e faixa. Isso permite definir interfaces e estruturas sem presumir, por exemplo, que `int` sempre possui 32 bits.

Os nomes são definidos por `typedef`: correspondem a tipos inteiros oferecidos pela implementação, mantendo as regras de operações e conversões de C.

## Largura Exata:

| Com Sinal | Sem Sinal | Largura |
|---|---|---|
| `int8_t` | `uint8_t` | 8 bits |
| `int16_t` | `uint16_t` | 16 bits |
| `int32_t` | `uint32_t` | 32 bits |
| `int64_t` | `uint64_t` | 64 bits |

Por exemplo, `int8_t` representa valores de `-128` a `127`, enquanto `uint8_t` representa de `0` a `255`. Esses tipos não possuem bits de preenchimento; os de largura exata com sinal usam complemento de dois em C17.

> Os tipos de largura exata são opcionais: existem quando a implementação oferece os tipos correspondentes. Os exemplos deste capítulo pressupõem sua disponibilidade. A largura fixa não elimina promoções de inteiros nem protege contra estouro.

## Outras Famílias:

| Família | Finalidade | Exemplo |
|---|---|---|
| `int_leastN_t` / `uint_leastN_t` | Menor tamanho disponível com pelo menos `N` bits | `uint_least16_t` |
| `int_fastN_t` / `uint_fastN_t` | Tipo escolhido pela implementação para favorecer operações com pelo menos `N` bits | `int_fast32_t` |
| `intmax_t` / `uintmax_t` | Representar qualquer valor dos tipos inteiros com sinal / sem sinal | `uintmax_t` |
| `intptr_t` / `uintptr_t` | Permitir converter um `void *` para inteiro e de volta, preservando a igualdade do ponteiro | `uintptr_t` |

As famílias `least` e `fast` existem para `8`, `16`, `32` e `64` bits. `fast` não garante o melhor desempenho em toda operação, e seu tamanho pode superar o mínimo indicado. `intptr_t` e `uintptr_t` também são opcionais.

## Limites e Constantes:

Os limites acompanham os nomes dos tipos: `INT32_MIN`, `INT32_MAX`, `UINT16_MAX`, `INT_FAST32_MAX` e assim por diante. Tipos sem sinal começam em zero. O cabeçalho também fornece `SIZE_MAX`, limite de `size_t`.

As *macros* `INT32_C(valor)` e `UINT64_C(valor)`, por exemplo, formam constantes adequadas às famílias de largura mínima correspondentes, considerando as promoções inteiras. Elas permitem expressar constantes sem escolher manualmente sufixos como `L` ou `ULL`.

## Entrada e Saída - Apoio de `<inttypes.h>`:

Um `uint64_t` pode corresponder a tipos fundamentais diferentes conforme a plataforma. `<inttypes.h>` fornece *macros* de formato que acompanham essa escolha, como `PRId32`, `PRIu64` e `PRIx32` para saída decimal com sinal, decimal sem sinal e hexadecimal.

```c
#include <stdint.h>
#include <inttypes.h>
#include <stdio.h>

int main(void) {
    uint32_t codigo = UINT32_C(123456);
    uint64_t total = UINT64_C(5000000000);
    printf("Codigo: %" PRIu32 "; total: %" PRIu64 "\n", codigo, total);
    // Codigo: 123456; total: 5000000000
    if (codigo < UINT32_MAX) {
        codigo++; // Verifica o limite antes do incremento.
    }
    return 0;
}
```

Em `"%" PRIu32`, a *macro* fornece parte do formato, e os literais adjacentes são unidos durante a tradução. Para leitura, existem *macros* como `SCNd32` e `SCNu64`: `scanf("%" SCNu64, &total)`. Permanecem os cuidados de retorno e faixa apresentados em `<stdio.h>`.

> Largura fixa ajuda a definir os dados, mas não padroniza a ordem dos bytes nem os preenchimentos entre campos de uma `struct`. Um formato binário portável ainda precisa especificar como os valores serão gravados.

---

# 6. Funções Matemáticas - `<math.h>`

O cabeçalho `<math.h>` reúne operações matemáticas e classificação de valores de ponto flutuante. Em geral, a versão sem sufixo trabalha com `double`, a versão com `f` usa `float` e a versão com `l` usa `long double`: `sqrt`, `sqrtf` e `sqrtl`.

Os exemplos utilizam `<math.h>` e `<stdio.h>`. Os cálculos seguem a precisão disponível no tipo, podendo produzir aproximações.

## Potências, Raízes e Logaritmos:

| Função | Operação |
|---|---|
| `pow(x, y)` | Potência de base `x` e expoente `y` |
| `sqrt(x)` / `cbrt(x)` | Raiz quadrada / cúbica |
| `exp(x)` | Exponencial de base `e` |
| `log(x)` | Logaritmo natural, de base `e` |
| `log10(x)` / `log2(x)` | Logaritmos nas bases `10` / `2` |
| `hypot(x, y)` | Hipotenusa: raiz de `x*x + y*y`, evitando estouros intermediários indevidos |

```c
printf("Raizes: %.1f e %.1f\n", sqrt(9.0), cbrt(-8.0)); // 3.0 e -2.0
printf("Potencia: %.1f\n", pow(2.0, 3.0)); // 8.0
printf("Logaritmos: %.1f %.1f %.1f\n", log(exp(1.0)), log10(1000.0), log2(8.0));
// Logaritmos: 1.0 3.0 3.0
printf("Hipotenusa: %.1f\n", hypot(3.0, 4.0)); // 5.0
```

`cbrt` aceita argumentos negativos; `pow(x, 1.0 / 3.0)` não é uma substituição geral para isso. A expressão `1 / 3` realiza divisão inteira e produz zero. Para um quadrado simples, `x * x` costuma expressar a operação mais diretamente.

## Arredondamento:

As funções abaixo continuam retornando ponto flutuante, mesmo quando o resultado tem valor inteiro.

| Função | Critério | Para `2.7` | Para `-2.7` |
|---|---|---|---|
| `floor(x)` | Inteiro mais próximo em direção a menos infinito | `2.0` | `-3.0` |
| `ceil(x)` | Inteiro mais próximo em direção a mais infinito | `3.0` | `-2.0` |
| `trunc(x)` | Descarta a parte fracionária, em direção a zero | `2.0` | `-2.0` |
| `round(x)` | Inteiro mais próximo; empates se afastam de zero | `3.0` | `-3.0` |

Por exemplo, `round(2.5)` produz `3.0` e `round(-2.5)` produz `-3.0`. Converter depois para um tipo inteiro ainda exige que o valor seja representável nesse tipo.

## Valor Absoluto e Resto:

`fabs(x)` obtém o valor absoluto de ponto flutuante. `fmod(x, y)` calcula o resto usando um quociente truncado em direção a zero; um resto não nulo tem o sinal de `x`.

```c
printf("Absoluto: %.1f\n", fabs(-3.5)); // 3.5
printf("Restos: %.1f e %.1f\n", fmod(7.5, 2.0), fmod(-7.5, 2.0));
// Restos: 1.5 e -1.5
```

O operador `%` é reservado a inteiros. `fmod` permite trabalhar com valores fracionários, mas não garante um resto positivo quando o primeiro argumento é negativo.

## Trigonometria:

| Funções | Operação |
|---|---|
| `sin(x)`, `cos(x)`, `tan(x)` | Seno, cosseno e tangente; argumento em radianos |
| `asin(x)`, `acos(x)`, `atan(x)` | Funções inversas; resultado em radianos |
| `atan2(y, x)` | Ângulo da direção `(x, y)`, considerando o quadrante |

```c
const double pi = acos(-1.0);
double angulo = 30.0 * pi / 180.0;
printf("Seno: %.2f\n", sin(angulo)); // 0.50
printf("Direcao: %.1f graus\n", atan2(1.0, 1.0) * 180.0 / pi); // 45.0 graus
```

A conversão de graus para radianos multiplica por `pi / 180`; a operação inversa multiplica por `180 / pi`. `atan2(y, x)` preserva a informação do quadrante que pode se perder em `atan(y / x)`.

> `M_PI` é uma extensão comum, mas não é garantida pelo padrão C17. `acos(-1.0)` fornece uma aproximação de pi sem depender dessa *macro*.

## Domínio, Precisão e Valores Especiais:

Algumas operações impõem restrições: `sqrt` exige argumento não negativo para resultado real, os logaritmos exigem argumento positivo e `asin`/`acos` recebem valores entre `-1` e `1`.

| *Macro* | Verifica se o Valor É |
|---|---|
| `isfinite(x)` | Finito, sem ser infinito nem NaN |
| `isinf(x)` | Infinito |
| `isnan(x)` | NaN (*Not a Number*) |

Esses testes retornam zero ou um valor não zero. NaN não é identificado com `x == NAN`; a verificação apropriada é `isnan(x)`.

Erros de domínio e resultados fora da faixa podem envolver sinalização por `errno`, exceções de ponto flutuante ou valores especiais, conforme a implementação. Validar as entradas evita depender apenas do resultado para reconhecer uma operação inadequada.

Resultados aproximados também exigem cuidado nas comparações: uma tolerância deve considerar a escala e o erro aceitável da aplicação. Arredondar a impressão com `printf` não altera o valor armazenado.

## Compilação:

Em ambientes como GCC no Linux, a ligação com a biblioteca matemática normalmente utiliza `-lm`, colocado após o arquivo que usa suas funções:

```sh
gcc -std=c17 programa.c -o programa -lm
```

Essa opção pertence à organização da ferramenta e do sistema, não à sintaxe de C; outros ambientes podem dispensá-la.

---

# 7. Argumentos Variáveis - `<stdarg.h>`

O cabeçalho `<stdarg.h>` permite acessar os argumentos de uma função variádica, cuja quantidade de argumentos pode mudar entre chamadas. A declaração termina com `...` e, em C17, precisa ter pelo menos um parâmetro nomeado antes das reticências.

A função não descobre automaticamente quantos argumentos recebeu nem seus tipos. Essas informações precisam seguir uma convenção, como uma contagem explícita ou uma *string* de formato, caso de `printf`.

## Recursos Principais:

| Recurso | Finalidade |
|---|---|
| `va_list` | Tipo que mantém o estado de acesso aos argumentos |
| `va_start(lista, ultimo)` | Inicializa o acesso; `ultimo` é o último parâmetro nomeado |
| `va_arg(lista, tipo)` | Obtém o próximo argumento no tipo informado e avança a leitura |
| `va_copy(destino, origem)` | Inicializa uma cópia independente do estado atual |
| `va_end(lista)` | Encerra o uso da lista inicializada |

O acesso ocorre sequencialmente. `va_list` não deve ser tratado como um *array* ou como um ponteiro comum; sua representação depende da implementação.

## Exemplo - Média de Valores:

A quantidade é informada no primeiro argumento. Os argumentos seguintes precisam ser fornecidos como `double`, incluindo valores `float` promovidos automaticamente.

```c
#include <stdarg.h>
#include <stdio.h>

double media(int quantidade, ...) {
    if (quantidade <= 0) {
        return 0.0; // Convencao deste exemplo para quantidade invalida.
    }
    double total = 0.0;
    va_list argumentos;
    va_start(argumentos, quantidade);
    for (int i = 0; i < quantidade; i++) {
        total += va_arg(argumentos, double);
    }
    va_end(argumentos);
    return total / quantidade;
}

int main(void) {
    printf("%.3f\n", media(2, 38.5, 22.5));           // 30.500
    printf("%.3f\n", media(3, 38.5, 22.5, 1.7));      // 20.900
    printf("%.3f\n", media(4, 38.5, 22.5, 1.7, 10.2)); // 18.225
    return 0;
}
```

O acumulador permanece local à função. O retorno `0.0` para uma quantidade inválida é apenas uma decisão do exemplo, não uma definição matemática de média sem elementos.

## Tipos, Promoções e Encerramento:

Os argumentos da parte `...` sofrem as **promoções padrão**:

- `float` é promovido a `double`.
- `char` e `short`, com ou sem sinal, são promovidos a `int` ou `unsigned int`, conforme a faixa representável.
- `_Bool` é promovido a `int`.

Assim, `media(2, 10.0, 20.0)` corresponde ao tipo esperado. Já `media(2, 10, 20)` passa inteiros: `va_arg(argumentos, double)` não converte esses valores e causa comportamento indefinido.

Ler além dos argumentos fornecidos também causa comportamento indefinido. O tipo solicitado deve corresponder ao argumento após as promoções, não apenas possuir o mesmo tamanho em bytes.

Cada inicialização com `va_start` ou `va_copy` exige um `va_end` correspondente na mesma função. Para percorrer os argumentos por uma segunda lista, utiliza-se `va_copy`; uma atribuição direta entre objetos `va_list` não é portável.

> `va_end` não libera os objetos passados pelo chamador nem necessariamente zera algum ponteiro. A *macro* encerra o estado de acesso e deve ser usada mesmo quando a implementação não exige uma operação visível.

---

# 8. Valores Booleanos - `<stdbool.h>`

Em C17, `<stdbool.h>` fornece nomes mais legíveis para representar condições lógicas. O tipo fundamental é `_Bool`; o cabeçalho define *macros* para sua utilização.

| *Macro* | Expansão em C17 |
|---|---|
| `bool` | `_Bool` |
| `true` | Constante inteira `1` |
| `false` | Constante inteira `0` |

Ao converter um valor numérico para `bool`, zero produz `false` e qualquer valor diferente de zero produz `true`. O valor armazenado fica normalizado para `0` ou `1`.

```c
#include <stdbool.h>
#include <stdio.h>

int main(void) {
    int estoque = 4;
    bool disponivel = estoque > 0;
    bool bloqueado = false;
    bool permitido = disponivel && !bloqueado;
    printf("Disponivel: %d\n", disponivel); // Disponivel: 1
    puts(permitido ? "Venda permitida" : "Venda bloqueada");
    // Venda permitida
    bool ativo = -7;
    printf("Ativo: %d\n", ativo); // Ativo: 1
    return 0;
}
```

Os operadores `!`, `&&` e `||` mantêm seu funcionamento habitual. A contribuição do cabeçalho é tornar explícita a intenção de representar estados como disponível, válido ou concluído.

`printf` pode utilizar `%d` porque `bool` é promovido a `int`. Para leitura, `%d` não pode receber um `bool *`: a entrada deve ser lida em um `int` e depois convertida, com validação quando apenas `0` e `1` forem aceitos.

> Um objeto `bool` não ocupa necessariamente um único bit; seu tamanho depende da implementação. O cabeçalho também não altera as regras das condições de C, que já interpretam zero como falso e valores não zero como verdadeiros.

---

# Fontes:

- DEITEL, Harvey M.; DEITEL, Paul J. *Como programar em C*. 2. ed. Rio de Janeiro: LTC, 1994.
- PEIXOTO, Daniela Cristina Cascini. *Disciplina: Lógica de programação*. Curso de graduação em Engenharia de Computação. Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2024.
- BATISTA, Natália Cosse. *Disciplina: Algoritmos e estruturas de dados*. Curso de graduação em Engenharia de Computação. Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2025.
- CPPREFERENCE.COM. *C reference*. [S. l.], [s. d.]. Disponível em: [https://en.cppreference.com/w/c](https://en.cppreference.com/w/c). Acesso em: 4 ago. 2026.
- FREE SOFTWARE FOUNDATION. *The GNU C Library Reference Manual*. Versão 2.42. [S. l.]: Free Software Foundation, 2025. Disponível em: [https://sourceware.org/glibc/manual/2.42/html_node/index.html](https://sourceware.org/glibc/manual/2.42/html_node/index.html). Acesso em: 26 set. 2026.
- ISO/IEC JTC 1/SC 22/WG 14. *Programming languages - C*. Committee Draft N1570, 12 abr. 2011. Disponível em: [https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf](https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf). Acesso em: 26 set. 2026.
