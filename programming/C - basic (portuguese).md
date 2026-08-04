# `C` - Básico

# 1. Características da linguagem

## 1.1 Paradigmas

* **Imperativa:** O código atua como uma série de comandos diretos que alteram o estado do programa passo a passo. O programador dita exatamente *como* o computador deve chegar ao resultado.
* **Estruturada:** O fluxo de controle é feito por meio de estruturas organizadas e bem definidas (sequência, seleção como `if`/`switch`, e iteração como `for`/`while`), evitando saltos e desvios incondicionais que dificultam a leitura.
* **Procedural:** O programa é dividido em procedimentos (funções), que encapsulam uma série de instruções e passos computacionais. Isso facilita a organização, a legibilidade e o reaproveitamento de código.

## 1.2 Tipagem

* **Estática:** A verificação de tipos é feita em tempo de compilação (o tipo de cada variável deve ser declarado). 
* **Fraca:** Permite muitas conversões implícitas (coerções) entre tipos diferentes (por exemplo, tratar um `char` como `int`, ou converter implicitamente entre ponteiros de tipos distintos usando `void*`).

## 1.3 Nível de abstração

**Intermediário:** possui abstrações, mas também muitos recursos de gestão manual do hardware. A proximidade com a CPU é o que garante boa parte de sua velocidade de execução.

## 1.4 Modelo de execução

**Compilada:** o arquivo `.c` precisa ser compilado (o compilador mais usado é o `gcc`) para gerar um executável.

## 1.5 Gerenciamento de memória

**Manual:** A linguagem permite controle quase absoluto sobre a memória. Todavia, esse recurso exige gerenciamento manual na alocação e liberação daquele espaço de memória (ver seção 8/9).

## 1.6 Principais aplicações

* Sistemas operacionais;
* Sistemas embarcados;
* Programação de alto desempenho;

## 1.7 Características marcantes

* Aritmética de ponteiros;
* Acesso direto à memória;
* Controle de layout de dados;
* Proximidade com o hardware;

---

# 2. Fundamentos da linguagem

## 2.1 Sintaxe e estrutura

* **Hierarquia de funções:** todo programa em C precisa de uma função `main`, responsável por definir as ações executadas **(função ativa)**. Ela é o ponto de entrada do programa. Por padrão, o tipo de retorno adotado para `main` é `int`, por conta da convenção de usar o valor de retorno `0` como indicativo de execução bem-sucedida. As demais funções funcionam como ferramentas **(função passiva)**.
* **Comentários:** `// comentário de uma linha` e `/* comentário de bloco, podendo ocupar mais de uma linha */`. Não alteram o funcionamento do código, apenas a legibilidade e organização.
* **Blocos:** delimitados por chaves (`{ }`) são usados em funções e em estruturas de controle (condicionais e *loops*) para definir sua área de atuação.
* **Delimitadores:** cada instrução é finalizada por ponto e vírgula (`;`).
* **Imports/includes:** bibliotecas são adicionadas com `#include <nome_da_biblioteca.h>` (bibliotecas padrão) ou `#include "nome_do_arquivo.h"` (arquivos próprios/locais, se estiverem na mesma pasta, ou `#include "caminho/do/arquivo.h"`). O uso de bibliotecas evita redundância de programação e reduz *bugs*, já que as funções fornecidas já foram testadas e otimizadas.

### Exemplo

```c
#include <stdio.h>              // Biblioteca para uso de elementos de input e output

int main(void) {                // Função de execução do programa, com retorno int e sem argumentos
    printf("Hello, World!\n");  // Instrução que imprime uma mensagem no terminal
    return 0;                   // Retorno indicando execução bem-sucedida
}
```

## 2.2 Tipos de dados

### Tipos fundamentais

Em C, todos os dados são essencialmente numéricos (representados em binário). O que muda é o especificador de tipo, o que facilita conversões entre tipos, e a quantidade de informação que conseguem armazenar (a depender da arquitetura).

* **`char`:** caracteres, dentro da tabela ASCII. Como a tabela ASCII associa códigos numéricos a caracteres, é possível realizar operações aritméticas (soma, subtração) diretamente com valores `char` (útil para formatação de caracteres e cifragem). Normalmente ocupa 1 *byte* (8*bits*).
* **`int`:** números inteiros. Normalmente ocupa 4 *bytes* (-2.147.483.648 - 2.147.483.647).
* **`float`:** números racionais (ponto flutuante). Normalmente ocupa 4 *bytes*.
* **`double`:** números racionais, com o dobro da capacidade do `float` (normalmente 8 *bytes*). Capaz de gerar números mais extremos (maiores em módulo ou próximos de zero) e manter maior precisão.

**`stdbool.h`:** A biblioteca disponibiliza um tipo booleano (`true`/`false`) para quem preferir essa notação.

Cada tipo possui um formatador associado, usado por `printf`/`scanf` (ver seção 9) para indicar explicitamente o tipo de dado na conversão de/para `string`: `%c` (char), `%d` (int), `%f` (float), `%lf` (double), entre outros (evitando erros de conversão).

### Qualificadores e modificadores

* **`const`:** Impede que o valor da variável seja alterado após sua inicialização.
* **`volatile`:** Informa ao compilador que o valor da variável pode ser alterado a qualquer momento por algo externo ao código (hardware, *threads*, interrupções). Isso impede o compilador de fazer otimizações assumindo que o valor permanecerá o mesmo.
* **`long` / `short`:** Modificam a quantidade de espaço de um tipo numérico. O `long` aumenta o tamanho reservado (evitando *overflow*/*underflow*), gerando formatadores como `%ld` (`long int`). O `short` diminui o tamanho (economia de memória em casos críticos).
* **`signed` / `unsigned`:** O `signed` (comportamento padrão numérico - não precisa ser explicitado na declaração) permite armazenar números positivos e negativos usando um bit como sinal. `unsigned` remove o bit de sinal, permitindo apenas valores positivos (potencialmente dobrando a capacidade máxima armazenável naquele mesmo espaço de memória se forem usados apenas números positivos).

### Conversões

* **Conversão explícita (*casting*):** adição do prefixo `(novo_tipo)` na frente da variável, feita explicitamente pelo programador. Normalmente usada para converter um tipo maior em um tipo menor (ex.: `double` → `int`).
* **Conversão implícita (coerção):** feita automaticamente pelo compilador. Normalmente usada para converter um tipo menor em um tipo maior (`float` → `double`).

É preferível sempre realizar a conversão de forma explícita (torna o código mais claro e evita *bugs*).

```c
#include <stdio.h>
int main(){
    // Conversão explícita:
    double pi = 3.1415926535;
    int i_pi = (int) pi;    // i_pi = 3

    unsigned int u = 5;
    int s = -5;
    if (s < (int) u) { /* ação */ }
    // Sem a conversão, ambos seriam tratados como unsigned. 
    // Em binário: -5 = 11111111 11111111 11111111 11111011 = 4.294.967.291 (unsigned) > 5

    // Conversão implícita:
    char a = 'A';           // char (65 na tabela ASCII)
    int b = a;              // char -> int implícito
    float c = b;            // int -> float implícito
    double d = c;           // float -> double implícito
    int e = d;              // double -> int implícito
    double op1 = b/10;      // op1 = (double)(65/10) = 6.0  (divisão inteira antes da conversão)
    double op2 = b/10.0;    // op2 = 65.0/10.0 = 6.5

    return 0;
}
```

**ATENÇÃO:** comparar um valor `signed` com um `unsigned` sem conversão explícita pode gerar resultados incorretos, pois o valor `signed` é implicitamente convertido para `unsigned` antes da comparação.

## 2.3 Variáveis

* **Declaração:** `tipo_de_dado nome_da_variável = valor;` (a atribuição pode ser feita depois da declaração).
* **Atribuição:** o valor atribuído pode ser um literal ou um valor indireto (resultado de outra variável, retorno de função, expressão), desde que condizente com o tipo da variável.
* **Declaração múltipla:** é possível declarar várias variáveis do mesmo tipo em uma linha, misturando atribuição literal e indireta.
* **Valores padrão / elemento nulo:** cada tipo tem sua própria forma de representar "ausência de valor": `0` para tipos numéricos, `'\0'` para `char`, `NULL` para ponteiros, *arrays* e *strings*.

**ATENÇÃO:** enquanto nenhum valor for atribuído a uma variável após sua declaração, ela não fica "vazia" - contém lixo de memória (dados residuais do endereço reservado). É necessário atribuir algum valor (mesmo que nulo) antes de utilizá-la.

```c
#include <stdio.h>

