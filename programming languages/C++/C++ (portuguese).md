```
 .´‾‾‾‾‾‾‾`.
/     _____|    _       _ 
▏   /         _| |_   _| |_ 
▏   ▏        |_   _| |_   _|
▏   \          |_|     |_|  
\     ‾‾‾‾‾|
 `.______.´
```

# 0. Características Básicas da Linguagem

> Esta apostila utiliza o **C++20** como referência.

C++ é uma linguagem de propósito geral, utilizada em aplicações e em componentes próximos ao *hardware*. Combina funções, classes e estruturas de controle com operações sobre endereços de memória e representações binárias.

## Paradigmas:

C++ é **multiparadigma**: permite combinar diferentes formas de organizar um programa. Na programação **imperativa**, as instruções modificam o estado do programa, representado pelos valores mantidos na memória.

```cpp
int saldo{100};
saldo = saldo + 50;  // 150.
saldo = saldo - 30;  // 120.
```

Na programação **procedural**, operações são agrupadas em funções reutilizáveis, como um cálculo de média aplicado a diferentes valores. A **programação estruturada** organiza o fluxo por sequência, decisão e repetição, como ao verificar uma compra ou percorrer um estoque.

A **programação orientada a objetos** permite reunir dados e operações em tipos próprios, como uma conta com saldo e operações de depósito e retirada. A **programação genérica** permite escrever ferramentas adaptáveis a diferentes tipos, como uma mesma estrutura para armazenar inteiros ou produtos.

Essas formas podem coexistir. Uma função simples não precisa pertencer a uma classe, e criar uma classe não exige montar uma hierarquia de herança.

## Relação com C:

C++ preserva grande parte da sintaxe e das ferramentas de C, mas as linguagens possuem regras próprias. Um programa válido em C pode exigir adaptações para compilar ou manter o mesmo comportamento em C++.

Os fundamentos compartilhados continuam úteis: condicionais, repetições, operações sobre bits e funções livres fazem parte da linguagem. Ao mesmo tempo, referências, classes e gerenciamento automático de recursos oferecem outras formas de resolver problemas.

> Recursos herdados de C não são automaticamente mais rápidos nem proibidos em C++. A escolha depende do problema, das interfaces utilizadas e de medições quando o desempenho for relevante. Os exemplos priorizam as ferramentas usuais de C++.

## Tipagem:

C++ possui **tipagem estática**: os tipos são conhecidos durante a compilação e orientam a interpretação dos dados e a verificação das operações. A linguagem admite conversões **implícitas**, automáticas, e **explícitas**, indicadas no código.

```cpp
int quantidade{3};
double preco{12.50};
double total = quantidade * preco;  // 37.50.
```

Na multiplicação, o valor de `quantidade` é convertido para `double` (a variável continua sendo `int`). Conversões podem perder informações ou produzir resultados inesperados.

> Deduzir um tipo com `auto` não torna a tipagem dinâmica: o compilador determina o tipo, que permanece o mesmo durante a execução.

## Nível de Abstração:

Funções, classes e bibliotecas permitem programar sem descrever cada instrução da CPU. Ao mesmo tempo, C++ oferece acesso a endereços, bits e organização da memória. O nome de uma variável identifica um objeto; ponteiros permitem trabalhar também com seu endereço.

Uma ferramenta pode esconder detalhes sem retirar o controle do programa. Por exemplo, um objeto pode administrar uma área de memória sem exigir que cada trecho que o utiliza repita os cuidados de liberação.

> Proximidade com o *hardware* não significa acesso irrestrito à memória: em sistemas operacionais, o programa continua sujeito às permissões e aos limites de seu processo.

## Compilação e Execução:

No uso habitual, arquivos `.cpp` são traduzidos pelo **compilador**, e o **ligador** combina as partes necessárias ao executável. Essa tradução antecipada precisa ser refeita após mudanças no código-fonte para que apareçam no programa executado.

> O **GCC**, utilizado por meio de `g++`, é uma das ferramentas disponíveis. Pré-processamento, compilação e ligação serão aprofundados no capítulo correspondente.

### Desempenho:

O controle dos dados e da memória, combinado às otimizações do compilador, permite construir programas eficientes. O desempenho depende também do algoritmo, da implementação e do *hardware* (a linguagem, isoladamente, não garante velocidade).

Uma abstração não acrescenta necessariamente trabalho durante a execução: o compilador pode simplificar ou eliminar parte de sua implementação. Outras escolhas possuem custos, como alocações dinâmicas e determinadas formas de polimorfismo.

### Portabilidade:

Código padronizado pode ser recompilado para diferentes plataformas, mas o executável normalmente depende da arquitetura e do sistema de destino. Tamanhos de tipos, representações e recursos específicos do sistema também limitam a portabilidade.

## Gerenciamento de Memória:

C++ combina armazenamento **automático**, como o de variáveis locais comuns, e **dinâmico**, cujo tempo de utilização pode ultrapassar o bloco que o solicitou. A responsabilidade por liberar uma alocação dinâmica pode ser administrada por um objeto.

Esse vínculo entre recurso e tempo de vida do objeto é a base de **RAII**: ao encerrar sua existência, o objeto realiza a limpeza pela qual é responsável. *Smart pointers* aplicam essa ideia ao gerenciamento de objetos acessados por ponteiros.

> RAII não é um coletor de lixo e não se limita à memória: também pode cuidar de arquivos e outros recursos. Seu funcionamento será desenvolvido depois de classes, construção e destruição.

## Principais Aplicações:

- **Sistemas e ferramentas:** componentes de sistemas, compiladores, bancos de dados e bibliotecas.
- **Sistemas embarcados:** dispositivos com recursos limitados ou interação com periféricos.
- **Aplicações e jogos:** programas de uso geral, interfaces e motores gráficos.
- **Alto desempenho:** simulações e processamento que exigem controle do custo das operações e da memória.

## Características Marcantes:

- **Classes e objetos:** construção de tipos com dados, operações e regras próprias.
- **Referências e ponteiros:** acesso a objetos existentes sem necessariamente copiá-los.
- **Gerenciamento de recursos:** construção, destruição e liberação vinculada ao tempo de vida dos objetos.
- **Operações sobre bits:** manipulação da representação binária de inteiros.
- **Biblioteca padrão:** entrada e saída, texto, estruturas de dados, algoritmos e outras ferramentas.
- **Programação genérica:** criação de ferramentas parametrizadas por tipos e outros argumentos.
- **Pré-processamento e compilação:** inclusão de arquivos, seleção de trechos e verificações antes da execução.

---

# 1. Fundamentos da Linguagem

> Este capítulo reúne os fundamentos de programação e suas particularidades em C++: tipos de dados, operadores, estruturas de controle, grupos de dados, referências e funções.

> REVISADO ATÉ AQUI.

Os programas completos incluem cabeçalhos e `main`. Nos fragmentos, instruções pressupõem sua inserção em uma função; definições de funções e de espaços de nomes ficam fora dela. Exemplos com entrada e saída pressupõem `<iostream>`; os que utilizam `std::string`, também `<string>`. Fragmentos separados são independentes, salvo indicação de continuidade.

## Estrutura de um Programa:

