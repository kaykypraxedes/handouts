```
 .´‾‾‾‾‾‾‾`.
/     _____|
▏   /        
▏   ▏      
▏   \      
\     ‾‾‾‾‾|
 `.______.´
```

# 0. Introduction

> This handout uses **C17** as its reference.

The C standard library brings together functions, types, and macros for operations such as input and output, string manipulation, allocation, and mathematical calculations. Its facilities are organized into **headers**, added through `#include`.

The header presents declarations to the compiler; the implementation of functions is provided by the library.

The fragments assume inclusion of the corresponding header and can be placed in `main` unless otherwise indicated. Using each function involves three considerations: **argument types, return value, and bounds of the memory used**.

---

# 1. Input and Output - `<stdio.h>`

The `<stdio.h>` header groups operations on **streams**: sequences of data received or produced by the program. Three streams are available when programs run in hosted environments:

| Stream | Purpose | Usual Association |
|---|---|---|
| `stdin` | Standard input | Keyboard |
| `stdout` | Standard output | Terminal |
| `stderr` | Errors and diagnostics | Terminal |

These associations can change through redirection. Thus, `printf` writes to `stdout` even when output is directed to a file.

## Formatted Output - `printf`:

`printf(formato, ...)` combines text and specifiers corresponding to the arguments in the same order. It returns the number of characters written or a negative value on error.

| Format | Use in `printf` |
|---|---|
| `%d` / `%i` | `int` in decimal |
| `%u`, `%o`, `%x` / `%X` | `unsigned int` in decimal, octal, or hexadecimal |
| `%ld` / `%lu` | `long` / `unsigned long` |
| `%lld` / `%llu` | `long long` / `unsigned long long` |
| `%zu` | `size_t`, such as the result of `sizeof` |
| `%f`, `%e`, `%g` | `double`: decimal, scientific, or a choice between the two |
| `%Lf` | `long double` |
| `%c` / `%s` | Character received as `int` / string terminated by `'\0'` |
| `%p` | Pointer converted to `void *` |
| `%%` | `%` symbol, without consuming an argument |

**Width** defines a minimum field; `-` aligns to the left, and `0` allows numeric padding with zeros. **Precision** in `%f` specifies decimal places; in `%s`, it limits the bytes written. `#` requests alternative forms, such as `0x` in nonzero hexadecimal.

```c
const char *produto = "Resistor";
int quantidade = 12;
double preco = 0.35;
printf("%-10s | %04d | %6.2f\n", produto, quantidade, preco);
// Resistor   | 0012 |   0.35
printf("Hexadecimal: %#x; progresso: %d%%\n", 42u, 75);
// Hexadecimal: 0x2a; progresso: 75%
```

Width does not cut off larger values: `%4d` prints all digits of `123456`. Formatting changes the presentation, not the variable. `float` values are promoted to `double` in this call, allowing `%f` for both.

> Formats incompatible with the arguments can cause undefined behavior. To display external text, `printf("%s", texto)` keeps the content separate from the format, even if it contains `%`.

## Characters and Simple Output:

| Function | Operation | Return |
|---|---|---|
| `puts(texto)` | Writes to `stdout` and adds `\n`; does not interpret formats | Nonnegative on success; `EOF` on failure |
| `putchar(c)` | Writes a character to `stdout` | Character written; `EOF` on failure |
| `getchar()` | Reads a character from `stdin` | Character read; `EOF` for end of input or error |

In character returns, the value is converted from `unsigned char` to `int`. The result of `getchar` remains in `int` until compared with `EOF`; storing it in `char` beforehand can lose this distinction.

```c
int caractere;
puts("Texto:");
while ((caractere = getchar()) != '\n' && caractere != EOF) {
    putchar(caractere); // Copies the first line.
}
putchar('\n');
```

`EOF` is a return indicator, not a character stored at the end of the text. The comparison uses the macro, whose value need not be `-1`.

## Formatted Input - `scanf`:

`scanf(formato, ...)` reads from `stdin` and stores results at the **received addresses**. It returns the number of assignments performed: `0` indicates that none occurred; `EOF` indicates input failure before completing the first conversion.

The formats resemble those of `printf`, but the arguments are pointers. The most important distinction is `%f` for `float *` and `%lf` for `double *`; `%Lf` receives `long double *`.

```c
char produto[20], categoria;
int quantidade;
double preco;
// Input: Resistor 12 0.35 A
if (scanf("%19s %d %lf %c", produto, &quantidade, &preco, &categoria) == 4) {
    printf("%s: %d unidades a %.2f; categoria %c\n",
           produto, quantidade, preco, categoria);
} else {
    puts("Entrada incompleta ou invalida.");
}
```

The array's name already provides the address for `%s`. The limit `19` reserves space for `'\0'`; this conversion reads only one word, stopping at the next whitespace.

Conversions such as `%d`, `%f`, and `%s` skip leading whitespace; `%c` does not. The space before `%c` in the example consumes pending whitespace. A space in the format can match spaces, tabs, and newlines.

Formats ending in a space or `\n`, such as `"%d\n"`, can keep waiting for another character during interactive input. After a conversion failure, the incompatible character can remain in the stream and cause further failures.

> Checking the return value does not validate the numeric range: a value that does not fit in the destination can cause undefined behavior. `fgets` with conversions such as `strtol`, from `<stdlib.h>`, offers more control over this validation.

## Reading and Parsing Lines - `fgets` and `sscanf`:

`fgets(buffer, capacidade, fluxo)` reads up to `capacidade - 1` characters and adds `'\0'` on success. It stops upon reading `\n`, reaching the limit, or encountering the end of input; the newline is preserved when read.

It returns the buffer's address on success or `NULL` on error or end of input with no characters read. A line larger than the buffer remains partially in the stream. The absence of `\n` can also indicate a final line without a trailing newline.

`sscanf(texto, formato, ...)` parses an already stored string, following the formats and return values of `scanf`. Reading the line and parsing the fields can therefore be separated:

```c
char linha[80], codigo[8];
int quantidade;
double preco;
if (fgets(linha, sizeof linha, stdin) != NULL) {
    // Input: R07 12 0.35
    if (sscanf(linha, "%7s %d %lf", codigo, &quantidade, &preco) == 3) {
        printf("%s: %d unidades a %.2f\n", codigo, quantidade, preco);
    } else {
        puts("Registro incompleto ou invalido.");
    }
} else {
    puts("Nenhuma linha foi obtida.");
}
```

For free text, such as a full name, simply use `linha` directly, without `sscanf`. Parsing does not modify the string, and obtaining the three fields does not guarantee the absence of additional content after them. The numeric range considerations of `scanf` also apply.

When alternating `scanf` and `fgets`, a pending newline can be read as an empty line. When the intention is to abandon **the entire remainder of the current line**, discarding it can be explicit:

```c
int caractere;
while ((caractere = getchar()) != '\n' && caractere != EOF) {
    // Discards the remaining characters, including other data on the line.
}
```

> `fflush(stdin)` is not a valid way to clear input in C17. Keeping input organized by lines with `fgets` usually simplifies this control.

## Formatting in Strings - `sprintf` and `snprintf`:

Both format values in a buffer. `sprintf(destino, formato, ...)` depends on sufficient space; `snprintf(destino, capacidade, formato, ...)` limits writing to the specified capacity, including the terminator.

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

Without an error, `snprintf` returns the length the complete text would have, **excluding `'\0'`**. A return value greater than or equal to the capacity indicates truncation. With positive capacity and no error, the stored content ends with `'\0'`.

The capacity must correspond to the available memory. `sizeof etiqueta` works because the object is an array; applied to a pointer, `sizeof` gives the pointer's size. `sprintf` returns the number written, excluding the terminator, or a negative value on error.

## File Handling:

`FILE` represents the control information for a stream: access mode, position, temporary storage, and state indicators.

> A `FILE *` references this control information, not the file's bytes directly.

`fopen(nome, modo)` opens the file and returns `NULL` on failure. `fclose(arquivo)` ends the stream, releases its resources, and attempts to send pending output; it returns `0` on success or `EOF` on failure.

### Opening Modes:

| Mode | Access | If It Does Not Exist | Existing Content |
|---|---|---|---|
| `"r"` | Read | Fails | Preserved |
| `"w"` | Write | Creates | Erased on opening |
| `"a"` | Write at the end | Creates | Preserved |
| `"r+"` | Read and write | Fails | Preserved |
| `"w+"` | Read and write | Creates | Erased on opening |
| `"a+"` | Read; write at the end | Creates | Preserved |

The `b` character selects binary mode: `"rb"`, `"wb"`, `"ab"`, `"r+b"`, `"w+b"`, or `"a+b"`. Text mode can translate representations such as newlines; binary mode avoids these translations. The file extension does not determine the mode.

> `fclose` is not equivalent to `free` and does not remove the pointer variable. After the call, the stream cannot be reused, even if closing reports an error.

### Text and Binary Representation:

The work sequence is shared: **open, transfer data, check the results, and close**. The difference lies in the chosen representation and transfer functions.

| Functions | Transfer | Return |
|---|---|---|
| `fprintf` / `fscanf` | Convert between values and text, like `printf` / `scanf` | Characters written or negative / assignment count or `EOF` |
| `fputs` / `fgets` | Write a string / read part of a line | Nonnegative or `EOF` / buffer or `NULL` |
| `fputc` / `fgetc` | Write / read a character | Character as `unsigned char` converted to `int`, or `EOF` |
| `fwrite` / `fread` | Transfer the representation of objects | Number of complete elements transferred |

`fputs(texto, arquivo)` does not add a newline. `fgets` works as in the examples with `stdin`, replacing it with the opened stream. `fgetc(arquivo)` and `fputc(c, arquivo)` allow choosing the stream for character operations.

In text, the integer `10` can occupy the characters `'1'` and `'0'`. With `fwrite`, the bytes of its memory representation are written. **Binary does not identify types automatically:** reading requires knowing the data organization.

### Comparative Example:

The program uses the same record and the same sequence of operations. `binario = 1` selects the memory representation; `binario = 0` selects formatted text.

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
    // Returns to the beginning and allows switching from writing to reading.
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

`fwrite(origem, tamanho, quantidade, arquivo)` and `fread(destino, tamanho, quantidade, arquivo)` count **complete elements**, not necessarily bytes. The example transfers one record per operation and uses the result only after confirming success.

Direct writing depends on sizes, byte order, numeric representation, and possible structure padding. It serves compatible environments; a portable format needs to define these details. Writing a pointer does not also write its target.

> Binary preserves the object's representation without text conversion; text allows direct inspection and formatting, which can reduce precision - as with `%.2f`. Opening mode and function are distinct choices: opening with `b` does not turn `fprintf` output into binary numbers.

### Stream Position and State:

Operations advance a position indicator, like a marker within the file. Repositioning it does not mean incrementing the `FILE *` variable.