int main(){
    char a;                    // Declaração
    a = 'c';                   // Atribuição de valor literal
    int b = 20;                // Declaração com atribuição direta
    int c = b, d = 40 + 20;    // Declaração múltipla: indireta (c) e literal/expressão (d)
    printf("a = %c, b = %d, c = %d, d = %d\n", a, b, c, d);
    // output: a = c, b = 20, c = 20, d = 60
    return 0;
}
```

### Constantes

Elementos cujo valor, após a declaração, não pode ser modificado. **A atribuição só pode ocorrer nesse momento**.

Sintaxe: `const tipo_de_dado nome_da_variável = valor;`.

## 2.4 Escopo e duração

* **Escopo local:** elemento declarado dentro de um bloco. Sua memória fica reservada apenas durante a execução do bloco, e é acessível diretamente apenas por esse bloco e seus sub-blocos.
* **Escopo global:** elemento declarado fora de qualquer bloco. Seu endereço de memória fica alocado durante toda a execução do programa e pode ser acessado por qualquer função.

### Sombreamento (*shadowing*)

Elementos pertencentes ao mesmo bloco (ou sub-blocos) não podem ter o mesmo nome, mas elementos em blocos diferentes podem. Quando um dado global e um local têm o mesmo nome, o dado **local** é o processado nas chamadas dentro daquele escopo.

```c
#include <stdio.h>

const int constante_global = 10;
int variavel_global = 20;

int main(){
    const int constante_local = 30;
    int variavel_local = 40;
    int variavel_global = 50;   // Sombreamento da variável global
    printf("constante_global = %d\nvariavel_global = %d\n", constante_global, variavel_global);
    // output: 
    // constante_global = 10
    // variavel_global = 50
    for (int i = 0; i < 10; i++) { /* i só existe dentro do for */ }
    int i = 5;                  // Nome reaproveitado, pois o i do for não existe mais aqui
    return 0;
}
```

### Duração/*lifetime*

A duração de um elemento (por quanto tempo sua memória permanece reservada) não se confunde com seu escopo (onde ele pode ser acessado). Em C, essa duração é controlada pelas classes de armazenamento `auto`, `static` e `extern` (ver seção 7/8).

## 2.5 Operadores e expressões

### Operadores

De forma resumida, a hierarquia de operadores segue, do maior para o menor nível de prioridade:

1. Parênteses.
2. Operadores aritméticos, lógicos, comparativos etc.
3. Operadores de atribuição.

É recomendável definir explicitamente a ordem das operações por meio de parênteses, para evitar *bugs* e comportamentos inesperados.

### Tabela de precedência completa

Da maior para a menor prioridade:

1. **Parênteses:** `( )`.
2. **Acesso a valores:** índice de array (`[ ]`), chamada de função, acesso a membro de struct (`.`), acesso a membro via ponteiro (`->`).
3. **Operadores unários** (avaliados da direita para a esquerda): inversão de sinal (`-`), NOT lógico (`!`), NOT bit a bit (`~`), incremento (`++`), decremento (`--`), operador de endereço (`&`), desreferenciação de ponteiro (`*`), `sizeof`.
4. **Aritméticos:** `*`, `/`, `%` têm prioridade maior que `+`, `-`.
5. **Deslocamento de bits:** `<<`, `>>`.
6. **Relacionais:** `>`, `>=`, `<`, `<=`, `==`, `!=`.
7. **Bit a bit:** AND (`&`), XOR (`^`), OR (`|`).
8. **Booleanos:** AND lógico (`&&`), OR lógico (`||`).
9. **Operador condicional (ternário):** `condição ? valor_se_verdadeiro : valor_se_falso`.
10. **Atribuição** (avaliados da direita para a esquerda): simples (`=`) e compostas (`+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`).
11. **Vírgula:** `,`.

**Observação:** os operadores de incremento/decremento, quando à esquerda da variável (`++x`), realizam a operação antes de retornar o valor; quando à direita (`x++`), retornam o valor antes de realizar a operação.

**Operador condicional (ternário):**
Avalia uma condição em linha e retorna um dos dois valores dependendo do resultado (funciona como um `if-else` compacto capaz de retornar valor).

Sintaxe: `condição ? valor_se_verdadeiro : valor_se_falso`

Exemplo:

```c
int argc = 1;
char* args[] = {"programa"};
// Se argc for 1 imprime a primeira string, senão imprime o último argumento
printf("%s\n", argc == 1 ? "Nao foi enviado nada" : args[argc - 1]);

int a = 10, b = 20;
int maior = (a > b) ? a : b; // Atribui 20 à variável maior
```

---

# 3. Controle de fluxo

Em C padrão não existe um tipo booleano nativo (ver seção 2). A linguagem entende `0` como falso e qualquer outro valor (de qualquer tipo) como verdadeiro.

## 3.1 Condicionais

### `if` / `else if` / `else`

**Conceito:** estrutura de escolha binária (verdadeiro ou falso).

**Sintaxe:**

```c
if (condição) {
    instruções;
} else if (outra_condição) {
    instruções;
} else {
    instruções;
}
```

Se a condição for verdadeira, executa o bloco correspondente; se for falsa, passa para o próximo teste (`else if` ou `else`).

**Exemplo:**

```c
#include <stdio.h>