Em programas convencionais sobre um sistema operacional, **`main`** é o ponto de entrada definido pela linguagem. `<iostream>` disponibiliza recursos de entrada e saída, e `#include` incorpora o cabeçalho durante o pré-processamento.

```cpp
#include <iostream>

int main() {                                // Retorno int; sem parâmetros.
    int quantidade{5};
    quantidade += 2;
    std::cout << "Quantidade: " << quantidade << '\n';  // 7.
    /* Comentário de bloco,
       que pode ocupar várias linhas. */
    return 0;                               // Encerramento bem-sucedido.
}
```

`std` é o **espaço de nomes** (*namespace*) da biblioteca padrão. Em `std::cout`, `::` indica onde procurar o nome `cout`, evitando confundi-lo com nomes definidos pelo programa. `<<` envia os valores para a saída, como será detalhado adiante.

As chaves delimitam **blocos**. O ponto e vírgula encerra declarações e instruções como atribuições, chamadas e retornos. Um bloco de `if` ou `for` normalmente não recebe `;` após a chave final (a definição de `struct` e o `do while` exigem esse delimitador).

Comentários `//` terminam na quebra de linha; `/* ... */` pode abranger várias linhas, mas não admite aninhamento. A indentação evidencia a organização, enquanto a sintaxe determina os blocos. Dentro de *strings*, espaços e quebras representadas fazem parte do conteúdo.

> Ambientes embarcados sem sistema operacional podem adotar outra inicialização, definida pela implementação. Nos programas deste capítulo, atingir o fim de `main` equivale a retornar zero.

### Compilação Básica:

Com o código em `programa.cpp`, `g++` produz o executável, que pode ser iniciado em um terminal Linux:

```sh
g++ -std=c++20 -Wall -Wextra -Wpedantic programa.cpp -o programa
./programa
```

`-std=c++20` seleciona o padrão; `-Wall` e `-Wextra` habilitam grupos de avisos; `-Wpedantic` solicita diagnósticos adicionais ligados ao padrão; `-o` define o nome da saída. `g++` também providencia a ligação usual com a biblioteca de C++.

> A ausência de avisos NÃO GARANTE que o programa funciona corretamente. Sem `-std=c++20`, a versão adotada depende do padrão configurado no compilador.

## Tipos de Dados e Variáveis:

O **tipo** determina valores representáveis e operações permitidas. Uma variável comum dá nome a um objeto que armazena dados. Como em um compartimento, o nome identifica o espaço, o tipo orienta a interpretação e a atribuição substitui o conteúdo.

### Tipos Básicos:

| Tipo | Utilização |
|---|---|
| `char` | Tipo inteiro utilizado frequentemente para caracteres. |
| `int` | Números inteiros. |
| `float` | Números em ponto flutuante. |
| `double` | Ponto flutuante, normalmente com maior precisão e alcance que `float`. |
| `bool` | Valores booleanos: `false` e `true`. |
| `void` | Ausência de valor em contextos como o retorno de uma função. |

```cpp
int pessoas{4};
float temperatura{26.5f};      // Sufixo f: literal float.
double distancia{1234.56789};  // Sem sufixo: literal double.
char letra{'A'};
```

Ponto flutuante possui precisão limitada: muitos decimais são aproximados, como uma divisão interrompida após certo número de casas. `char` ocupa um byte de C++, normalmente de oito bits. Tamanhos como quatro bytes para `int` e `float` e oito para `double` são comuns, mas dependem da implementação.

> Um caractere visual pode ocupar vários bytes em codificações como UTF-8. Um `char` isolado não comporta necessariamente uma letra acentuada ou um emoji.

### Inteiros com e sem Sinal:

`int` equivale a `signed int`; `unsigned int` representa valores não negativos. `short`, `long` e `long long` selecionam outras categorias de inteiros, e a palavra `int` pode ser omitida nessas combinações.

```cpp
int saldo{-20};
unsigned int quantidade{20};
short pequeno{100};
long populacao{1000000L};
long long contador{10000000000LL};
unsigned long capacidade{500000UL};
```

Tipos distintos podem ter o mesmo tamanho: `long` não garante mais espaço que `int` em qualquer plataforma. `char`, `signed char` e `unsigned char` são tipos diferentes (o comportamento de `char` quanto ao sinal depende da implementação).

### Valores Booleanos:

São utilizados para representar opções binárias (`true` e `false`). Na conversão numérica, **zero se torna falso** e **qualquer outro valor se torna verdadeiro**.

```cpp
bool ativo{true};
bool bloqueado{false};
bool possui_itens = 5;  // true: demonstra a conversão numérica.
// bool rejeitado{5};   // Inválido: estreitamento na inicialização com chaves.
std::cout << ativo << ' ' << bloqueado << '\n';  // 1 0.
```

`bool`, `true` e `false` fazem parte da linguagem e não exigem um cabeçalho. Por padrão, `std::cout` apresenta booleanos como `1` e `0`; a comparação e o armazenamento continuam utilizando `bool`.

### Literais e Caracteres Especiais:

Valores escritos diretamente incluem `10`, `3.5`, `'A'` e `"Texto"`. Aspas simples delimitam um literal de caractere; aspas duplas delimitam um literal de *string*.

```cpp
int decimal{25}, hexadecimal{0x19}, octal{031}, binario{0b11001};
int milhao{1'000'000};  // Apóstrofos apenas separam grupos de dígitos.
std::cout << "Nome:\tAna\nCaminho: pasta\\arquivo\nMensagem: \"Ola\"\n";
```

| Escape | Significado |
|---|---|
| `\n` | Nova linha. |
| `\t` | Tabulação horizontal. |
| `\\` | Barra invertida. |
| `\"` | Aspas duplas. |
| `\'` | Aspas simples. |
| `\0` | Caractere nulo, de valor zero. |

> `'0'` é o caractere usado para escrever o algarismo zero; `'\0'` é o caractere nulo. Seus valores são diferentes. Em C++, o literal simples `'A'` possui tipo `char`, diferentemente de C, em que possui tipo `int`.

### Declaração, Inicialização e Atribuição:

A declaração informa tipo e nome; a inicialização fornece o primeiro valor; uma atribuição posterior altera o objeto existente. A cópia de um valor não estabelece uma ligação permanente entre variáveis.

```cpp
int a;                  // Declaração sem inicialização.
a = 10;                 // Atribuição antes de qualquer leitura.
int b = 20, c = b;       // Inicialização com =.
b = 30;                 // c continua valendo 20.
int largura(10);        // Inicialização com parênteses.
int altura{5};          // Inicialização com chaves.
int total{};            // Para int, as chaves vazias inicializam com zero.
int area{largura * altura};
```

> Tanto `int quantidade = 2;`, forma também utilizada em C, quanto `int quantidade{2};` são inicializações válidas em C++. Nesta apostila, as chaves serão preferidas por rejeitarem conversões de **estreitamento**, como de `double` para `int`, reduzindo o risco de alterações indesejadas do valor. Elas não impedem todas as conversões implícitas nem mudam a tipagem estática da linguagem. Atribuições posteriores continuam utilizando `=`.

Uma variável local de tipo básico, como `int`, sem inicialização possui **valor indeterminado**; sua leitura pode causar comportamento indefinido. Inicializar no momento da declaração evita depender de uma atribuição posterior.