| Facility | Purpose |
|---|---|
| `ftell(arquivo)` | Obtains a position indication; returns `-1L` on failure |
| `fseek(arquivo, deslocamento, referência)` | Requests repositioning; returns `0` on success |
| `rewind(arquivo)` | Requests the beginning and clears the error and end indicators; returns no status |
| `feof(arquivo)` | Checks whether a previous read detected the end of the stream |
| `ferror(arquivo)` | Checks whether a stream error occurred |

The reference points for `fseek` are `SEEK_SET` (beginning), `SEEK_CUR` (current position), and `SEEK_END` (end). Not every stream allows repositioning.

```c
long posicao = ftell(arquivo);
if (posicao == -1L || fseek(arquivo, posicao, SEEK_SET) != 0) {
    puts("Falha na consulta ou no reposicionamento.");
}
```

In text, `ftell` does not provide a general byte count: the position can be saved and restored with `SEEK_SET`. Portably, `fseek` in text uses a zero offset or a position obtained through `ftell` on the same file, with `SEEK_SET`.

In modes with `+`, **writing followed by reading** requires appropriate `fflush` or positioning. **Reading followed by writing** requires positioning, except when the read encountered the end of the file. The comparative example uses `fseek` for this transition.

In append mode, writes remain at the end even after repositioning; in `"a+"`, repositioning allows choosing where to read. `fflush` sends pending output in the cases specified for output and update streams, without acting as a general input cleanup.

> The read's return value should control processing: `while (fgets(linha, sizeof linha, arquivo) != NULL)`, for example. `while (!feof(arquivo))` can process an unsuccessful attempt because `feof` does not predict the next read. After the loop, `ferror` allows checking for an error.

---

# 2. General Utilities - `<stdlib.h>`

The `<stdlib.h>` header groups numeric conversions, dynamic allocation, sorting, searching, and other general-purpose operations. Despite its name, it is only one of the standard library's headers.

## Conversions between Text and Numbers:

Converting a string means interpreting its characters and producing a numeric value. A cast such as `(int)` does not perform this operation: converting `"120"` requires a function that interprets the text.

| Function | Type Produced | Conversion Control |
|---|---|---|
| `atoi`, `atol`, `atoll` | `int`, `long`, `long long` | Decimal conversion without sufficient diagnostics |
| `atof` | `double` | Conversion without sufficient diagnostics |
| `strtol`, `strtoll` | `long`, `long long` | Base, stopping point, and range error |
| `strtoul`, `strtoull` | `unsigned long`, `unsigned long long` | Base, stopping point, and range error |
| `strtof`, `strtod`, `strtold` | `float`, `double`, `long double` | Stopping point and range errors |

`atoi("12abc")` produces `12`: these functions interpret the initial convertible part. A zero return alone does not distinguish valid input from a conversion that did not occur. When validation is needed, the `strto...` family offers more control.

### Integers - `strtol` and Related Functions:

`strtol(texto, &fim, base)` returns the value and places the address of the first unconverted character in `fim`. The base can range from `2` to `36`; with `0`, it recognizes decimal, octal starting with `0`, and hexadecimal starting with `0x` in C17.

Validation combines the stopping point, `errno`, and destination limits. The example accepts whitespace before and after the number, including the newline normally received through `fgets`.

```c
#include <stdlib.h>
#include <stdio.h>
#include <errno.h>  // errno and ERANGE
#include <limits.h> // INT_MIN and INT_MAX
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

`fim == texto` indicates no conversion; `ERANGE` signals a value outside the range of `long`. The `INT_MIN` and `INT_MAX` limits check whether the result also fits in `int`, before the cast.

The final test rejects remainders such as `"abc"`. `isspace`, from `<ctype.h>`, recognizes whitespace; the cast to `unsigned char` keeps its argument valid. The function assumes that `resultado` points to a writable `int`.

> `errno`, from `<errno.h>`, is zeroed before conversion because a successful call need not clear previous errors. Unsigned functions, such as `strtoul`, also accept a negative sign in the input; if the application forbids this, the sign needs to be validated separately.

### Floating Point - `strtod` and Related Functions:

`strtod(texto, &fim)` follows the same idea without a base argument. It recognizes representations such as `"3.5"` and `"3.5e2"`; the decimal separator depends on the locale, being `.` in the initial `"C"` locale.

```c
// Headers: stdlib.h, stdio.h, and errno.h.
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

This example requires the immediate end of the string, so it rejects trailing whitespace. `strtof` and `strtold` handle the other floating-point types.

`ERANGE` indicates a range error; values that are too small can also trigger this signal. Representations of infinity and NaN may be accepted: requiring a finite number is additional validation, with facilities such as `isfinite`, from `<math.h>`.

## Dynamic Allocation:

The functions below manage memory blocks whose lifetime is controlled by the program. Memory organization and the concepts of ownership and lifetime were introduced in the C handout.

| Function | Operation |
|---|---|
| `malloc(bytes)` | Allocates a block without initializing its contents |
| `calloc(quantidade, tamanho)` | Allocates space for the elements and zeroes all bits |
| `realloc(ponteiro, bytes)` | Resizes a block, possibly moving it |
| `free(ponteiro)` | Releases the block; returns no value |

For positive sizes, the three allocation functions return `NULL` on failure. In C, the `void *` return can be assigned directly to the appropriate pointer without a cast.

### Allocation, Expansion, and Release:

The example creates four zeroed counters, expands the space to six, and initializes the added part.

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