int main(){
    int valor;
    scanf(" %d", &valor);
    if (valor == 10) {
        printf("Eh 10!\n");
        return 10;
    } else if (valor == 20 || valor == 30) {
        return 30;      // valor != 10
    } else {            // valor != 10 && valor != 20 && valor != 30
        if ('b')        // Diferente de 0, portanto sempre executado
            printf("Acao alcancada!\n");
        else
            return 1;   // Nunca alcançado
    }
    return 0;
}
```

**ATENÇÃO:** estruturas de controle podem ser usadas sem chaves, caso em que apenas a instrução imediatamente seguinte pertence ao bloco. É recomendável sempre usar chaves para evitar comportamento inesperado.

### `switch` / `case`

**Conceito:** executa diferentes ações a depender do valor de uma expressão.

**Sintaxe:**

```c
switch (valor) {
    case opcao_1:
        instruções;
        break;
    default:
        instruções;
        break; // facultativo no default
}
```

**Exemplo:**

```c
#include <stdio.h>

int main(){
    int valor;
    scanf(" %d", &valor);
    switch (valor) {
        case 10:            // valor == 10
            // Ação 1
            break;
        case 30: case 40:   // valor == 30 || valor == 40
            // Ação 3
            break;
        default:            // valor != 10 && valor != 30 && valor != 40
            // Ação padrão
            break;
    }
    return 0;
}
```

**ATENÇÃO:** todo `case` precisa obrigatoriamente de um `break` (no `default` é facultativo); sem ele, os `case`s seguintes também são executados (*fall-through*).

## 3.2 Repetição

### `while` / `do while`

**Conceito:**

* `while` executa uma ação enquanto a condição for verdadeira (podendo nunca entrar no bloco, se a condição já começar falsa).
* `do while` executa o bloco antes de testar a condição, garantindo ao menos uma execução.

**Sintaxe:**

```c
while (condição) { 
    instruções; 
}

do { 
    instruções; 
} while (condição);
```

**Exemplo:**

```c
#include <stdio.h>

int main(){
    int valor;
    scanf(" %d", &valor);
    while (valor != 10) {           // Se valor == 10, nem entra
        printf("Valor diferente de 10\n");
        scanf(" %d", &valor);
    }
    do {
        printf("%d\n", ++valor);    // output: 11
    } while (valor <= 10);          // Executa ao menos uma vez
    return 0;
}
```

### `for`

**Conceito:** *loop* cuja duração é delimitada no próprio escopo da estrutura.

**Sintaxe:**

```c
for (tipo_de_dado variável = valor; condição_de_parada; operação) {
    instruções;
}
```

**Observação:** a variável de controle não precisa ser declarada no escopo do `for` (pode ser declarada antes). Se for criada no escopo, ela é local a ele (só existe dentro do laço).

**Exemplo:**

```c
#include <stdio.h>

int main(){
    int u = 0;
    for (int i = 0; u < 20; i++) { // i incrementado de 1 em 1, interno ao for
        u += 2;
        printf("%d ", u);
    } printf("\n");                // output: 2 4 6 8 10 12 14 16 18 20

    for (u; u >= 10; u -= 3)       // u, externo ao for, decrementado de 3 em 3
        printf("%d ", u);
    printf("\n");                  // output: 20 17 14 11
    return 0;
}
```

## 3.3 Controle da execução

* **`break`:** sai imediatamente do bloco de execução (válido em *loops* e `switch`).
* **`continue`:** ignora o restante das instruções abaixo dele no *loop* atual e inicia a próxima repetição (só funciona em *loops*).
* **`return`:** encerra a função atual, opcionalmente retornando um valor.

#### `goto`

**Conceito:** redireciona a execução para a linha marcada por `LABEL` (ver seção 11).
**Sintaxe:** `goto LABEL;` 

```c
#include <stdio.h>

int main(){
    int a = 10;
    goto LABEL;
    a *= 2; // Pulado pelo goto
    LABEL:
    printf("%d\n", a); // output: 10
    return 0;
}
```

---

# 4. Funções

## 4.1 Declaração e chamada

**Sintaxe:** 

```c
tipo_de_retorno nome_da_funcao(tipo_de_dado_1 arg1, tipo_de_dado_2 arg2, ...) { 
    instruções 
}
```

Toda função deve ter seu tipo de retorno definido (`void` se não retornar nada) e pode admitir argumentos. Funções precisam ser escritas **acima** das funções que as chamam (por isso `main` costuma ser a última função do arquivo).

### Protótipos

Ferramenta que "pré-compila" as funções, permitindo posicioná-las livremente no código (como declarar uma variável e definir seu valor depois). No protótipo, basta declarar o tipo dos argumentos. Ainda assim, o protótipo precisa ficar acima de qualquer função que chame a função referenciada.

```c
#include <stdio.h>

int dobro(int);             // Protótipo - precisa vir acima, pois é chamado em funcaoSemPrototipo

void funcaoSemPrototipo(int valor){
    printf("%d\n", dobro(valor));
}

int funcaoComPrototipo();   // Protótipo que pode ficar abaixo

int main(){
    funcaoSemPrototipo(funcaoComPrototipo()); // Mesmo que: funcaoSemPrototipo(25)
    return 0;               // output: 50
}
int dobro(int valor){
    return (valor * 2);
}
int funcaoComPrototipo(){   // void implícito no argumento
    return 25;
}
```

## 4.2 Parâmetros e retorno

Parâmetros são variáveis locais declaradas no escopo da função, que copiam os dados passados na chamada. Em C padrão, a quantidade de parâmetros de uma função é fixa (ver seção 9). O tipo de retorno precisa ser definido (`void` indica que a função não retorna valor).

### Parâmetros do `main` (`argc`/`argv`)

A função `main` pode receber parâmetros vindos da linha de comando:

`int main(int argc, char* argv[])`.

Ao executar o programa pelo terminal, os elementos digitados após o nome do executável (separados por espaço) são salvos em `argv`, e a quantidade de elementos é salva em `argc`.

```c
#include <stdio.h>

int main(int argc, char* argv[]){
    printf("Ultima mensagem: %s\n", argc == 1 ? "Nao foi enviado nada" : argv[argc - 1]);
    return 0;
}
```

**Observação:** se nenhum argumento for passado, `argc == 1` e `argv[0]` fica vazio (armazena apenas `'\n'`).

## 4.3 Passagem de argumentos

Dentro de uma função, os valores passados na chamada são copiados para as variáveis locais correspondentes.

* **Passagem por valor:** ao passar um valor comum, ele é copiado para a variável local da função (não há interação com a variável original).
* **Passagem por referência (via ponteiro):** ao passar um endereço, ele é copiado para o ponteiro local da função, que passa a interagir diretamente com a variável original (ver seção 8).

```c
#include <stdio.h>