Chaves também rejeitam conversões classificadas como **estreitamento** (*narrowing*), como passar de ponto flutuante para inteiro:

```cpp
int truncado = 3.8;    // Permitido: armazena 3.
// int rejeitado{3.8}; // Inválido: conversão de double para int entre chaves.
double exato{3};       // Válido: o valor inteiro é representável como double.
int numero{3};
// double rejeitado{numero};  // Inválido: int variável pode exigir estreitamento.
double convertido{static_cast<double>(numero)};  // Conversão explícita.
```

> Objetos de duração estática recebem inicialização com zero antes de outras inicializações aplicáveis. Classes podem definir suas próprias regras de construção; por isso, chaves vazias não significam universalmente “zerar todos os bytes”.

### Dedução de Tipo com `auto`:

`auto` solicita que o compilador deduza o tipo a partir do inicializador. Não significa que a variável poderá trocar de tipo depois.

```cpp
auto quantidade = 3;    // int.
auto preco = 12.5;      // double.
auto letra = 'A';       // char.
auto ativo = true;     // bool.
quantidade = 4.9;       // Continua int: recebe 4 após a conversão.
// auto indefinido;     // Inválido: falta o inicializador para deduzir o tipo.
```

> Com `auto`, os exemplos mantêm `=`: o compilador deduz o tipo do inicializador, sem converter o valor para um tipo previamente escolhido.

O tipo explícito ajuda quando a escolha importa para a leitura ou para o armazenamento. `auto` é útil quando o inicializador já deixa o tipo claro ou quando escrevê-lo seria repetitivo.

> `auto texto = "Ola";` não cria uma `std::string`: deduz um ponteiro para caracteres constantes. Para criar o objeto de texto, utiliza-se `std::string texto{"Ola"};`, apresentado adiante.

### Constantes com `const` e `constexpr`:

`const` impede a alteração por uma expressão que trate o objeto como constante. O valor pode ser obtido durante a execução: uma leitura ou o resultado de uma função pode inicializar uma variável `const`.

```cpp
double preco{100.0};
const double taxa{0.15};
const double acrescimo{preco * taxa};  // Valor calculado na inicialização.
preco = 200.0;                         // acrescimo continua 15.0.
// taxa = 0.20;                        // Inválido.
```

Em uma variável, `constexpr` exige um inicializador que possa ser avaliado como expressão constante e também torna o objeto constante. É útil para valores utilizados em contextos que exigem constantes, como o tamanho de um *array* local comum.

```cpp
constexpr int linhas{3};
constexpr int colunas{4};
constexpr int capacidade{linhas * colunas};  // 12.
int quantidade{5};
const int copia{quantidade};                // Válido.
// constexpr int erro{quantidade};          // Inválido: quantidade não é constante.
```

> Um `const int` inicializado adequadamente também pode participar de expressões constantes. A diferença é que `const`, sozinho, não exige essa possibilidade. Funções `constexpr` serão retomadas no capítulo de compilação.

### Tamanho com `sizeof`:

`sizeof` informa o tamanho de um tipo ou objeto em bytes. Seu resultado possui tipo `std::size_t`, um tipo inteiro sem sinal disponibilizado por `<cstddef>`.

```cpp
int numero{10};
std::cout << "Tipo: " << sizeof(int) << "; objeto: " << sizeof numero << '\n';
```

> `sizeof` mede o armazenamento do próprio objeto. Em um objeto que administra dados externos, como `std::string`, isso não informa a quantidade de caracteres armazenados.

## Operadores e Expressões:

Uma **expressão** combina valores e operadores para produzir um resultado. Atribuições e incrementos também modificam o estado do programa.

### Aritmética e Conversões:

| Operador | Operação | Exemplo |
|---|---|---|
| `+` | Adição. | `7 + 2` -> `9`. |
| `-` | Subtração. | `7 - 2` -> `5`. |
| `*` | Multiplicação. | `7 * 2` -> `14`. |
| `/` | Divisão. | `7 / 2` -> `3`. |
| `%` | Resto inteiro. | `7 % 2` -> `1`. |

Se os dois operandos são inteiros, a divisão descarta a parte fracionária em direção a zero. O tipo do destino não altera retroativamente a operação. Conversões **implícitas** seguem as regras da linguagem; `static_cast<tipo>(expressao)` indica explicitamente uma conversão como as numéricas abaixo.

```cpp
int a{7 / 2}, b{-7 / 2};                // a = 3 e b = -3.
double c{7 / 2};                         // Divisão inteira, depois conversão: 3.0.
double d{7 / 2.0};                       // Divisão em ponto flutuante: 3.5.
double total{static_cast<double>(a)};     // Conversão explícita: 3.0.
int parte_inteira{static_cast<int>(8.9)}; // 8.
int soma{15}, elementos{2};
double media{static_cast<double>(soma) / elementos};  // 7.5.
```

Com chaves, converter um inteiro variável para `double` pode ser rejeitado como estreitamento, pois o tipo pode representar valores que não cabem exatamente no destino. No exemplo, `static_cast<double>(a)` explicita essa conversão; já `double c{7 / 2};` aceita o resultado constante e representável `3`.

Um *cast* não garante segurança: o destino pode ser incapaz de representar o valor. Misturar inteiros com e sem sinal também pode mudar a interpretação da comparação:

```cpp
int saldo{-1};
unsigned int limite{10};
bool resultado{saldo < limite};  // false: -1 é convertido para unsigned int.
```

Nesse exemplo, a conversão produz o maior valor de `unsigned int`, e não um número negativo. O compilador pode avisar sobre a mistura de sinais.

> Divisão inteira por zero, resto por zero e estouro aritmético de inteiros com sinal causam **comportamento indefinido**: a linguagem não exige um resultado ou reação específicos. Inteiros sem sinal seguem redução modular, o que também pode contrariar a lógica pretendida.

> A conversão no formato `(tipo) expressao`, herdada de C, também existe. Os *casts* nomeados de C++ tornam a intenção mais visível; `static_cast` não substitui todos os tipos de conversão.

### Atribuição e Atualização:

`=` atribui um valor; operadores compostos combinam cálculo e atualização. `++` e `--` acrescentam ou retiram uma unidade. A forma pós-fixada produz o valor anterior; a pré-fixada produz o atualizado.

```cpp
int saldo{100};
saldo += 20;  // Equivale, aqui, a saldo = saldo + 20.
saldo -= 10;
saldo *= 2;
saldo /= 5;
saldo %= 7;
int contador{5};
int anterior{contador++};  // anterior = 5; contador = 6.
int atual{++contador};     // atual = 7; contador = 7.
```

> `i++ + i++` modifica o mesmo objeto sem o sequenciamento necessário e causa comportamento indefinido. Atualizações separadas deixam a ordem explícita.

### Comparações e Operações Lógicas:

Comparações entre os tipos básicos produzem `bool`: `true` ou `false`. Em condições numéricas, zero é falso e qualquer outro valor é verdadeiro.