`realloc` preserves the content up to the smaller of the old and new sizes; the added part is not initialized. If it fails with a positive size, the original block remains valid. This is why the return passes through a temporary pointer.

After success, access must use the returned pointer: the previous pointer and any references into the block are no longer valid, even if the numeric address does not change.

`malloc(quantidade * sizeof *contadores)` would be an alternative to the initial allocation, but would require initializing the counters before reading them. `calloc` zeroes bits; this produces zero in integers, but does not universally guarantee null pointers or floating-point zero.

> For variable counts, the multiplication needs to fit in `size_t`: before `quantidade * sizeof *ponteiro`, the condition is `quantidade <= SIZE_MAX / sizeof *ponteiro`, with `SIZE_MAX` from `<stdint.h>`. Zero counts can be handled separately; in C17, `realloc(p, 0)` does not portably replace `free(p)`.

`free(NULL)` does nothing. For other values, `free` receives the start of a valid block that has not yet been released. The function does not change the pointer variable; assigning `NULL` afterward can prevent accidental reuse, but does not fix other references to the block.

## Sorting and Searching - `qsort` and `bsearch`:

These functions work with arrays of different types. The program supplies the starting address, the element count, the size of each element, and a **comparison function**.

| Comparator Return | Relationship between Elements |
|---|---|
| Negative | First comes before second |
| Zero | Equivalent under the chosen criterion |
| Positive | First comes after second |

The comparator receives `const void *`, interprets the objects according to their type, and defines the order. It need not return exactly `-1` or `1`: the sign is sufficient.

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

`qsort` modifies the array itself and returns no value. Despite its name, C does not require a quicksort implementation or guarantee stability: equivalent elements can change order.

`bsearch` searches for a key in the array, which must be organized according to the comparison criterion used in the search. It returns a pointer to a matching element or `NULL`; among duplicates, it does not guarantee the first or last.

> `return x - y` can overflow the range of `int`. The expression `(x > y) - (x < y)` compares without this risk. For structures, the same principle allows comparing a field; in a search, the comparator receives the key first and then an array element.

## Integer Operations - Absolute Value and Division:

| Function | Types Used | Result |
|---|---|---|
| `abs`, `labs`, `llabs` | `int`, `long`, `long long` | Absolute value in the same type |
| `div`, `ldiv`, `lldiv` | `int`, `long`, `long long` | Structure with quotient and remainder |

The structures `div_t`, `ldiv_t`, and `lldiv_t` have the members `quot` and `rem`. They are useful when both parts of the division matter, as when distributing a quantity into groups.

```c
int pecas = 17;
div_t caixas = div(pecas, 5);
printf("Caixas completas: %d; restantes: %d\n", caixas.quot, caixas.rem);
// Caixas completas: 3; restantes: 2
printf("Modulo: %d\n", abs(-7)); // Modulo: 7
```

The quotient is truncated toward zero, like `/` between integers in C17. A nonzero remainder has the numerator's sign: `div(-17, 5)` produces quotient `-3` and remainder `-2`.

> The result must fit in the type. The absolute value of `INT_MIN` may not be representable; division by zero and an out-of-range quotient also cause undefined behavior. For floating point, the absolute value is obtained with the `fabs` family, from `<math.h>`.

## Pseudorandom Numbers - `rand` and `srand`:

`rand()` produces an integer between `0` and `RAND_MAX`, inclusive. `srand(semente)` initializes the sequence; the same seed reproduces the same results in the same implementation.

```c
srand(1234); // Fixed seed: allows repeating the test.
for (int i = 0; i < 3; i++) {
    printf("%d\n", rand()); // The exact values depend on the implementation.
}
```

Without `srand`, the sequence is equivalent to one initialized with seed `1`. Initialization usually occurs once, before generation; restarting it on every call can repeat the same values.

The expression `rand() % 6 + 1` produces values from `1` to `6`, but can introduce bias: some results have more possible source values than others. The library also does not guarantee a high-quality distribution.

> `rand` serves simple examples and tests, but is unsuitable for passwords, tokens, or other uses requiring unpredictability.

## Program Termination:

`EXIT_SUCCESS` and `EXIT_FAILURE` represent portable success and failure statuses. They can be used in the return from `main`, as in the examples, or as an argument to `exit`.

`exit(estado)` terminates the entire program, even when called outside `main`. It runs the functions registered with `atexit`, sends pending stream output, and closes open files. A `return` in an ordinary function only returns control to the caller.