void divisao(int valor, int* referencia){ // Recebe um int (valor) e o endereço de um int (referência)
    valor /= 2;                           // Cópia local (não afeta a variável original)
    *referencia /= 2;                     // Ponteiro para o endereço da variável original (afeta ela)
}
int main(){
    int a = 10, b = 10;
    divisao(a, &b);                       // Envia o valor de a e o endereço de b
    printf("a = %d, b = %d\n", a, b);     // output: a = 10, b = 5
    return 0;
}
```

## 4.4 Recursão

**Mecanismo em C:** uma função chama a si mesma; quando a condição de parada é atingida, as chamadas retornam seus resultados gradualmente.

```c
#include <stdio.h>

void fibonacci_recursivo(int antecessor, int atual, int termo){
    printf("%d, ", antecessor);
    if (--termo > 1)
        fibonacci_recursivo(atual, antecessor + atual, termo);
    else
        printf("%d\n", atual);
}
int main(){
    fibonacci_recursivo(1, 1, 15); // Imprime os 15 primeiros termos de Fibonacci
    return 0;
    // output: 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610
}
```

**Observação:** cada chamada recursiva mantém suas variáveis alocadas e ativas na *stack* até retornar - uma função com *n* variáveis, a cada chamada recursiva, adiciona um novo *frame* com mais *n* variáveis à pilha.
Uso prolongado reduz a eficiência e, em casos extremos (memória limitada), pode causar *stack overflow*.

---

# 5. Estruturas de dados

## 5.1 Arrays e vetores

**Conceito:** São coleções de variáveis do mesmo tipo agrupadas sob um único nome, armazenadas de forma sequencial na memória. 

**Declaração:** Um *array* de `n` elementos é declarado como: `tipo_de_dado nome[n];`, sendo indexado de `nome[0]` até `nome[n-1]`.

**Observação:** Em geral, múltiplos elementos de um *array* só podem ser inicializados simultaneamente durante a declaração; alterações posteriores são feitas individualmente (frequentemente usando *loops*).

```c
#include <stdio.h>

int main() {
    int array_vazio[10];                // Declarado, mas com lixo de memória
    int array_zerado[5] = {0};          // {0, 0, 0, 0, 0}
    int array_inferido[] = {4, 5, 6};   // O compilador infere o tamanho como 3

    int notas[4] = {8, 7, 9, 6};        // Inicialização na declaração
    
    notas[3] = 10; // Modificação individual posterior do último elemento
    
    for (int i = 0; i < 4; i++) {
        printf("%d ", notas[i]);
    }
    printf("\n"); // output: 8 7 9 10

    return 0;
}
```

### Matrizes (arrays multidimensionais)

Um vetor é uma matriz de dimensão 1; matrizes de *n* dimensões são conjuntos de matrizes de dimensão *n-1*. Na declaração, é obrigatório definir o tamanho de todas as dimensões.

```c
int matriz_2[3][3] = {0};                       // Todos zerados
int matriz_3[3][3] = {1, 2, 3};                 // {{1,2,3},{0,0,0},{0,0,0}}
int matriz_4[3][3] = {{9, 8, 7}, {6, 5, 4}};    // {{9,8,7},{6,5,4},{0,0,0}}
int matriz_5[2][5] = {1,2,3,4,5,6,7,8,9,10};    // {{1,2,3,4,5},{6,7,8,9,10}}
```

```c
#include <stdio.h>

int main() {
    int matriz_zerada[3][3] = {0};                    // Todos os elementos zerados
    int matriz_linear[2][3] = {1, 2, 3, 4, 5, 6};     // Preenchimento sequencial
    
    // Inicialização aninhada (mais legível)
    int grade[2][3] = {
        {9, 8, 7}, 
        {6, 5, 4}
    };    
    
    // Acessando os elementos da matriz aninhada
    for(int i = 0; i < 2; i++) {
        for(int j = 0; j < 3; j++) {
            printf("%d ", grade[i][j]);
        }
        printf("\n");
    }
    // output: 
    // 9 8 7 
    // 6 5 4
    
    return 0;
}
```

## 5.2 Strings

*Strings* não são tipos nativos em C; elas são manipuladas como *arrays* de `char` em que o seu final é estritamente demarcado pelo caractere nulo (`'\0'`). Esse caractere nulo dita o tamanho "lógico" da string, permitindo que a cadeia de texto varie dentro de um *array* fixo maior.

**Inicialização e mutabilidade:**

```c
// Array mutável (alocado no stack, cada caractere pode ser modificado individualmente)
char string_mutavel[] = "Texto"; // Cria um array de 6 posições ('T', 'e', 'x', 't', 'o', '\0')
string_mutavel[0] = 'M';         // Agora é "Mexto"

// Ponteiro para string literal (normalmente alocada em memória de leitura, imutável)
char* string_literal = "Texto";
// string_literal[0] = 'M';      // ERRO: comportamento indefinido, possivelmente crash.
```

O ferramental completo para manipulação (*strcpy*, *strcat*, etc) fica em `string.h` (ver seção 9).

## 5.3 Estruturas compostas

### `struct`

**Conceito:** tipo derivado de dados que agrupa variáveis de múltiplos tipos relacionados entre si. Simula, de forma primitiva, algo como um
"objeto" de linguagens orientadas a objeto (sem funções internas, encapsulamento ou polimorfismo).

**Sintaxe:** 

```c
struct nome { 
    tipo_de_dado elem_1; 
    tipo_de_dado elem_2; 
    ... 
};
```

```c
struct carta {
    char *naipe, *face;
    int valor;
};
typedef struct carta carta;             // Apelido mais curto para o tipo (antes era obrigatório declarar struct carta nome_da_variavel)
typedef struct carta CartaDeBaralho;    // Outro apelido possível

int main(){
    struct carta a;
    a.naipe = "Paus"; a.valor = 9; a.face = "Nove";

    carta b = {"Ouros", "Rei", 10};                                    // Inicialização ordenada
    CartaDeBaralho c = {.face = "As", .valor = 11, .naipe = "Copas"};  // Inicialização nomeada (ordem livre)
    return 0;
}
```

**ATENÇÃO:** não é possível predefinir valores para os membros de uma `struct` em sua própria definição - só é possível atribuir valores durante ou depois da declaração de uma variável desse tipo. Essa estrutura, quando associada a um ponteiro para si mesma (auto-referência), cria a base para estruturas encadeadas como listas ligadas e árvores (ver seção 8).

## 5.4 Enumerações

As enumerações permitem agrupar e nomear constantes inteiras sob um tipo unificado, auxiliando fortemente na legibilidade e manutenção do código.

**Sintaxe e uso:**

```c
// Cria um tipo enum onde, por padrão, o primeiro item vale 0 e o restante incrementa em 1.
enum DiaSemana { DOMINGO, SEGUNDA, TERCA, QUARTA, QUINTA, SEXTA, SABADO };

// Pode-se definir valores explícitos.
enum Estado { DESLIGADO = 0, LIGADO = 10, EM_ESPERA = 15 };