| Operadores | Relação ou operação |
|---|---|
| `==`, `!=` | Igualdade e diferença. |
| `<`, `<=`, `>`, `>=` | Menor, menor ou igual, maior, maior ou igual. |
| `&&` | Verdadeiro quando ambas as condições são verdadeiras. |
| `\|\|` | Verdadeiro quando pelo menos uma condição é verdadeira. |
| `!` | Inverte o resultado lógico. |

```cpp
int idade{20};
bool possui_documento{true};
bool entrada_permitida{idade >= 18 && possui_documento};  // true.
bool entrada_bloqueada{!entrada_permitida};               // false.
bool tem_dez_anos{idade == 10};                           // false.
bool alternativa{idade < 18 || !possui_documento};        // false.
int divisor{0};
bool resultado{divisor != 0 && 20 / divisor > 2};  // Não executa a divisão.
```

`&&` e `||`, aplicados a essas condições, utilizam **curto-circuito**: só avaliam a segunda expressão quando ela ainda é necessária. `=` faz atribuição, não comparação; sua troca por `==` pode alterar a lógica sem impedir a compilação.

> Intervalos exigem relações separadas: `valor >= 0 && valor <= 10`. `0 <= valor <= 10` não representa esse intervalo.

### Operações sobre Bits:

Cada bit pode ser visualizado como um interruptor. Os operadores atuam sobre as posições da representação de valores inteiros.

| Operador | Operação |
|---|---|
| `&` | AND: bits presentes em ambos. |
| `\|` | OR: bits presentes em pelo menos um. |
| `^` | XOR: bits diferentes. |
| `~` | Inversão de todos os bits do tipo utilizado. |
| `<<`, `>>` | Deslocamento para esquerda e direita. |

```cpp
unsigned int a{0b0110}, b{0b0011};  // 6 e 3.
unsigned int intersecao{a & b};      // 0010 -> 2.
unsigned int uniao{a | b};           // 0111 -> 7.
unsigned int diferentes{a ^ b};      // 0101 -> 5.
unsigned int dobro{a << 1};          // 1100 -> 12.
unsigned int metade{a >> 1};         // 0011 -> 3.
a |= 0b0001;                         // Liga o bit final: a = 7.
a &= ~0b0010u;                       // Desliga o segundo bit: a = 5.
```

> `&` e `|` não oferecem curto-circuito. Deslocamentos exigem quantidade não negativa e menor que a largura do operando promovido. Tipos sem sinal tornam essas operações mais previsíveis; `~` inverte também os bits omitidos na representação abreviada.

### Operador Condicional:

`condicao ? expressao_verdadeira : expressao_falsa` avalia somente a alternativa selecionada e produz um resultado utilizável em outras expressões.

```cpp
int a{10}, b{20};
int maior{a > b ? a : b};  // 20.
```

### Precedência e Agrupamento:

A precedência determina o agrupamento: `2 + 3 * 4` resulta em `14`; `(2 + 3) * 4`, em `20`. A tabela resume os operadores utilizados aqui, da maior para a menor precedência.

| Grupo | Operadores |
|---|---|
| Resolução de escopo | `::`. |
| Pós-fixados | Chamada `()`, índice `[]`, membros `.` e `->`, `x++`, `x--`. |
| Unários | `++x`, `--x`, `+`, `-`, `!`, `~`, `&`, `*`, `sizeof`. |
| Multiplicativos | `*`, `/`, `%`. |
| Aditivos | `+`, `-`. |
| Deslocamentos | `<<`, `>>`. |
| Relacionais | `<`, `<=`, `>`, `>=`. |
| Igualdade | `==`, `!=`. |
| AND bit a bit | `&`. |
| XOR bit a bit | `^`. |
| OR bit a bit | `\|`. |
| AND lógico | `&&`. |
| OR lógico | `\|\|`. |
| Condicional e atribuição | `?:`, `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `^=`, `\|=`, `<<=`, `>>=`. |
| Vírgula | `,`. |

A associatividade resolve operadores de mesma precedência: `a - b - c` corresponde a `(a - b) - c`; `a = b = 0`, a `a = (b = 0)`. Em `static_cast<tipo>(expressao)`, os parênteses já delimitam a expressão convertida.

> Agrupamento não determina, em geral, a ordem de avaliação. Em `f() + g()`, não há garantia de qual função é chamada primeiro. Com `std::cout`, uma comparação exige agrupamento: `std::cout << (a < b);`.

## Entrada e Saída Básica:

`<iostream>` oferece entrada e saída por fluxos. Em uma execução interativa comum, `std::cin` recebe dados do terminal e `std::cout` apresenta resultados, mas esses fluxos também podem ser redirecionados.

### Saída com `std::cout`:

`std::cout << valor` envia um valor para a saída. A operação pode ser encadeada, e a apresentação considera o tipo de cada argumento, sem exigir marcadores como `%d` e `%f`.

```cpp
int quantidade{3};
double preco{12.5};
char categoria{'A'};
std::cout << "Quantidade: " << quantidade << "; preco: " << preco
          << "; categoria: " << categoria << "; desconto: 10%\n";
// Quantidade: 3; preco: 12.5; categoria: A; desconto: 10%.
```

O mesmo símbolo pode representar operações diferentes conforme os operandos. Entre inteiros, `<<` desloca bits; com `std::cout`, insere dados no fluxo. A definição de operações para tipos próprios será apresentada com classes.

Para exibir uma quantidade fixa de casas decimais, `<iomanip>` oferece `std::setprecision`, combinado com `std::fixed`:

```cpp
#include <iostream>
#include <iomanip>

int main() {
    double preco{12.5};
    std::cout << std::fixed << std::setprecision(2) << preco << '\n';  // 12.50.
    return 0;
}
```

Essa configuração permanece nas próximas saídas do mesmo fluxo até ser alterada. Sem `std::fixed`, a precisão indica a quantidade de dígitos significativos, não apenas as casas decimais.

> `std::endl` acrescenta uma quebra de linha e solicita a descarga do fluxo (*flush*). Para apenas separar linhas, `\n` costuma ser suficiente, evitando solicitar essa descarga a cada saída.

### Entrada com `std::cin`:

`std::cin >> variavel` tenta ler e converter um valor para o destino. A extração já recebe acesso à variável; não se escreve `&` antes do nome como em chamadas usuais de `scanf`.

```cpp
#include <iostream>