`atexit(funcao)` registers a function with no arguments and no return value for normal termination. It returns zero on success, and registered functions run in reverse registration order.

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
    return EXIT_SUCCESS; // Then execute finalizar.
}
```

Registration does not replace releasing resources when they are no longer needed during execution. `abort()`, in turn, causes abnormal termination and does not run functions registered with `atexit`.

---

# 3. Character Classification and Conversion - `<ctype.h>`

The `<ctype.h>` header allows identifying character properties and converting between uppercase and lowercase. Operations are individual; a string is processed by traversing its characters.

The functions receive `int`, but the argument must be `EOF` or a value representable as `unsigned char`. For characters obtained from a string, the usual form is `funcao((unsigned char)texto[i])`, avoiding negative values when `char` is signed.

## Classification:

Classification functions return **zero when the condition is false** and **a nonzero value when it is true**. They can therefore be used directly in conditions, such as `if (isdigit(c))`.

| Function | Checks whether the Character Is | True Examples |
|---|---|---|
| `isalpha(c)` | Letter | `'A'`, `'b'` |
| `isdigit(c)` | Decimal digit | `'0'`, `'8'` |
| `isalnum(c)` | Letter or decimal digit | `'A'`, `'7'` |
| `isupper(c)` | Uppercase letter | `'F'` |
| `islower(c)` | Lowercase letter | `'c'` |
| `isxdigit(c)` | Hexadecimal digit | `'9'`, `'A'`, `'f'` |
| `isspace(c)` | Whitespace | `' '`, `'\t'`, `'\n'` |
| `isblank(c)` | Horizontal whitespace | `' '`, `'\t'` |
| `ispunct(c)` | Printable character that is neither alphanumeric nor a space | `'!'`, `'_'` |
| `isprint(c)` | Printable character, including space | `'A'`, `'!'`, `' '` |
| `isgraph(c)` | Printable character, excluding space | `'A'`, `'!'` |
| `iscntrl(c)` | Control character | `'\n'`, `'\t'` |

In the initial `"C"` locale, `isspace` recognizes space, `\t`, `\n`, `\r`, `\f`, and `\v`; `isblank` recognizes only space and horizontal tab.

`isdigit` checks a **character**, not a complete number. Thus, `'-'` and `'.'` are not digits, although they can appear in numeric representations such as `"-3.5"`.

## Conversion between Uppercase and Lowercase:

| Function | Operation |
|---|---|
| `toupper(c)` | Returns the corresponding uppercase character, when applicable |
| `tolower(c)` | Returns the corresponding lowercase character, when applicable |

Both return `int`. When no conversion applies, the value is returned unchanged. The function does not modify the supplied variable: the change must be stored.

```c
printf("%c %c %c\n", toupper('u'), tolower('W'), toupper('7'));
// U w 7
```

There is no need to test `islower` before `toupper` or `isupper` before `tolower`. The functions themselves handle characters that do not need conversion.

### Classification and Conversion of a String:

The example counts letters, digits, and whitespace while converting the text to uppercase. Comparisons with zero normalize results to `0` or `1`, allowing them to be added to the counters.

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

The `texto` array is modifiable. Directly modifying a string literal, such as the one pointed to by `char *texto = "Peca"`, causes undefined behavior.

## Encoding and Limits:

Classification and conversion can depend on the locale. The examples use basic characters compatible with the initial `"C"` locale.

These functions do not automatically process Unicode characters composed of multiple bytes. In UTF-8, an accented letter can occupy more than one byte; applying `toupper` separately to each byte does not fully convert that letter.

> For results from `getchar` or `fgetc`, the value remains in `int` until checking for `EOF`. Converting it to `unsigned char` beforehand eliminates the distinction between this indicator and a valid byte.

---

# 4. Strings and Memory Blocks - `<string.h>`

The `<string.h>` header groups copying, concatenation, comparison, and search operations. String functions recognize `'\0'` as a terminator; `mem...` operations work with an explicit byte count, including zero-valued bytes.

These functions do not automatically allocate the destination. Available space, pointer validity, and access bounds remain the program's responsibility. The examples use `<string.h>` and `<stdio.h>`.

## Length - `strlen`:

`strlen(texto)` returns a `size_t` with the number of bytes before the first `'\0'`. It neither includes the terminator nor reports the buffer's capacity.

```c
char texto[20] = "Feliz";
printf("Comprimento: %zu; capacidade: %zu\n", strlen(texto), sizeof texto);
// Comprimento: 5; capacidade: 20
```

`sizeof` gives the capacity in this case because `texto` is an array; applied to a pointer, it gives the pointer's size. `strlen` requires a string terminated within accessible memory.

> In UTF-8, a letter can occupy several bytes. Therefore, the result of `strlen` does not necessarily represent the number of characters perceived when reading.

## Copying and Concatenation:

Arguments follow the order **destination, source**. The functions below return the destination's address.

| Function | Operation | Terminator |
|---|---|---|
| `strcpy(destino, origem)` | Copies the entire string | Also copies `'\0'` |
| `strncpy(destino, origem, n)` | Writes `n` positions, copying up to the end of the source and padding with zeros | May not add `'\0'` if the source has at least `n` bytes before the terminator |
| `strcat(destino, origem)` | Appends the source to the end of the destination | Includes `'\0'` at the end |
| `strncat(destino, origem, n)` | Appends up to `n` bytes of the source | Also appends `'\0'` |

For concatenation, the destination must already contain a valid string, even if empty: `char texto[32] = "";`. Copying and concatenation require nonoverlapping source and destination regions.

```c
char mensagem[32], inicio[6];
strcpy(mensagem, "Feliz");
strcat(mensagem, " aniversario"); // The known texts fit in the destination.
strncat(mensagem, "!", sizeof mensagem - strlen(mensagem) - 1);
strncpy(inicio, mensagem, sizeof inicio - 1);
inicio[sizeof inicio - 1] = '\0';
printf("%s | %s\n", mensagem, inicio);
// Feliz aniversario! | Feliz
```

`strncpy` is not a version that simply guarantees safe copying: if the limit is reached before the terminator, the result will not be a terminated string. In the example, the last position is explicitly filled with `'\0'`.

In `strncat`, `n` limits the appended content, **not the destination's total capacity**. The expression `capacidade - strlen(destino) - 1` calculates the remaining space, provided the destination is already a valid string within that buffer.

Limiting the copy can cause truncation. When the complete text is needed, capacity must be checked before the operation; for `strcat`, it must accommodate `strlen(destino) + strlen(origem) + 1` bytes.

## Comparison - `strcmp` and `strncmp`:

`strcmp(a, b)` compares strings until the first difference or the terminator. `strncmp(a, b, n)` compares at most `n` bytes, also respecting the end of the strings.

| Return | Meaning |
|---|---|
| Negative | `a` comes before `b` in byte order |
| Zero | Equivalent content in the compared part |
| Positive | `a` comes after `b` in byte order |

```c
printf("Iguais: %d\n", strcmp("Abc", "Abc") == 0); // Iguais: 1
printf("Prefixo igual: %d\n", strncmp("Abc", "Abd", 2) == 0); // Prefixo igual: 1
printf("Diferentes: %d\n", strcmp("Casa", "casa") != 0); // Diferentes: 1
```

The comparison distinguishes uppercase from lowercase and interprets bytes as `unsigned char`. The result need not be exactly `-1` or `1`, and this order is not necessarily a language's alphabetical order.

> `a == b`, when both are pointers, compares addresses. To check string contents, the condition is `strcmp(a, b) == 0`. An equal prefix with `strncmp` does not guarantee complete equality.

## Searching for Characters and Substrings:

| Function | Searches for |
|---|---|
| `strchr(texto, c)` | First occurrence of the character |
| `strrchr(texto, c)` | Last occurrence of the character |
| `strstr(texto, trecho)` | First occurrence of the substring |

All three return a pointer to the occurrence or `NULL` when not found. The pointer references the string itself: no copy is created.

```c
const char *nome = "arquivo.tar.gz";
const char *primeiro = strchr(nome, '.');
const char *ultimo = strrchr(nome, '.');
const char *trecho = strstr(nome, "tar");
if (primeiro != NULL) printf("%s\n", primeiro); // .tar.gz
if (ultimo != NULL) printf("%s\n", ultimo);     // .gz
if (trecho != NULL) printf("%s\n", trecho);     // tar.gz
```

Printing continues until the original terminator. If the intention is to modify the found content, the source string must be in modifiable memory; a string literal cannot be changed.

## Operations on Memory Blocks:

The `mem...` functions do not interpret strings or stop at `'\0'`. Their limits are expressed in **bytes**, allowing work with arrays and other memory representations.

| Function | Operation |
|---|---|
| `memcpy(destino, origem, n)` | Copies `n` bytes; regions must not overlap |
| `memmove(destino, origem, n)` | Copies `n` bytes, allowing overlap |
| `memset(destino, valor, n)` | Fills `n` bytes with the value converted to `unsigned char` |
| `memcmp(a, b, n)` | Compares `n` bytes and returns negative, zero, or positive |

`memcpy`, `memmove`, and `memset` return the destination's address. Source and destination must allow the accesses specified by the size; `memmove` handles overlap but does not enlarge the buffer.

```c
unsigned char origem[] = {0x10, 0x00, 0x20};
unsigned char copia[sizeof origem];
memcpy(copia, origem, sizeof origem); // Also copies the zero-valued byte.
printf("Blocos iguais: %d\n", memcmp(origem, copia, sizeof origem) == 0);
// Blocos iguais: 1
memset(copia, 0, sizeof copia); // All bytes become zero.