int main() {
    enum DiaSemana hoje = SEGUNDA; // hoje == 1
    if (hoje == SEGUNDA) { /* ... */ }
    return 0;
}
```

---

# 6. Paradigmas e abstração

## 6.1 Tipos definidos pelo usuário

`typedef` cria um apelido para um tipo já existente (muito usado para encurtar nomes de `struct`s e `enum`s).

```c
typedef struct carta carta;      // "carta" passa a ser um apelido para "struct carta"
typedef struct lista* listaPtr;  // Apelido para ponteiro de struct (bastante usado com listas)
```

Além de `typedef`, `struct` e `enum` e `union` (ver seção 5/8) também funcionam como mecanismos de criação de tipos.

---

# 7. Memória e gerenciamento de recursos

## 7.1 Modelo de memória

Como modelo didático, a memória de um programa em C pode ser representada organizada da região mais baixa (endereço menor) até a mais alta, dividida por tipo de alocação:

* **Alocação estática:** região mais baixa, onde ficam os elementos fixos do programa (variáveis globais e `static`, código de `main`, definição de funções etc.) - memória alocada durante toda a execução.
* **Alocação automática (*stack*/pilha):** região mais alta; o compilador aloca e desaloca automaticamente as informações temporárias das funções (parâmetros, variáveis locais, endereço de retorno). Novos dados costumam ser colocados no "topo" da pilha (um novo *frame* por chamada, em um endereço menor).
* **Alocação dinâmica (*heap*):** região logo após a estática; controle manual pelo programador (alocação e liberação). Novos dados costumam ser salvos na base do *heap* (endereços crescentes).
* **Memória livre:** região compartilhada de onde *stack* e *heap* retiram espaço conforme a necessidade.

**Cuidado:** esta divisão é um modelo didático, não uma descrição universal do funcionamento interno de todo compilador/sistema.

## 7.2 Alocação

* **Estática:** o código do programa, variáveis globais e `static` - alocadas durante toda a execução (ver seção 8).
* **Automática:** variáveis locais, parâmetros, chamadas de função - alocadas e desalocadas automaticamente pelo compilador.
* **Dinâmica:** realizada e gerenciada diretamente pelo programador, do início (reservar espaço no *heap*) ao fim (liberar o espaço depois de usar) (ver seção 8). As funções para isso ficam na biblioteca `stdlib.h` (ver seção 9).

```c
#include <stdlib.h>

int* ptr = (int*) malloc(sizeof(int)); // Aloca memória para um int (com lixo de memória)
*ptr = 10;
free(ptr);                             // Desaloca antes de reutilizar o ponteiro
```

## 7.3 Tempo de vida

Variáveis automáticas (locais) deixam de existir ao final do bloco onde foram declaradas; variáveis `static` preservam seu valor entre chamadas da função, mas continuam com escopo local; variáveis globais e `extern` existem durante toda a execução do programa (ver seção 8).

---

# 8. Recursos característicos da linguagem

## 8.1 Ponteiros

**Conceito:** elementos especiais que armazenam o endereço de outra variável, permitindo alterá-la sem contato direto (o endereço é acessado com o prefixo `&`).

**Sintaxe:** `tipo_de_dado* nome = &variável;`. 

Para desreferenciar (acessar o valor apontado): `*nome`.

```c
#include <stdio.h>

int main(){
    int a = 10;
    int* b;             // Declaração de um ponteiro de int
    b = &a;             // b recebe o endereço de a
    (*b)++;             // Equivalente a a++
    printf("%d\n", a);  // output: 11

    int* c = &a;        // Declaração e atribuição de um ponteiro
    int** d, * e;       // d: ponteiro para ponteiro de int; e: ponteiro de int
    d = &b;             // d == &b: *d == b == &a: **d == *b == a
    return 0;
}
```

Podem existir ponteiros para qualquer tipo, inclusive ponteiros de ponteiros (`**`, `***`, e assim por diante).

### Ponteiros e Arrays (Vetores)

Um *array* pode funcionar como um ponteiro para o endereço do seu primeiro elemento (o array "decai" para um ponteiro). Por isso, a linguagem aceita usá-lo como ponteiro, e vice-versa.

```c
#include <stdio.h>

void funcao_com_array(int arr[]) {
    for (int i = 0; i < 3; i++) printf("%d ", arr[i]);
    printf("\n");
}
void funcao_com_ponteiro(int *ptr) {
    for (int i = 0; i < 3; i++) printf("%d ", ptr[i]); // Poderia ser *(ptr + i)
    printf("\n");
}
int main(){
    int array[3] = {1, 2, 3};
    int* ptr = array;            // Ponteiro aponta para o primeiro elemento (array[0])
                                 // Aritmética de ponteiros: ptr[n] == array[n] == *(ptr + n) == *(array + n)
    funcao_com_array(array);     // output: 1 2 3
    funcao_com_array(ptr);       // output: 1 2 3
    funcao_com_ponteiro(ptr);    // output: 1 2 3
    return 0;
}
```
### Ponteiros e Matrizes (Arrays multidimensionais)

Matrizes também podem ser representadas por ponteiros múltiplos (`int**`), mas os dois modelos possuem naturezas distintas e não devem ser misturados:

* Uma função que espera `int matriz[][n]` exige uma matriz de verdade (memória contígua, alocada de forma sequencial).

* Uma função que espera `int**` exige um vetor de ponteiros (um array cujos elementos são ponteiros para outras áreas de memória, que não estão necessariamente sequenciais).

**ATENÇÃO:** Confundir uma matriz verdadeira com um vetor de ponteiros é um erro comum e gera problemas de compilação, pois cada uma exige uma assinatura de função diferente.

### Ponteiros para função

Assim como guardam endereços de variáveis, ponteiros podem armazenar o endereço de memória onde residem as instruções de uma função, permitindo passá-la dinamicamente por parâmetro (muito usado em *callbacks* e polimorfismo primitivo).

```c
int soma(int a, int b) { return a + b; }

int main() {
    // Declaração de um ponteiro para uma função que recebe (int, int) e retorna int
    int (*ptrFunc)(int, int) = &soma;
    int resultado = ptrFunc(5, 5);      // Chama a função, resultado = 10
    return 0;
}
```

### Ponteiros constantes e constantes apontadas

A posição da palavra-chave `const` afeta o que está sendo trancado de modificações.

```c
int valor = 10, outro = 20;

// O valor apontado é constante, o ponteiro não.
const int* ptr1 = &valor; 
ptr1 = &outro;    // Válido
// *ptr1 = 30;    // ERRO

// O ponteiro é constante, o valor apontado não.
int* const ptr2 = &valor;
*ptr2 = 30;       // Válido
// ptr2 = &outro; // ERRO