int main() {
    int idade{};
    double altura{};
    char opcao{};
    std::cout << "Idade, altura e opcao:\n";
    if (!(std::cin >> idade >> altura >> opcao)) {
        std::cout << "Entrada invalida.\n";
        return 1;
    }
    std::cout << "Idade: " << idade << "; altura: " << altura
              << "; opcao: " << opcao << '\n';
    return 0;
}
```

A condição verifica o estado do fluxo após as leituras. O `if` impede o uso dos resultados quando alguma extração falha. Por padrão, essas leituras ignoram espaços em branco iniciais, inclusive ao ler um `char`.

Uma falha mantém o fluxo em estado de erro, impedindo novas extrações comuns até sua recuperação. Os exemplos encerram o programa nesse caso; limpar o estado e descartar uma entrada inválida são operações diferentes.

> Uma leitura numérica pode consumir apenas um prefixo: ao receber `12abc` para um `int`, pode obter `12` e deixar `abc` pendente. Verificar a extração não equivale a validar toda a linha. A leitura de texto com espaços aparecerá junto de `std::string`.

## Estruturas Condicionais:

### `if`, `else if` e `else`:

`if` executa a alternativa verdadeira; `else` atende ao caso falso. Em uma cadeia, o primeiro teste satisfeito seleciona seu bloco e os demais são ignorados. Um `if` simples pode omitir tanto `else if` quanto `else`.

```cpp
int nota{5};
if (nota >= 6) {
    std::cout << "Aprovado.\n";
} else if (nota >= 4) {
    std::cout << "Recuperacao.\n";
} else {
    std::cout << "Reprovado.\n";
}
```

> Sem chaves, apenas a instrução seguinte pertence ao ramo. As chaves tornam o agrupamento explícito. C++ também permite uma inicialização antes da condição: `if (int dobro = nota * 2; dobro >= 10) { ... }`; o nome permanece disponível nos ramos desse `if`.

### `switch` e `case`:

`switch` seleciona um ponto de entrada conforme um valor inteiro ou de enumeração. Os rótulos `case` utilizam expressões constantes; `default`, opcional, atende a valores sem correspondência. `break` encerra o `switch`.

```cpp
int opcao{2};
switch (opcao) {
    case 1:
        std::cout << "Cadastrar.\n";
        break;
    case 2:
        std::cout << "Consultar.\n";
        break;
    case 3:
    case 4:  // Duas opções compartilham a ação.
        std::cout << "Operacao administrativa.\n";
        break;
    default:
        std::cout << "Opcao desconhecida.\n";
        break;
}
```

Sem interrupção, a execução continua nas instruções seguintes (*fall-through*). Um rótulo `case` não cria um bloco próprio: para declarar e inicializar variáveis locais em uma alternativa, pode-se envolver seu conteúdo em chaves.

> `switch` não compara diretamente *strings* nem ponto flutuante. Quando a continuação de uma alternativa para outra é intencional, `[[fallthrough]];` pode documentá-la antes do próximo rótulo.

## Estruturas de Repetição:

### `while` e `do while`:

`while` testa antes do corpo e pode não executá-lo. `do while` testa depois, garantindo uma execução inicial. A condição é reavaliada a cada repetição.

```cpp
int contador{1};
while (contador <= 3) {
    std::cout << contador++ << ' ';
}
std::cout << '\n';  // 1 2 3.

contador = 5;
do {
    std::cout << contador++ << '\n';  // 5: executa mesmo com condição falsa.
} while (contador < 3);
```

> O `;` após a condição faz parte da sintaxe de `do while`.

### `for`:

`for (inicializacao; condicao; atualizacao)` executa a inicialização uma vez, testa antes do corpo e atualiza após cada repetição. Uma variável declarada no cabeçalho tem escopo restrito à estrutura.

```cpp
for (int i{0}; i < 4; i++) {
    std::cout << i << ' ';
}
std::cout << '\n';  // 0 1 2 3.

int i{};  // Também pode ser declarada antes do loop.
for (i = 10; i >= 0; i -= 5) {
    std::cout << i << ' ';
}
std::cout << '\n';  // 10 5 0.
```

As três partes podem ser omitidas. Sem condição, ela é tratada como verdadeira: `for (;;) { break; }` só termina pela transferência de controle explícita.

### `break` e `continue`:

`break` encerra o *loop* ou `switch` mais interno que o contém. `continue` pula o restante da repetição: no `for`, segue para a atualização e o teste; no `while` e `do while`, para o teste.

```cpp
for (int i{1}; i <= 10; i++) {
    if (i == 3) {
        continue;
    }
    if (i == 6) {
        break;
    }
    std::cout << i << ' ';
}
std::cout << '\n';  // 1 2 4 5.
```

> `goto` também permite saltar para um rótulo da mesma função, com restrições para não atravessar inicializações indevidamente. Para decisões e repetições comuns, as estruturas acima tornam o caminho da execução mais claro.

## Grupos de Dados:

### *Arrays*:

Um ***array*** reúne elementos do mesmo tipo em posições consecutivas, como compartimentos iguais numerados a partir de zero. Um *array* de quatro elementos possui índices de `0` a `3`.

```cpp
int notas[4]{8, 7, 9, 6};
int inferido[]{10, 20, 30};  // Tamanho deduzido: 3.
int parcial[5]{1, 2};        // {1, 2, 0, 0, 0}.
int zerado[5]{};               // Todos recebem zero.
int indefinido[5];             // Local comum: valores indeterminados.
notas[1] = 10;
std::cout << notas[0] << ' ' << notas[3] << '\n';  // 8 6.
// notas = {1, 2, 3, 4};       // Inválido: não admite atribuição integral.
```

Um inicializador parcial inicializa também as posições restantes (para inteiros, elas recebem zero). Depois da criação, os elementos podem ser modificados individualmente, mas o *array* não é reatribuído com `=`.

O tamanho de um *array* local nessa forma precisa ser uma expressão constante positiva. `constexpr int tamanho{4};` permite declarar `int valores[tamanho]{};`. Um tamanho lido durante a execução exige outra forma de armazenamento.

*Loops* percorrem as posições. A quantidade pode ser calculada pela divisão do tamanho total pelo tamanho de um elemento:

```cpp
#include <iostream>
#include <cstddef>

int main() {
    int notas[]{8, 7, 9, 6};
    std::size_t quantidade{sizeof notas / sizeof notas[0]};
    int soma{0};
    for (std::size_t i{0}; i < quantidade; i++) {
        soma += notas[i];
    }
    double media{static_cast<double>(soma) / quantidade};
    std::cout << "Quantidade: " << quantidade << "; media: " << media << '\n';
    return 0;  // Quantidade: 4; media: 7.5.
}
```

> Esse cálculo exige o próprio *array*, não um ponteiro nem um parâmetro ajustado para ponteiro. Acessos fora dos limites causam comportamento indefinido; os índices não são verificados automaticamente. *Arrays* de tamanho variável, aceitos em alguns compiladores como extensão, não fazem parte de C++20.

### `for` Baseado em Intervalo:

`for (tipo elemento : grupo)` percorre os elementos de um grupo compatível, como um *array*, sem controlar o índice manualmente.

```cpp
int notas[]{8, 7, 9, 6};
int soma{0};
for (int nota : notas) {
    soma += nota;
}
std::cout << soma << '\n';  // 30.
```

Nesse exemplo, `nota` recebe uma cópia de cada elemento. Alterar essa variável não modifica o *array*. A alteração direta será apresentada junto de referências.

### Matrizes:

Uma matriz pode ser representada como um *array* de *arrays*. Em `matriz[linha][coluna]`, o primeiro índice seleciona uma linha e o segundo um elemento dela. As linhas se sucedem de forma contígua na memória.

```cpp
int matriz[][3]{  // Primeira dimensão deduzida: 2; também caberia [2][3].
    {1, 2, 3},
    {4, 5, 6}
};
std::cout << matriz[1][2] << '\n';  // 6.
for (int linha{0}; linha < 2; linha++) {
    for (int coluna{0}; coluna < 3; coluna++) {
        std::cout << matriz[linha][coluna] << ' ';
    }
    std::cout << '\n';
}
```

### Texto com `std::string`:

Uma **`std::string`** é um objeto que armazena uma sequência de caracteres e administra o espaço necessário. `<string>` disponibiliza esse tipo, que permite atribuir, comparar e juntar textos diretamente.

```cpp
std::string nome{"Ana"};
std::string copia{nome};
copia[0] = 'I';                        // copia = "Ina"; nome continua "Ana".
std::string mensagem{"Ola, " + nome}; // "Ola, Ana".
nome += " Silva";                      // "Ana Silva".
bool igual{nome == "Ana Silva"};      // Compara o conteúdo: true.
std::cout << nome << ' ' << nome.size() << '\n';  // Ana Silva 9.
```

`texto.size()` consulta a quantidade de elementos `char`, e `texto.empty()` verifica se ela é zero. O ponto acessa uma operação do objeto. `texto[indice]` acessa um caractere existente, usando índices de `0` até `texto.size() - 1` para os caracteres do texto; uma *string* vazia não possui caracteres nessa faixa.

`std::cin >> nome` lê uma palavra, encerrando ao encontrar espaço em branco. `std::getline(std::cin, nome)` lê uma linha, consome a quebra de linha e não a armazena no texto:

```cpp
#include <iostream>
#include <string>