char texto[] = "ABCDE";
memmove(texto, texto + 2, strlen(texto + 2) + 1); // Includes the terminator.
puts(texto); // CDE
```

In the last operation, source and destination belong to the same array and overlap. `memmove` preserves the required bytes during copying; replacing this call with `memcpy` would cause undefined behavior.

`memset` repeats a **byte**, not a value of the elements' type. Therefore, `memset(vetor, 1, sizeof vetor)` does not fill an `int` array with the number `1`. Zeroing bits also does not universally guarantee null pointers or floating-point zero.

> `memcmp` compares representations, not abstract values. Structures with equal fields can have different padding bytes; comparing fields is more appropriate for comparing their data.

---

# 5. Standardized Integer Types - `<stdint.h>`

The `<stdint.h>` header provides integer type names with explicit width and range requirements. This allows defining interfaces and structures without assuming, for example, that `int` always has 32 bits.

The names are defined through `typedef`: they correspond to integer types provided by the implementation, retaining C's rules for operations and conversions.

## Exact Width:

| Signed | Unsigned | Width |
|---|---|---|
| `int8_t` | `uint8_t` | 8 bits |
| `int16_t` | `uint16_t` | 16 bits |
| `int32_t` | `uint32_t` | 32 bits |
| `int64_t` | `uint64_t` | 64 bits |

For example, `int8_t` represents values from `-128` to `127`, while `uint8_t` represents `0` to `255`. These types have no padding bits; signed exact-width types use two's complement in C17.

> Exact-width types are optional: they exist when the implementation provides the corresponding types. This chapter's examples assume their availability. Fixed width does not eliminate integer promotions or protect against overflow.

## Other Families:

| Family | Purpose | Example |
|---|---|---|
| `int_leastN_t` / `uint_leastN_t` | Smallest available size with at least `N` bits | `uint_least16_t` |
| `int_fastN_t` / `uint_fastN_t` | Type chosen by the implementation to favor operations with at least `N` bits | `int_fast32_t` |
| `intmax_t` / `uintmax_t` | Represent any value of signed / unsigned integer types | `uintmax_t` |
| `intptr_t` / `uintptr_t` | Allow converting a `void *` to an integer and back, preserving pointer equality | `uintptr_t` |

The `least` and `fast` families exist for `8`, `16`, `32`, and `64` bits. `fast` does not guarantee the best performance in every operation, and its size can exceed the indicated minimum. `intptr_t` and `uintptr_t` are also optional.

## Limits and Constants:

Limits follow the type names: `INT32_MIN`, `INT32_MAX`, `UINT16_MAX`, `INT_FAST32_MAX`, and so on. Unsigned types start at zero. The header also provides `SIZE_MAX`, the limit of `size_t`.

The macros `INT32_C(valor)` and `UINT64_C(valor)`, for example, form constants suitable for the corresponding minimum-width families, taking integer promotions into account. They allow expressing constants without manually choosing suffixes such as `L` or `ULL`.

## Input and Output - Support from `<inttypes.h>`:

A `uint64_t` can correspond to different fundamental types depending on the platform. `<inttypes.h>` provides format macros that follow this choice, such as `PRId32`, `PRIu64`, and `PRIx32` for signed decimal, unsigned decimal, and hexadecimal output.

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
        codigo++; // Checks the limit before incrementing.
    }
    return 0;
}
```