// Ambos são constantes.
const int* const ptr3 = &valor;
```

## 8.2 Gerenciamento manual de memória

As funções de alocação dinâmica (`malloc`, `calloc`, `free`, `realloc`) ficam na biblioteca `stdlib.h` (ver seção 9). Após alocar, é responsabilidade do programador liberar a memória (`free`) quando ela não for mais necessária.

### Erros comuns envolvendo controle manual:

* **Vazamento de memória (*Memory Leak*):** se a memória alocada dinamicamente não for liberada (por exemplo, ao reatribuir um ponteiro sem antes chamar `free` no endereço anterior), ela permanece alocada mas inacessível. O uso prolongado pode esgotar a RAM do sistema.
* **Double free:** Tentar dar `free()` duas ou mais vezes no mesmo endereço de memória. Isso corrompe as estruturas internas do *heap* gerenciado pela biblioteca C.
* **Use-after-free:** Tentar dereferenciar (acessar ou editar) um ponteiro que já passou pelo `free()`. É um comportamento indefinido.
* **Ponteiro pendente (*Dangling pointer*):** Após um `free()`, o endereço de memória que o ponteiro armazena ainda existe lá dentro (embora não deva ser acessado). Para prevenir os dois erros anteriores, uma boa prática universal é atribuir `NULL` imediatamente a qualquer ponteiro após seu `free()`.

**ATENÇÃO:** depois de usar `malloc`/`calloc`, é importante verificar se o ponteiro retornado é diferente de `NULL` antes de usá-lo - caso contrário, a alocação pode ter falhado (ver seção 9).

## 8.3 Preprocessador e macros

**Conceito:** diretivas (`#comando`) executadas antes da compilação - inclusão de arquivos, definição de constantes/macros, compilação condicional etc.

### `#include`

Inclui arquivos para a execução do programa.

```c
#include <stdio.h>    // Biblioteca padrão
#include "mylib.h"    // Biblioteca/arquivo próprio
```

### `#define` e `#undef`

`#define` cria (e copia) uma macro - constante ou "função"; `#undef` a
remove.

```c
#define PI 3.14
#define NOME_PROGRAMA "Calculadora Financeira"
#define AREA_CIRCULO(raio) (PI * (raio) * (raio)) // Macro do tipo "função"
#undef DEZ
```

### `#if`, `#ifdef`, `#ifndef`, `#elif`, `#else`, `#endif`

Testam a condição de uma diretiva, válida até o próximo teste ou até
`#endif`.

```c
#ifndef DEZ // if (!defined(DEZ)); também existe #ifdef
#warning "DEZ nao definido!"
#define DEZ 15
#endif
#if (DEZ > 20)
#undef DEZ
#define DEZ 25
#elif (DEZ < 15)
#undef DEZ
#define DEZ 5
#else
#undef DEZ
#define DEZ 10
#endif
```

### Macros predefinidas

* **`__LINE__`:** `int` com o número da linha atual do código.
* **`__FILE__`:** `string` com o nome do arquivo.
* **`__DATE__`:** `string` com a data atual (`Mmm dd aaaa`, ex.: `Feb  7 2026`).
* **`__TIME__`:** `string` com a hora atual (`hh:mm:ss`).

## 8.4 Classes de armazenamento (`auto`, `static`, `extern`)

* **`auto`:** duração apenas durante a execução da função onde foi declarada; não acessível diretamente por outras funções/arquivos. É o tipo implícito de uma variável local (não precisa declarar explicitamente).
* **`static`:** dentro de uma função, faz com que a variável mantenha seu espaço de memória (e seu valor entre chamadas) durante toda a execução do programa, mas continua com escopo local à função. Como elemento global, impede o acesso direto por código externo ao arquivo (só sendo alcançado indiretamente, como por funções).
* **`extern`:** define elementos acessíveis por todo o programa (código local e externo). É o tipo implícito de elementos globais. Não pode ser declarado dentro de blocos.

```c
#include <stdio.h>

int contExtern = 0; // extern int contExtern = 0;
void inicializacao() {
    static int contStatic = 0;
    int contAuto = 0; // auto int contAuto = 0;
    contExtern++, contStatic++, contAuto++;
    printf("%d, %d, %d\n", contExtern, contStatic, contAuto);
}
int main() {
    inicializacao(); // output: 1, 1, 1
    inicializacao(); // output: 2, 2, 1
    inicializacao(); // output: 3, 3, 1
    return 0;
}
```

## 8.5 `union`

**Conceito:** É uma estrutura de dados semelhante a uma `struct`, contudo, todos os seus campos compartilham exatamente a mesma localização de memória. Consequentemente, o tamanho total da `union` é equivalente ao tamanho de seu maior membro, e apenas um membro pode armazenar um valor válido por vez.

```c
union Dado {
    int inteiro;
    float decimal;
};

int main() {
    union Dado d;
    d.inteiro = 10;
    printf("%d\n", d.inteiro);   // Funciona: 10
    
    d.decimal = 3.14; 
    // d.inteiro agora contém lixo porque a memória foi reescrita pela conversão em float
    printf("%.2f\n", d.decimal); // Funciona: 3.14
    return 0;
}
```

---

# 9. Biblioteca padrão

## 9.1 Entrada e saída

**`scanf`:** recebe uma *string* do *input* (terminal), converte os dados conforme os formatadores definidos e grava os valores nos endereços das variáveis fornecidas. **`printf`:** recebe informações de vários tipos, converte para *string* (via formatadores) e imprime no *output* padrão (terminal).

**ATENÇÃO:** o `scanf` pode se comportar de forma inesperada por causa do *buffer* (área de memória que guarda dados de *input* provisoriamente) estar "sujo" antes de sua execução. Para contornar, basta colocar um espaço antes do primeiro formatador (`" %d"`), ou usar um `getchar` vazio / `fgets` para limpar o *buffer*.

```c
#include <stdio.h>

int int_a; char char_b, vet_d[100], vet_e[100]; double double_f;
scanf(" %d %c", &int_a, &char_b); // input assumido: 10 abc

printf("int_a = %d, pi = %.2lf, Nome: %s\n", int_a, 3.141592, "Maria");
// output: int_a = 10, pi = 3.14, Nome: Maria

sprintf(vet_e, "char_b = %c, num = %.4lf\n", char_b, 2.15); // printf para array de char
printf("vet_e: %s", vet_e);

char_b = getchar();       // Pega o próximo caractere do buffer
putchar(char_b);          // Imprime um char

char linha[100];
fgets(linha, 100, stdin); // Lê uma linha (ou limpa o buffer)
puts(linha);              // Imprime uma string, adicionando '\n' ao final

sscanf(vet_e, "char_b = %c, num = %lf", &char_b, &double_f); // "scanf" de uma string
```

## 9.2 Memória e utilidades

```c
#include <stdlib.h>

int* intptr = (int*) malloc(sizeof(int));          // Aloca memória (com lixo)
int* intarr = (int*) calloc(10, sizeof(int));      // Aloca e ZERA a memória
intarr = (int*) realloc(intarr, 20 * sizeof(int)); // Redimensiona a memória alocada
free(intptr);
free(intarr);
```