int main() {
    std::string nome{};
    std::cout << "Nome completo:\n";
    if (!std::getline(std::cin, nome)) {
        std::cout << "Falha na leitura.\n";
        return 1;
    }
    std::cout << "Ola, " << nome << "!\n";
    return 0;
}
```

> Após uma leitura com `>>`, a quebra de linha pode continuar pendente e fazer o próximo `getline` obter uma linha vazia. `std::getline(std::cin >> std::ws, nome)` descarta os espaços em branco iniciais antes de ler, mas também ignora linhas vazias e espaços que poderiam fazer parte do texto.

> `size()` conta unidades `char`, não necessariamente letras visíveis. Além disso, `"Ola" + "Ana"` não concatena dois literais; no exemplo anterior, a presença de uma `std::string` permite a operação com `+`.

### *Strings* como *Arrays* de Caracteres:

Também é possível representar texto como em C: um *array* de `char` terminado por `'\0'`. O terminador ocupa uma posição adicional e informa onde o texto termina para as operações que adotam essa convenção.

```cpp
char nome[]{"Ana"};  // {'A', 'n', 'a', '\0'}: quatro elementos.
nome[0] = 'I';
std::cout << nome << '\n';  // Ina.
// nome = "Bia";           // Inválido: o array não admite atribuição integral.
```

Nessa representação, `=` não copia integralmente o *array*, e `==` entre dois *arrays* não compara seu conteúdo. `std::string` será a escolha usual dos exemplos que precisam manipular texto.

> O literal `"Ana"` não pode ser modificado. No exemplo, seus caracteres inicializam um *array* próprio e modificável. A ausência do terminador em um *array* passado como texto a `std::cout` pode provocar leituras além de seus limites.

### Estruturas com `struct`:

Uma `struct` reúne membros de tipos diferentes, cada um com armazenamento próprio, como uma ficha com código, nome e preço. A definição descreve o formato; a declaração da variável cria o objeto; `.` seleciona um membro.

```cpp
struct Produto {
    int codigo{};
    std::string nome{};
    double preco{};
};

Produto produto{10, "Caderno", 15.50};
Produto vazio{};                          // codigo = 0; nome vazio; preco = 0.0.
Produto outro{.codigo = 11, .nome = "Lapis", .preco = 2.0};
Produto copia{produto};
copia.preco = 20.0;
std::cout << produto.nome << ' ' << produto.preco << '\n';  // Caderno 15.5.
```

Em C++, o nome `Produto` já identifica o tipo sem repetir `struct` nem criar um `typedef`. Membros podem ter inicializadores padrão, utilizados quando a inicialização do objeto não fornece outro valor para eles. Uma `std::string` inicializada sem texto começa vazia.

Nessas estruturas simples, a cópia e a atribuição copiam os membros, incluindo o conteúdo de `std::string`. Alterar o preço da cópia não modifica o preço do original.

> A inicialização com nomes de membros existe a partir de C++20 para agregados como este. Os nomes precisam seguir a ordem de declaração, embora seja possível omitir membros. Uma `struct` também pode ter funções-membro e outros recursos de classes, apresentados no próximo capítulo.

### Enumerações com `enum class`:

Enumerações definem um tipo com valores nomeados, como estados de um equipamento. `enum class` mantém os nomes dentro do escopo da enumeração e não os converte implicitamente para inteiros.

```cpp
enum class Estado { Desligado, Ligado, EmEspera };
Estado estado{Estado::Ligado};
if (estado == Estado::Ligado) {
    std::cout << "Equipamento em funcionamento.\n";
}
int codigo{static_cast<int>(estado)};  // 1.
// estado = 1;                          // Inválido: int não é Estado.
```

Sem valor explícito, o primeiro enumerador recebe zero e os seguintes recebem o anterior mais um. É possível escolher os valores: `enum class Codigo { Sucesso = 0, ErroLeitura = 10, ErroEscrita };`, em que `ErroEscrita` corresponde a `11`.

> O `enum` sem `class`, semelhante ao de C, também existe e possui regras de escopo e conversão menos restritivas. Uma enumeração não valida automaticamente dados externos: forçar uma conversão não garante que o resultado corresponda a um dos nomes declarados.

### Uniões com `union`:

Os membros de uma `union` compartilham armazenamento, como um compartimento que admite formatos diferentes de conteúdo, usados um de cada vez.

```cpp
union Valor {
    int inteiro;
    double decimal;
};

Valor valor{};
valor.inteiro = 10;
std::cout << valor.inteiro << '\n';  // 10.
valor.decimal = 3.5;
std::cout << valor.decimal << '\n';  // 3.5.
```

Seu tamanho comporta o maior membro e os requisitos de alinhamento, não a soma de espaços independentes. O programa precisa acompanhar qual membro está ativo; depois da escrita em `decimal`, não deve ler `inteiro` para tentar converter o valor.

> Em C++, ler um membro inativo geralmente causa comportamento indefinido, com exceções específicas. Membros como `std::string` também exigem cuidados extras de construção e destruição em uma `union`; o exemplo se limita a tipos básicos.

## Referências e Acesso Indireto:

Copiar um valor cria dados independentes. Referências e ponteiros permitem alcançar um objeto que já existe, sem criar uma cópia dele. Aqui será apresentado o necessário às funções e classes; validade, memória e alocação serão aprofundadas no capítulo correspondente.

### Referências:

Uma **referência** funciona como outro nome para um objeto. Na declaração `int& referencia{numero};`, `&` faz parte da declaração da referência; não solicita uma cópia de `numero`.

```cpp
int numero{10};
int copia{numero};
int& referencia{numero};
referencia = 20;  // Altera numero.
std::cout << numero << ' ' << copia << '\n';  // 20 10.