In `"%" PRIu32`, the macro supplies part of the format, and adjacent literals are joined during translation. For input, there are macros such as `SCNd32` and `SCNu64`: `scanf("%" SCNu64, &total)`. The return value and range considerations introduced in `<stdio.h>` still apply.

> Fixed width helps define the data but does not standardize byte order or padding between fields of a `struct`. A portable binary format still needs to specify how values will be written.

---

# 6. Mathematical Functions - `<math.h>`

The `<math.h>` header groups mathematical operations and classification of floating-point values. Generally, the version without a suffix works with `double`, the version with `f` uses `float`, and the version with `l` uses `long double`: `sqrt`, `sqrtf`, and `sqrtl`.

The examples use `<math.h>` and `<stdio.h>`. Calculations follow the precision available in the type and can produce approximations.

## Powers, Roots, and Logarithms:

| Function | Operation |
|---|---|
| `pow(x, y)` | Power with base `x` and exponent `y` |
| `sqrt(x)` / `cbrt(x)` | Square / cube root |
| `exp(x)` | Exponential with base `e` |
| `log(x)` | Natural logarithm, with base `e` |
| `log10(x)` / `log2(x)` | Logarithms with bases `10` / `2` |
| `hypot(x, y)` | Hypotenuse: square root of `x*x + y*y`, avoiding undue intermediate overflow |

```c
printf("Raizes: %.1f e %.1f\n", sqrt(9.0), cbrt(-8.0)); // 3.0 e -2.0
printf("Potencia: %.1f\n", pow(2.0, 3.0)); // 8.0
printf("Logaritmos: %.1f %.1f %.1f\n", log(exp(1.0)), log10(1000.0), log2(8.0));
// Logaritmos: 1.0 3.0 3.0
printf("Hipotenusa: %.1f\n", hypot(3.0, 4.0)); // 5.0
```

`cbrt` accepts negative arguments; `pow(x, 1.0 / 3.0)` is not a general replacement for this. The expression `1 / 3` performs integer division and produces zero. For a simple square, `x * x` usually expresses the operation more directly.

## Rounding:

The functions below still return floating point, even when the result has an integer value.

| Function | Criterion | For `2.7` | For `-2.7` |
|---|---|---|---|
| `floor(x)` | Nearest integer toward negative infinity | `2.0` | `-3.0` |
| `ceil(x)` | Nearest integer toward positive infinity | `3.0` | `-2.0` |
| `trunc(x)` | Discards the fractional part, toward zero | `2.0` | `-2.0` |
| `round(x)` | Nearest integer; ties go away from zero | `3.0` | `-3.0` |

For example, `round(2.5)` produces `3.0`, and `round(-2.5)` produces `-3.0`. Converting afterward to an integer type still requires the value to be representable in that type.

## Absolute Value and Remainder:

`fabs(x)` obtains the floating-point absolute value. `fmod(x, y)` calculates the remainder using a quotient truncated toward zero; a nonzero remainder has the sign of `x`.

```c
printf("Absoluto: %.1f\n", fabs(-3.5)); // 3.5
printf("Restos: %.1f e %.1f\n", fmod(7.5, 2.0), fmod(-7.5, 2.0));
// Restos: 1.5 e -1.5
```

The `%` operator is reserved for integers. `fmod` allows working with fractional values, but does not guarantee a positive remainder when the first argument is negative.

## Trigonometry:

| Functions | Operation |
|---|---|
| `sin(x)`, `cos(x)`, `tan(x)` | Sine, cosine, and tangent; argument in radians |
| `asin(x)`, `acos(x)`, `atan(x)` | Inverse functions; result in radians |
| `atan2(y, x)` | Angle of the direction `(x, y)`, accounting for the quadrant |

```c
const double pi = acos(-1.0);
double angulo = 30.0 * pi / 180.0;
printf("Seno: %.2f\n", sin(angulo)); // 0.50
printf("Direcao: %.1f graus\n", atan2(1.0, 1.0) * 180.0 / pi); // 45.0 graus
```

Converting degrees to radians multiplies by `pi / 180`; the reverse operation multiplies by `180 / pi`. `atan2(y, x)` preserves quadrant information that can be lost in `atan(y / x)`.

> `M_PI` is a common extension, but is not guaranteed by the C17 standard. `acos(-1.0)` provides an approximation of pi without depending on this macro.

## Domain, Precision, and Special Values:

Some operations impose restrictions: `sqrt` requires a nonnegative argument for a real result, logarithms require a positive argument, and `asin`/`acos` receive values between `-1` and `1`.