**Observação:** `malloc` e `calloc` retornam, por padrão, um ponteiro `void*`; a conversão para o tipo de ponteiro desejado é automática em C (o *cast* explícito, como em `(int*) malloc(...)`, é facultativo em C, embora obrigatório em C++).

**ATENÇÃO:** É importante sempre verificar se o ponteiro retornado é diferente de `NULL` antes de usá-lo (podem ocorrer falhas de alocação, levando a bugs silenciosos e comportamento indefinido) (ver seção 8).

## 9.3 Arquivos

Cria-se um ponteiro para arquivo (`FILE* ponteiro;`) para navegar e manipular o arquivo, conforme o modo de acesso escolhido. Ao final, o ponteiro deve ser desalocado (`fclose`), garantindo o salvamento.

### Modos de acesso (texto)

| Modo | Efeito |
| --- | --- |
| `r` | Abre para leitura apenas. |
| `w` | Cria/sobrescreve, para escrita apenas. |
| `a` | Abre/cria, para anexar conteúdo ao final. |
| `r+` | Abre para leitura e/ou escrita. |
| `w+` | Cria/sobrescreve, para leitura e/ou escrita. |
| `a+` | Abre/cria, para leitura e/ou anexar ao final. |

Os equivalentes binários são, respectivamente: `rb`, `wb`, `ab`, `rb+`, `wb+`, `ab+`.

### Texto

Todo o conteúdo é tratado como *string*, exigindo conversão para o tipo desejado.

```c
#include <stdio.h>

FILE* filePointer;
if ((filePointer = fopen("dados.dat", "r")) == NULL) return -1; // Erro ao abrir
for (int i = 0; !feof(filePointer); i++)
    fscanf(filePointer, " %d %lf", &conta[i], &saldo[i]); // Espaço antes do %d limpa o buffer
fclose(filePointer);

if ((filePointer = fopen("novo.txt", "w")) == NULL) return -1;
for (int j = 0; conta[j] != 0; j++)
    fprintf(filePointer, "%d - %.1lf\n", conta[j], saldo[j]);
fclose(filePointer);
```

**ATENÇÃO:** Assim como na alocação dinâmica pelo `malloc`, é importante sempre verificar se a alocação dinâmica e se o ponteiro do arquivo foram bem-sucedidas (`!= NULL`) antes de usar o ponteiro.

### Binário

A informação é armazenada numericamente, em blocos de memória (os valores não são convertidos em *string*).

```c
#include <stdio.h>

typedef struct { int conta; double saldo; } Dados;

FILE* filePointer;
if ((filePointer = fopen("dados.bin", "wb")) == NULL) return -1;
fwrite(&dados[i], sizeof(Dados), 1, filePointer); // (endereço, tamanho por objeto, quantidade, ponteiro)
fclose(filePointer);

if ((filePointer = fopen("dados.bin", "rb")) == NULL) return -1;
fread(&lidos[i], sizeof(Dados), 1, filePointer);
fclose(filePointer);
```

**Observação:** `rewind(ponteiro)` volta o ponteiro do arquivo para o
início (não disponível nos modos `a`/`ab`).

## 9.4 Parâmetros variáveis

A biblioteca `stdarg.h` permite uma quantidade variável de argumentos (é necessário pelo menos um argumento fixo).

```c
#include <stdio.h>
#include <stdarg.h>

double media(double total, int i, ...){
    va_list pointer;                            // "Ponteiro" para a lista de argumentos variáveis
    va_start(pointer, i);                       // Posiciona o ponteiro após o último argumento fixo (i)
    for (int j = 0; j < i; j++)
        total += va_arg(pointer, double);       // Acessa o próximo argumento (tamanho double)
    va_end(pointer);                            // Zera o ponteiro
    return (total / i);
}
int main(){
    double w = 38.5, x = 22.5, y = 1.7, z = 10.2;
    printf("%.3lf\n", media(0, 2, w, x));       // output: 30.500
    printf("%.3lf\n", media(0, 3, w, x, y));    // output: 20.900
    printf("%.3lf\n", media(0, 4, w, x, y, z)); // output: 18.225
    return 0;
}
```

## 9.5 Strings

Funções de `string.h` (manipulação) e `ctype.h` (classificação/conversão de caracteres), normalmente usadas em conjunto:

```c
#include <string.h>
#include <ctype.h>

// ctype.h - funções de comparação (retornam 1/0)
isdigit('8'); isalpha('b'); isupper('F'); islower('c'); isalnum('A'); isspace(' ');
// ctype.h - funções de modificação (retornam o mesmo char se não for possível alterar)
toupper('u'); tolower('W');

char* str1 = "Feliz aniversario", str2[20], str3[20];
strlen(str1);                       // Quantidade de caracteres (sem contar o '\0')
strcpy(str2, str1);                 // Copia str1 para str2
strncpy(str3, str2, 5);             // Copia os 5 primeiros caracteres

char str6[20] = "";
strcat(str6, "Feliz ano novo");     // Anexa ao final de str6
strncat(str6, "Feliz ano novo", 5); // Anexa os 5 primeiros caracteres

strcmp("Abc", "Abc");               // 0 se as strings forem iguais
strncmp("Abc", "Abd", 2);           // Compara os n primeiros caracteres

strchr("Uma maquina voadora", 'q'); // A partir da primeira ocorrência do char (NULL se não achar)
strstr("O bebe saiu dai", "iu");    // A partir da primeira ocorrência de uma substring
```

**Conversão string → número (`stdlib.h`):**

```c
#include <stdlib.h>

int a = atoi("99");                                // string (apenas dígitos) -> int
double b = strtod("51.2% foram admitidos", &cPtr); // com detecção do que sobrou (b = 51.2)
long bin = strtol("100101abc", &cPtr2, 2);         // base 2; bin = 37, cPtr2 aponta para "abc"
```

## 9.6 Matemática

```c
#include <math.h>

ceil(98.0001);          // 99.00 - arredonda para cima
floor(10.8);            // 10.00 - arredonda para baixo
sqrt(8);                // Raiz quadrada
pow(27, 1.0/3);         // Potenciação (também usada para raízes)
sin(x); cos(x); tan(x);
log(2.71828);           // Logaritmo natural
log10(1000);            // Logaritmo na base 10
```

**`stdlib.h` (utilidades numéricas):**

```c
#include <stdlib.h>

int c = abs(-25);        // Valor absoluto -> 25
div_t res = div(70, 30); // Divisão inteira com quociente e resto: res.quot = 2, res.rem = 10
```

## 9.7 Algoritmos prontos

`qsort` (ordenação) e `bsearch` (busca binária), ambos de `stdlib.h`, recebem uma função de comparação como parâmetro (implicitamente, um ponteiro de função) (ver seção 8).