int outro{30};
referencia = outro;  // Atribui 30 a numero; não troca o objeto referenciado.
outro = 40;
std::cout << referencia << '\n';  // 30.
```

Uma referência local precisa ser inicializada e não pode ser redirecionada depois. As operações sobre ela atuam no objeto ao qual foi vinculada. Referências não possuem um estado nulo válido para representar “nenhum objeto”.

### Referências e `const`:

`const int&` permite consultar um inteiro sem alterá-lo por esse acesso. O objeto original pode continuar modificável por outro nome.

```cpp
int numero{10};
const int& leitura{numero};
numero = 20;
std::cout << leitura << '\n';  // 20: acompanha o mesmo objeto.
// leitura = 30;              // Inválido: acesso constante.
const int& valor{42};        // Também pode se vincular a um temporário.
// int& invalida{42};        // Inválido para referência não constante desse tipo.
```

> A referência não administra automaticamente a vida de um objeto existente. Se ele deixar de existir, o acesso por ela ficará inválido. Há regras específicas para temporários: na declaração local `const int& valor{42};`, o temporário permanece pelo tempo de vida dessa referência.

### Referências em Repetições:

No `for` baseado em intervalo, uma referência permite alterar os elementos diretamente. `auto&` deduz o tipo mantendo esse vínculo; `const auto&` permite consultá-los sem criar uma cópia de cada objeto e sem alterá-los por esse acesso.

```cpp
int notas[]{8, 7, 9, 6};
for (auto& nota : notas) {
    nota++;  // O array passa a conter {9, 8, 10, 7}.
}
for (const auto& nota : notas) {
    std::cout << nota << ' ';
}
std::cout << '\n';  // 9 8 10 7.
```

> `auto copia = referencia;` normalmente deduz o tipo do valor e cria uma cópia. Para manter uma referência nesse caso, escreve-se `auto& copia = referencia;`. Para valores pequenos, como `int`, a cópia em um *loop* de leitura costuma ser suficiente.

### Ponteiros:

Um **ponteiro** armazena um valor que permite localizar um objeto, como um endereço de entrega. `&numero` obtém seu endereço; `*ponteiro` acessa o objeto apontado, operação chamada **desreferenciação**.

```cpp
int numero{10};
int* ponteiro{&numero};
*ponteiro = 20;  // Altera numero.

int outro{30};
ponteiro = &outro;  // Agora aponta para outro.
*ponteiro = 40;
std::cout << numero << ' ' << outro << '\n';  // 20 40.

ponteiro = nullptr;  // Não aponta para um objeto.
if (ponteiro != nullptr) {
    std::cout << *ponteiro << '\n';  // Não executado.
}
```

Na declaração, `int*` indica um ponteiro para inteiro; na expressão, `*ponteiro` acessa o destino. Diferentemente da referência, o ponteiro pode ser redirecionado e assumir `nullptr`, o valor usado para indicar ausência de destino.

Copiar um ponteiro copia seu endereço, não o objeto apontado. Desreferenciar um ponteiro nulo ou inválido causa comportamento indefinido; testar `nullptr` não comprova que um objeto ainda existe.

### Acesso a Membros por Ponteiros:

Referências utilizam `.` como o objeto original. Para um ponteiro, `->` acessa o membro do objeto apontado: `ponteiro->membro` equivale a `(*ponteiro).membro`.

```cpp
struct Ponto { int x; int y; };
Ponto ponto{2, 3};
Ponto& referencia{ponto};
Ponto* ponteiro{&ponto};
referencia.x = 10;
ponteiro->y = 20;
std::cout << ponto.x << ' ' << ponto.y << '\n';  // 10 20.
```

## Funções:

Uma função reúne operações sob um nome e informa entradas e retorno, como uma ferramenta com interface definida. **Parâmetros** são as variáveis da definição; **argumentos** são os valores fornecidos na chamada.

### Retorno, Protótipos e Passagem por Valor:

O **protótipo** declara nome, retorno e tipos dos parâmetros antes do uso. A definição pode vir depois. Um parâmetro como `int valor` recebe seu próprio valor: alterá-lo não modifica a variável utilizada na chamada.

```cpp
#include <iostream>

int incrementar(int valor);  // Também poderia ser int incrementar(int);

int main() {
    int numero{10};
    int resultado{incrementar(numero)};
    std::cout << numero << ' ' << resultado << '\n';  // 10 11.
    return 0;
}

int incrementar(int valor) {
    valor++;
    return valor;
}
```

`return expressao;` encerra a função e produz seu resultado. Uma função `void` não retorna valor: pode terminar pelo final do corpo ou antecipadamente com `return;`.

```cpp
void mostrar_linha() {
    std::cout << "----------------\n";
}

int maior(int a, int b) {
    if (a > b) {
        return a;  // Encerra antecipadamente.
    }
    return b;
}
// Chamadas dentro de outra função: mostrar_linha(); e maior(3, 5).
```

> Em C++, `void funcao();` declara uma função sem parâmetros, assim como `void funcao(void);`. Isso difere de C17. Um protótipo fornece informações ao compilador, sem executar a função; uma função com retorno diferente de `void` precisa produzir seu resultado nos caminhos que terminam normalmente, com a exceção já apresentada de `main`.

### Passagem por Referência e por Ponteiro:

Um parâmetro `T&` permite operar sobre o objeto do chamador. `const T&` permite consultá-lo sem copiá-lo e sem alterá-lo por essa referência. É útil para objetos cuja cópia pode exigir mais trabalho, como textos e estruturas maiores.

```cpp
void incrementar(int& valor) {
    valor++;
}

void mostrar_nome(const std::string& nome) {
    std::cout << nome << '\n';
}
// Em outra função: int numero{10}; incrementar(numero); -> numero = 11.
// mostrar_nome("Ana"); também é válido: cria uma string temporária para a chamada.
```

Um parâmetro ponteiro também permite alcançar o objeto do chamador. O ponteiro é recebido por valor: alterar o destino é diferente de redirecionar a cópia local do ponteiro.

```cpp
void incrementar_se_existir(int* valor) {
    if (valor != nullptr) {
        (*valor)++;
    }
}
// Em outra função: int numero{10}; incrementar_se_existir(&numero); -> 11.
// incrementar_se_existir(nullptr); não realiza alteração.
```

| Parâmetro | Uso comum |
|---|---|
| `T valor` | Receber um valor próprio; escolha simples para tipos pequenos. |
| `T& valor` | Acessar e possivelmente alterar um objeto existente. |
| `const T& valor` | Consultar um objeto existente sem copiá-lo. |
| `T* valor` | Acessar um objeto por endereço; pode admitir ausência, se a função a tratar. |
| `const T* valor` | Consultar por endereço sem modificar o objeto por esse acesso. |

> Referência não significa automaticamente maior velocidade. Para um `int`, receber por valor normalmente é suficiente. Também não se deve retornar um ponteiro ou uma referência para uma variável local comum: ela deixa de existir ao sair da função. Retornar seu valor é permitido e produz um resultado utilizável pelo chamador.

### Sobrecarga de Funções:

**Sobrecarga** permite utilizar o mesmo nome para funções com diferentes listas de parâmetros. O compilador escolhe a alternativa aplicável conforme os argumentos da chamada.

```cpp
int somar(int a, int b) {
    return a + b;
}