| Macro | Checks whether the Value Is |
|---|---|
| `isfinite(x)` | Finite, neither infinite nor NaN |
| `isinf(x)` | Infinite |
| `isnan(x)` | NaN (Not a Number) |

These tests return zero or a nonzero value. NaN is not identified with `x == NAN`; the appropriate check is `isnan(x)`.

Domain errors and out-of-range results can involve signaling through `errno`, floating-point exceptions, or special values, depending on the implementation. Validating inputs avoids depending solely on the result to recognize an inappropriate operation.

Approximate results also require care in comparisons: a tolerance should consider the application's scale and acceptable error. Rounding the display with `printf` does not change the stored value.

## Compilation:

In environments such as GCC on Linux, linking with the mathematical library normally uses `-lm`, placed after the file using its functions:

```sh
gcc -std=c17 programa.c -o programa -lm
```

This option belongs to the organization of the tool and system, not to C syntax; other environments may not require it.

---

# 7. Variable Arguments - `<stdarg.h>`

The `<stdarg.h>` header allows access to the arguments of a variadic function, whose argument count can change between calls. The declaration ends with `...` and, in C17, must have at least one named parameter before the ellipsis.

The function does not automatically discover how many arguments it received or their types. This information must follow a convention, such as an explicit count or a format string, as with `printf`.

## Main Facilities:

| Facility | Purpose |
|---|---|
| `va_list` | Type maintaining the argument access state |
| `va_start(lista, ultimo)` | Initializes access; `ultimo` is the last named parameter |
| `va_arg(lista, tipo)` | Obtains the next argument in the specified type and advances reading |
| `va_copy(destino, origem)` | Initializes an independent copy of the current state |
| `va_end(lista)` | Ends use of the initialized list |

Access occurs sequentially. `va_list` should not be treated as an array or an ordinary pointer; its representation depends on the implementation.

## Example - Average of Values:

The count is supplied in the first argument. The following arguments must be supplied as `double`, including automatically promoted `float` values.

```c
#include <stdarg.h>
#include <stdio.h>

double media(int quantidade, ...) {
    if (quantidade <= 0) {
        return 0.0; // This example's convention for an invalid count.
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

The accumulator remains local to the function. Returning `0.0` for an invalid count is only a decision in the example, not a mathematical definition of an average without elements.

## Types, Promotions, and Cleanup:

Arguments in the `...` part undergo **default promotions**:

- `float` is promoted to `double`.
- `char` and `short`, signed or unsigned, are promoted to `int` or `unsigned int`, according to the representable range.
- `_Bool` is promoted to `int`.

Thus, `media(2, 10.0, 20.0)` matches the expected type. In contrast, `media(2, 10, 20)` passes integers: `va_arg(argumentos, double)` does not convert these values and causes undefined behavior.

Reading beyond the supplied arguments also causes undefined behavior. The requested type must match the argument after promotions, not merely have the same size in bytes.

Each initialization with `va_start` or `va_copy` requires a corresponding `va_end` in the same function. To traverse the arguments with a second list, use `va_copy`; direct assignment between `va_list` objects is not portable.

> `va_end` does not release the objects passed by the caller or necessarily zero any pointer. The macro ends the access state and must be used even when the implementation requires no visible operation.

---

# 8. Boolean Values - `<stdbool.h>`

In C17, `<stdbool.h>` provides more readable names for representing logical conditions. The fundamental type is `_Bool`; the header defines macros for its use.

| Macro | Expansion in C17 |
|---|---|
| `bool` | `_Bool` |
| `true` | Integer constant `1` |
| `false` | Integer constant `0` |

When converting a numeric value to `bool`, zero produces `false`, and any value other than zero produces `true`. The stored value is normalized to `0` or `1`.

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

The operators `!`, `&&`, and `||` retain their usual behavior. The header's contribution is to make explicit the intention to represent states such as available, valid, or completed.

`printf` can use `%d` because `bool` is promoted to `int`. For input, `%d` cannot receive a `bool *`: input must be read into an `int` and then converted, with validation when only `0` and `1` are accepted.

> A `bool` object does not necessarily occupy a single bit; its size depends on the implementation. The header also does not change C's condition rules, which already interpret zero as false and nonzero values as true.

---

# Sources:

- DEITEL, Harvey M.; DEITEL, Paul J. *Como programar em C*. 2nd ed. Rio de Janeiro: LTC, 1994.

- PEIXOTO, Daniela Cristina Cascini. *Disciplina: Lógica de programação*. Undergraduate Computer Engineering program. Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2024.

- BATISTA, Natália Cosse. *Disciplina: Algoritmos e estruturas de dados*. Undergraduate Computer Engineering program. Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2025.

- CPPREFERENCE.COM. *C reference*. [No place], [no date]. Available at: [https://en.cppreference.com/w/c](https://en.cppreference.com/w/c). Accessed: 4 Aug. 2026.

- FREE SOFTWARE FOUNDATION. *The GNU C Library Reference Manual*. Version 2.42. [No place]: Free Software Foundation, 2025. Available at: [https://sourceware.org/glibc/manual/2.42/html_node/index.html](https://sourceware.org/glibc/manual/2.42/html_node/index.html). Accessed: 26 Sep. 2026.

- ISO/IEC JTC 1/SC 22/WG 14. *Programming languages - C*. Committee Draft N1570, 12 Apr. 2011. Available at: [https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf](https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf). Accessed: 26 Sep. 2026.