```c
#include <stdlib.h>

int crescente(const void* a, const void* b) {
    return (*(int*) a - *(int*) b); // > 0 troca a posição dos elementos
}
int main(){
    int numeros[] = {42, 13, 7, 99, 1, 25};
    int n = sizeof(numeros) / sizeof(numeros[0]);
    qsort(numeros, n, sizeof(int), crescente); // numeros = {1, 7, 13, 25, 42, 99}

    int chave = 25;
    int* resultado = bsearch(&chave, numeros, n, sizeof(int), crescente);
    if (resultado != NULL) printf("Encontrado: %d\n", *resultado); // output: Encontrado: 25
    return 0;
}
```

## 9.8 Datas e tempo

A biblioteca `<time.h>` fornece componentes e funções para gerenciamento de tempo e representações de data.

```c
#include <time.h>
#include <stdio.h>

int main() {
    time_t rawtime = time(NULL);                // Capta o tempo em formato numérico
    struct tm* data_hora = localtime(&rawtime); // Converte para o tempo local 
    printf("Atual: %s", asctime(data_hora));    // Converte e imprime como string formatada
    return 0;
}
```

### Assertions

A biblioteca `<assert.h>` fornece a macro `assert(condição)`, projetada para atestar pressupostos e apoiar o *debugging*. Se a condição entregue à *assertion* retornar falso (`0`), a execução do programa é imediatamente abortada exibindo o arquivo e a linha do erro no terminal. Costuma ser desabilitada na construção da versão de lançamento via pré-processador.

---

# 10. Ferramentas e ecossistema básico

## 10.1 Compilador

O código `.c` precisa obrigatoriamente ser traduzido em linguagem de máquina via software. O compilador mais consolidado e utilizado do ecossistema C é o **GCC** (*GNU Compiler Collection*).

**Fluxo básico por linha de comando:**

1. Escrever o código no arquivo `programa.c`.
2. Compilar invocando `gcc programa.c -o programa`.
3. Executar o binário gerado invocando `./programa` (Linux/MacOS) ou `programa.exe` (Windows).

## 10.2 Gerenciador de pacotes

Diferente de linguagens modernas, C não possui um gerenciador de pacotes nativo universal e padronizado em volta da linguagem. A obtenção de bibliotecas externas ocorre primariamente:

* Através do gerenciador de pacotes do próprio Sistema Operacional (`apt` no Ubuntu, `pacman` no Arch, `brew` no MacOS);
* Utilizando gerenciadores independentes de projetos modernos, como o `Conan` ou `vcpkg`;
* Realizando o *build* manual a partir dos arquivos e código-fonte disponibilizado pelo autor.

## 10.3 Build (Compilação e Múltiplos Arquivos)

Programas reais com múltiplos arquivos-fonte (`.c`) e cabeçalhos (`.h`) precisam ser compilados em conjunto. Todavia, o processo pode ser automatizado utilizando o **Make** (através de um arquivo `Makefile`) ou o gerador de projetos **CMake**, orquestramdp as rotinas de compilação sem exigir que comandos gigantescos e repetitivos sejam digitados a cada teste.

### Include guards e organização

Para compilar de modo unificado, divide-se a arquitetura: o **arquivo fonte** (`.c`) guarda a implementação real, e o **arquivo cabeçalho/header** (`.h`) guarda as interfaces (protótipos de funções, structs). O *include guard* (usando pragmas do pré-processador) previne que o mesmo cabeçalho seja incluído duas vezes e trave a compilação gerando erros de redefinição.

```c
/* mylib.h */
#ifndef QUALQUER_NOME_H
#define QUALQUER_NOME_H
typedef struct { int x, y; } Ponto;
double media(double*, int); // Protótipo
#endif

/* mylib.c */
#include "mylib.h"
double media(double* valores, int quantidade){
    double total = 0;
    for (int i = 0; i < quantidade; i++) total += valores[i];
    return total / quantidade;
}

/* main.c */
#include <stdio.h>
#include "mylib.h"
int main(){
    double valores[] = {10, 20, 30};
    printf("Media = %.2lf\n", media(valores, 3));
    return 0;
}
```

---

# 11. Cuidados e boas práticas

## 11.1 Erros comuns

Vários erros comuns já foram registrados nas seções:

* variável usada sem inicialização (lixo de memória) (ver seção 2);
* comparação `signed` × `unsigned` sem conversão explícita (ver seção 2);
* *buffer* do `scanf` "sujo" antes da leitura (ver seção 9);
* *overflow*/*underflow* em tipos numéricos pequenos (ver seção 2);
* ponteiro não verificado após `malloc`/`calloc` e `fopen` (ver seção 8/9);
* vazamento de memória por não liberar antes de reatribuir um ponteiro (ver seção 8);

## 11.2 Comportamentos perigosos

Muitos dos desastres e falhas em programas C emanam do chamado **Comportamento Indefinido** (*Undefined Behavior* ou UB). O Padrão de C dita regras sobre o que não pode ser feito (como acessar um array fora de seus limites, tentar escrever em um ponteiro de string nulo, ou dividir inteiros por zero), no entanto, a linguagem não dita o que *deve acontecer* se o erro for efetivado - o compilador frequentemente omite mecanismos de segurança presumindo que o seu código nunca ativará um UB, gerando execuções corrompidas ou silenciosas, tornando o teste manual da memória e o controle preciso muito mais exigentes do que em linguagens seguras.

## 11.3 Recursos desencorajados

**`goto`** (ver seção 3): seu uso é desencorajado, pois deixa o programa desorganizado (dificultando *debugging* e manutenção) e pode causar falhas lógicas (como avançar para uma área do código que depende de uma variável que deveria ter sido declarada, mas cuja instrução de declaração foi pulada pelo `goto`).

---

# 12. Referências

1. DEITEL, Harvey M.; DEITEL, Paul J. *Como programar em C*. 2. ed. Rio de Janeiro: LTC, 1994.
2. ZIVIANI, Nivio. *Projeto de algoritmos com implementações em Pascal e C*. 4. ed. São Paulo: Pioneira, 1999.
3. BATISTA, Natália Cosse. *Ponteiros e alocação dinâmica de memória*. 2022. 40 f. Slides (PDF) da disciplina Algoritmos e Estruturas de Dados. Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2025.
4. PEIXOTO, Daniela Cristina Cascini. *Disciplina: Lógica de programação*. Curso de graduação em Engenharia de Computação - CEFET-MG, 2024.
5. CAMPOS, Luciana Maria de Assis. *Disciplina: Programação orientada a objetos*. Curso de graduação em Engenharia de Computação - CEFET-MG, 2024.
6. BATISTA, Natália Cosse. *Disciplina: Algoritmos e estruturas de dados*. Curso de graduação em Engenharia de Computação - CEFET-MG, 2025.
7. cppreference.com. *C reference*. Disponível em: [https://en.cppreference.com/w/c](https://en.cppreference.com/w/c). Acesso em: 04 ago. 2026.