double somar(double a, double b) {
    return a + b;
}
// Em outra função: somar(2, 3) -> 5; somar(2.5, 3.5) -> 6.0.
// somar(2, 3.5);  // Ambíguo entre essas duas alternativas.
```

É possível diferenciar funções pela quantidade ou pelos tipos dos parâmetros. Alterar apenas o retorno ou o nome dos parâmetros não cria uma sobrecarga. Se não houver uma melhor alternativa única para a chamada, a compilação falha.

### Argumentos Padrão:

Um argumento padrão fornece um valor quando a chamada omite aquele argumento. Nesse uso, os parâmetros com valores padrão ficam à direita dos obrigatórios; não é possível pular um argumento do meio da chamada.

```cpp
double aplicar_desconto(double preco, double taxa = 0.10) {
    return preco * (1.0 - taxa);
}
// Em outra função: aplicar_desconto(100.0) -> 90.0.
// aplicar_desconto(100.0, 0.20) -> 80.0.
```

Quando há um protótipo separado, o valor padrão costuma ficar nele, visível no ponto da chamada. A definição não deve repetir esse valor. Combinar padrões e sobrecargas exige cuidado para não tornar chamadas ambíguas.

### Recursão:

Uma função recursiva chama a si mesma, diretamente ou por outras funções, reduzindo o problema até uma condição de encerramento.

```cpp
unsigned int fatorial(unsigned int n) {
    if (n <= 1) {
        return 1;
    }
    return n * fatorial(n - 1);
}
// Em outra função: std::cout << fatorial(5) << '\n'; -> 120.
```

`fatorial(5)` depende de `fatorial(4)` e assim sucessivamente (os resultados são combinados no retorno). Cada chamada possui seus próprios parâmetros e variáveis locais. Profundidade excessiva pode esgotar recursos, e resultados grandes podem ultrapassar a faixa do tipo: o exemplo atende a valores pequenos.

## Escopos e Organização de Nomes:

### Escopo e Sombreamento:

O **escopo** determina onde um nome pode ser usado. Nomes locais ficam disponíveis a partir de sua declaração até o fim do bloco e em seus blocos internos, respeitando eventuais nomes que os ocultem. O escopo mais externo é chamado de **global**.

```cpp
#include <iostream>
int total{100};

int main() {
    int quantidade{5};
    {
        int quantidade{2};  // Sombreamento do nome externo.
        std::cout << quantidade << '\n';  // 2.
    }
    std::cout << quantidade << ' ' << total << '\n';  // 5 100.
    return 0;
}
```

O **sombreamento** (*shadowing*) faz o nome identificar a declaração mais interna, sem eliminar o objeto externo. Escopo indica onde o nome é utilizável; tempo de vida indica por quanto tempo o objeto existe.

### Espaços de Nomes com `namespace`:

Um **espaço de nomes** agrupa declarações sob um nome, como pastas que permitem organizar arquivos com nomes iguais. `::` seleciona um nome dentro desse grupo.

```cpp
#include <iostream>

namespace medidas {
    constexpr double pi{3.141592653589793};

    double area_circulo(double raio) {
        return pi * raio * raio;
    }
}

int main() {
    std::cout << medidas::area_circulo(2.0) << '\n';  // Aproximadamente 12.5664.
    return 0;
}
```

Uma declaração `using medidas::area_circulo;` permite usar apenas `area_circulo` no escopo em que foi introduzida. Também é possível abreviar o grupo com `namespace med = medidas;` e escrever `med::area_circulo(2.0)`.

> `using namespace std;` permite procurar nomes de todo esse espaço sem escrever `std::`, mas pode gerar conflitos e tornar sua origem menos clara. Os exemplos mantêm o prefixo explícito. `::nome`, sem outro nome à esquerda, procura no escopo global.

### Apelidos de Tipos com `using`:

`using Nome = Tipo;` cria um nome alternativo para um tipo, sem criar um tipo diferente nem modificar seu armazenamento. Por exemplo, `using Contador = unsigned long;` permite declarar `Contador acessos{0};`.

O `typedef` herdado de C continua disponível: `typedef unsigned long Contador;` produz o mesmo apelido. O `using` de um apelido de tipo é diferente de `using std::cout;`, que disponibiliza um nome existente no escopo.

## Exemplo Integrado:

O exemplo reúne uma estrutura, um *array*, funções e referências. A função `aplicar_bonus` altera cada aluno; `mostrar` consulta o objeto sem copiá-lo.

```cpp
#include <iostream>
#include <string>

struct Aluno {
    std::string nome{};
    double nota{};
};

void aplicar_bonus(Aluno& aluno, double bonus = 0.5) {
    aluno.nota += bonus;
    if (aluno.nota > 10.0) {
        aluno.nota = 10.0;
    }
}

void mostrar(const Aluno& aluno) {
    std::cout << aluno.nome << ": " << aluno.nota << '\n';
}

int main() {
    Aluno turma[]{{"Ana", 8.0}, {"Bruno", 6.5}, {"Carla", 9.0}};
    for (auto& aluno : turma) {
        aplicar_bonus(aluno);
        mostrar(aluno);
    }
    return 0;
}
// Saída em três linhas: Ana: 8.5; Bruno: 7; Carla: 9.5.
```

O exemplo pressupõe notas inicialmente entre zero e dez e bônus não negativos. No próximo capítulo, classes permitirão reunir dados e operações e controlar as alterações que mantêm um objeto válido.

---

# Fontes:

- STANDARD C++ FOUNDATION. *Learning C++ if you already know C*. [S. l.], [s. d.]. Disponível em: [https://isocpp.org/wiki/faq/c](https://isocpp.org/wiki/faq/c). Acesso em: 5 out. 2026.

- STROUSTRUP, Bjarne; SUTTER, Herb (ed.). *C++ Core Guidelines*. [S. l.], 2026. Disponível em: [https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines). Acesso em: 5 out. 2026.

- FREE SOFTWARE FOUNDATION. *Compiling C++ Programs*. In: *Using the GNU Compiler Collection (GCC)*. [S. l.], [s. d.]. Disponível em: [https://gcc.gnu.org/onlinedocs/gcc/Invoking-G_002b_002b.html](https://gcc.gnu.org/onlinedocs/gcc/Invoking-G_002b_002b.html). Acesso em: 5 out. 2026.

- LEARNCPP. *Lvalue references*. [S. l.], 2024. Disponível em: [https://www.learncpp.com/cpp-tutorial/lvalue-references/](https://www.learncpp.com/cpp-tutorial/lvalue-references/). Acesso em: 5 out. 2026.

- LEARNCPP. *Constexpr variables*. [S. l.], [s. d.]. Disponível em: [https://www.learncpp.com/cpp-tutorial/constexpr-variables/](https://www.learncpp.com/cpp-tutorial/constexpr-variables/). Acesso em: 5 out. 2026.

- LEARNCPP. *Struct aggregate initialization*. [S. l.], [s. d.]. Disponível em: [https://www.learncpp.com/cpp-tutorial/struct-aggregate-initialization/](https://www.learncpp.com/cpp-tutorial/struct-aggregate-initialization/). Acesso em: 5 out. 2026.

- LEARNCPP. *Default arguments*. [S. l.], 2024. Disponível em: [https://www.learncpp.com/cpp-tutorial/default-arguments/](https://www.learncpp.com/cpp-tutorial/default-arguments/). Acesso em: 5 out. 2026.
