```
 _____                     _       
|  _  |                   | |      
| | | | _ __   __ _   ___ | |  ___ 
| | | || '__| / _` | / __|| | / _ \   
\ \_/ /| |   | (_| || (__ | ||  __/
 \___/ |_|    \__,_| \___||_| \___|
 _____  _____  _     
/  ___||  _  || |    
\ `--. | | | || |    
 `--. \| | | || |    
/\__/ /\ \/' /| |____
\____/  \_/\_\\_____/
```

# 0. Conceitos Básicos

> Esta apostila aborda SQL no *Oracle Database*. Os exemplos administrativos utilizam `FREEPDB1` como nome de serviço (esse nome depende da instalação).

## Banco de Dados e SQL:

O **banco de dados** reúne informações organizadas; o **Sistema de Gerenciamento de Banco de Dados (SGBD)** armazena, consulta e controla o acesso a essas informações. O *Oracle Database* é um SGBD.

Em um banco relacional, **tabelas** organizam registros em **linhas**, com suas informações distribuídas em **colunas**. Uma tabela de funcionários pode ter matrícula, nome e departamento.

**SQL** permite definir estruturas, consultar e modificar dados. É declarativa: informa o resultado desejado, como “funcionários do departamento 10”, sem descrever como percorrer os registros. **PL/SQL** acrescenta variáveis, condições e blocos de programação, utilizados aqui nos gatilhos (*triggers*).

## Arquitetura *Three-Schema*:

A arquitetura separa o que cada aplicação enxerga, a organização geral dos dados e seu armazenamento.

| Nível                                   | Finalidade                                          | Exemplo                                                 |
| --------------------------------------- | --------------------------------------------------- | ------------------------------------------------------- |
| **VDL (*View Definition Language*)**    | Externo. Visão dos dados para cada público.         | RH consulta salários; o diretório interno mostra nomes. |
| **DDL (*Data Definition Language*)**    | Conceitual. Estruturas, relações e regras do banco. | Funcionários vinculados a departamentos.                |
| **SDL (*Storage Definition Language*)** | Interno. Armazenamento e estruturas de acesso.      | Arquivos e índices.                                     |

No Oracle, comandos de definição podem atender a mais de uma dessas necessidades.

- **Independência física:** permite mudar o armazenamento sem alterar a estrutura conceitual.
- **Independência lógica:** busca preservar as interfaces externas diante de mudanças na estrutura conceitual.

## Convenções e Exemplos:

Palavras-chave são escritas em maiúsculas por convenção. Nomes sem aspas duplas não diferenciam maiúsculas de minúsculas (a linguagem não é *case-sensitive*); textos usam aspas simples (`'Carlos'`) e identificadores delimitados usam aspas duplas (`"Nome Completo"`).

- `-- {comentário}` - Comenta até o final da linha.

- `/* {comentário} */` - Delimita um comentário de uma ou mais linhas.

- `;` - Encerra comandos SQL nos clientes utilizados aqui.

- `/` - Em uma linha isolada, executa o bloco PL/SQL no modo de *script* do *SQL\*Plus*.

---

# 1. Estrutura das Tabelas

## Tipos de Dados:

| Tipo | Utilização |
|---|---|
| `NUMBER(p,s)` | Número com precisão `p` e escala `s`; por exemplo, `NUMBER(8,2)` admite seis dígitos inteiros e dois decimais. |
| `NUMBER(p)` | Equivale a `NUMBER(p,0)`, utilizado para inteiros de até `p` dígitos. |
| `VARCHAR2(n)` | Texto de tamanho variável, até o limite indicado. |
| `CHAR(n)` | Texto de tamanho fixo, completado com espaços. |
| `DATE` | Data e horário, até segundos. |
| `TIMESTAMP` | Data e horário, incluindo frações de segundo. |

Em textos, `VARCHAR2(30 CHAR)` limita caracteres e `VARCHAR2(30 BYTE)` limita *bytes* (no padrão UTF-8, caracteres acentuados, por exemplo, ocupam dois *bytes*). 

> Números não preservam zeros à esquerda: para manter `001` como identificador, deve-se utilizar texto.

## Comandos DDL:

**DDL (*Data Definition Language*)** reúne comandos de definição de estruturas.

- `CREATE TABLE {tabela} ({coluna} {tipo}, ...)` - Cria uma tabela com as colunas indicadas.

- `ALTER TABLE {tabela} ADD {coluna} {tipo}` - Acrescenta uma coluna.

- `ALTER TABLE {tabela} MODIFY {coluna} {definição}` - Altera tipo, tamanho, obrigatoriedade ou valor padrão de uma coluna.

- `ALTER TABLE {tabela} RENAME COLUMN {antiga} TO {nova}` - Renomeia uma coluna.

- `ALTER TABLE {tabela} DROP COLUMN {coluna}` - Remove uma coluna.

- `ALTER TABLE {tabela} RENAME TO {novo_nome}` - Renomeia a tabela.

- `DROP TABLE {tabela}` - Remove a tabela e seus dados.

> Alterações de tipos e restrições precisam ser compatíveis com os dados existentes. Por exemplo, definir `NOT NULL` exige que a coluna não contenha nulos.

## Valores Padrão e Restrições:

**`NULL` representa ausência de valor**, não zero nem a palavra `'NULL'`. No Oracle, o texto vazio (`''`) também é tratado como nulo.

- `DEFAULT {valor}` - Define um valor padrão para a coluna. Ele é usado quando ela é omitida de uma inserção ou quando a atribuição utiliza `DEFAULT`.

Alterar o padrão não substitui os dados existentes. Na declaração comum de `DEFAULT`, informar `NULL` explicitamente não aplica o padrão.

As **restrições** (*constraints*) definem os valores e relacionamentos aceitos pela tabela.

| Restrição     | Regra                                                                             |
| ------------- | --------------------------------------------------------------------------------- |
| `PRIMARY KEY` | Identificador único e não nulo da linha.                                          |
| `FOREIGN KEY` | Referência a uma chave primária ou única.                                         |
| `UNIQUE`      | Impede valores repetidos; uma coluna com essa restrição pode conter vários nulos. |
| `NOT NULL`    | Exige preenchimento.                                                              |
| `CHECK`       | Rejeita valores que tornem a condição falsa.                                      |

Uma **chave composta** utiliza a combinação das colunas: `PRIMARY KEY (a, b)` aceita `(1,2)` e `(1,3)`, mas não duas ocorrências de `(1,2)`. Nenhuma de suas colunas pode ser nula.

`CHECK (sexo IN ('M','F'))` aceita `'M'`, `'F'` ou `NULL`. Para obrigar o preenchimento, combine com `NOT NULL`. Uma chave estrangeira de uma coluna também admite `NULL` quando não houver essa exigência.

### Declaração e Alteração:

- `{coluna} {tipo} CONSTRAINT {nome_constraint} {restrição}` - Declara uma restrição junto da coluna.

- `CONSTRAINT {nome} PRIMARY KEY ({colunas})` - Declara uma chave primária no corpo da tabela; permite chaves compostas.

- `CONSTRAINT {nome} FOREIGN KEY ({colunas}) REFERENCES {tabela} ({colunas})` - Declara uma chave estrangeira no corpo da tabela.

- `ALTER TABLE {tabela} ADD CONSTRAINT {nome} {restrição}` - Acrescenta uma restrição.

- `ALTER TABLE {tabela} DROP CONSTRAINT {nome}` - Remove a restrição, preservando os dados.

- `ALTER TABLE {tabela} MODIFY {coluna} NOT NULL` - Torna a coluna obrigatória; `NULL` volta a permitir ausência.

### Dependências e Exclusão:

| Opção da chave estrangeira | Ao excluir uma linha pai com dependentes                       |
| -------------------------- | -------------------------------------------------------------- |
| Sem `ON DELETE`            | Rejeita a exclusão.                                            |
| `ON DELETE CASCADE`        | Exclui as linhas filhas correspondentes.                       |
| `ON DELETE SET NULL`       | Define a referência como nula, se as demais regras permitirem. |

- `DROP TABLE {tabela} CASCADE CONSTRAINTS` - Remove a tabela e as restrições referenciais que dependem de suas chaves; preserva as tabelas filhas.

- `ALTER TABLE {tabela} DROP COLUMN {coluna} CASCADE CONSTRAINTS` - Remove a coluna e as restrições dependentes.

```sql
-- Criação das tabelas.
CREATE TABLE Departamento_Exemplo (
    id   NUMBER(3) CONSTRAINT pk_dept_exemplo PRIMARY KEY,
    nome VARCHAR2(30 CHAR) NOT NULL
);

CREATE TABLE Empregado_Exemplo (
    mat     NUMBER(5) PRIMARY KEY,
    nome    VARCHAR2(40 CHAR) NOT NULL,
    salario NUMBER(8,2) DEFAULT 2000,
    dept_id NUMBER(3),
    CONSTRAINT ck_emp_salario CHECK (salario >= 0)
);

-- Restrição acrescentada após a criação.
ALTER TABLE Empregado_Exemplo
ADD CONSTRAINT fk_emp_exemplo FOREIGN KEY (dept_id)
REFERENCES Departamento_Exemplo(id) ON DELETE CASCADE;

-- Alterações de coluna e de valor padrão.
ALTER TABLE Empregado_Exemplo ADD email VARCHAR2(60 CHAR);
ALTER TABLE Empregado_Exemplo ADD CONSTRAINT uk_emp_email UNIQUE (email);
ALTER TABLE Empregado_Exemplo MODIFY nome VARCHAR2(80 CHAR);
ALTER TABLE Empregado_Exemplo MODIFY salario DEFAULT 2500;
ALTER TABLE Empregado_Exemplo RENAME COLUMN nome TO nome_completo;
ALTER TABLE Empregado_Exemplo DROP CONSTRAINT uk_emp_email;
ALTER TABLE Empregado_Exemplo DROP COLUMN email;

-- Renomeia a tabela pai e depois remove sua chave e as dependências.
ALTER TABLE Departamento_Exemplo RENAME TO Departamento_Aux;
ALTER TABLE Departamento_Aux DROP COLUMN id CASCADE CONSTRAINTS;

-- Limpeza do cenário auxiliar.
DROP TABLE Empregado_Exemplo;
DROP TABLE Departamento_Aux;
```

---

# 2. Consultas Básicas

> As aplicações e os exemplos de comandos SQL a partir daqui foram montados com base nas estruturas definidas no apêndice, permitindo testar os comandos de maneira interativa.

## Seleção e Filtros:

- `SELECT {expressões} FROM {tabela}` - Consulta colunas ou expressões; `*` seleciona todas as colunas.

Podem ser adicionados sufixos ao `SELECT` para formatar os dados e controlar a forma como são enviados:

- `AS {apelido}` - Define o nome da coluna no resultado.

- `WHERE {condição}` - Mantém apenas as linhas cuja condição seja verdadeira.

- `DISTINCT {expressões}` - Elimina repetições da combinação selecionada.

- `ORDER BY {coluna} {ASC|DESC}, ...` - Ordena o resultado; `ASC` é crescente e padrão (implícito), `DESC` é decrescente.

- `NULLS FIRST` / `NULLS LAST` - Define a posição dos nulos em um critério de ordenação.

- `FETCH FIRST {n} ROWS ONLY` - Limita a quantidade de linhas retornadas.

| Operador | Utilização |
|---|---|
| `=`, `<>` ou `!=` | Igual ou diferente. |
| `<`, `>`, `<=`, `>=` | Comparações de ordem. |
| `AND`, `OR`, `NOT` | Combinação ou negação de condições. |
| `BETWEEN a AND b` | Intervalo incluindo os extremos. |
| `IN (...)` | Correspondência com algum valor da lista. |
| `LIKE {padrão}` | Comparação textual: `%` representa zero ou mais caracteres; `_`, um caractere. |
| `IS NULL` / `IS NOT NULL` | Ausência ou presença de valor. |

Comparações como `coluna = NULL` são desconhecidas, não verdadeiras. Da mesma forma, `coluna <> 10` não inclui as linhas nulas. Para incluí-las, utilize `coluna <> 10 OR coluna IS NULL`.

> `AND` tem precedência sobre `OR`; parênteses deixam o agrupamento explícito. Sem `ORDER BY`, não há garantia de ordem das linhas.


```sql
-- Filtra departamentos e faixa salarial; desempata pela matrícula.
SELECT 
	mat, 
	primeiro AS nome, 
	salario
FROM Funcionario
WHERE 
	dept_id IN (10, 20) AND salario BETWEEN 4000 AND 6000
ORDER BY salario DESC, mat;
-- Patricia: 5600; Rafael: 5200.50; Daniela: 4100.

-- Cada combinação aparece uma única vez.
SELECT DISTINCT sexo, dept_id FROM Funcionario ORDER BY sexo, dept_id; -- A indentação e a divisão do comando em linhas não interferem em sua execução.

-- Texto e ausência de vínculo.
SELECT 
	mat, 
	primeiro 
FROM Funcionario 
WHERE primeiro LIKE 'Ca%';

SELECT 
	mat, 
	primeiro 
FROM Funcionario 
WHERE dept_id IS NULL; -- Bruno.

-- Três maiores salários.
SELECT 
	primeiro, 
	salario 
FROM Funcionario
ORDER BY salario DESC, mat FETCH FIRST 3 ROWS ONLY;
-- André: 7200; Marcos: 6800; Patricia: 5600.
```

## Expressões:

- `+`, `-`, `*`, `/` - Realizam operações aritméticas.

- `||` - Concatena textos.

- `SELECT {expressão} FROM DUAL` - Avalia uma expressão sem depender de uma tabela da aplicação.

```sql
SELECT 
	primeiro || ' ' || ultimo AS nome_completo, 
	salario * 1.10 AS salario_simulado
FROM Funcionario;

SELECT 2 + 3 AS resultado FROM DUAL; -- 5.
```

O salário simulado aparece apenas na consulta. Para gravar o novo valor, utiliza-se um comando de alteração.

---

# 3. Manipulação e Transações

## Comandos DML:

**DML (*Data Modification Language*)** reúne comandos de modificação de dados:

- `INSERT INTO {tabela} ({colunas}) VALUES ({valores})` - Insere uma linha nas colunas indicadas.

- `INSERT INTO {tabela} VALUES ({valores})` - Insere seguindo a ordem das colunas da tabela.

- `UPDATE {tabela} SET {coluna} = {valor}, ... WHERE {condição}` - Atualiza as linhas selecionadas.

- `DELETE FROM {tabela} WHERE {condição}` - Exclui as linhas selecionadas, preservando a tabela.

Colunas omitidas na inserção recebem seu padrão ou `NULL`, desde que isso respeite as restrições. Sem `WHERE`, os comandos `UPDATE` e `DELETE` afetam todas as linhas.

## Controle de Transações:

Uma **transação** reúne alterações pendentes. A própria sessão já enxerga o que modificou; outras sessões não leem essas alterações antes da confirmação.

- `COMMIT` - Confirma as alterações da transação e a encerra.
- `ROLLBACK` - Desfaz as alterações pendentes da transação, inclusive em várias tabelas.
- `SAVEPOINT {nome}` - Marca uma posição dentro da transação.
- `ROLLBACK TO {nome}` - Desfaz as alterações posteriores à marca, mantendo a transação aberta.

> DDL como `CREATE`, `ALTER` e `DROP` provoca confirmação implícita antes da execução de um comando sintaticamente válido e depois de sua conclusão (não dá para desfazer com `ROLLBACK`).

```sql
INSERT INTO Funcionario (mat, primeiro, ultimo, salario, dept_id)
VALUES (90, 'Manuel', 'Souza', 3000, 10);

UPDATE Funcionario SET salario = salario + 500 WHERE mat = 90;
SAVEPOINT reajuste;

DELETE FROM Funcionario WHERE mat = 90;
SELECT mat FROM Funcionario WHERE mat = 90; -- Nenhuma linha.

ROLLBACK TO reajuste;
SELECT mat, salario FROM Funcionario WHERE mat = 90; -- 90, 3500.

ROLLBACK; -- Desfaz também a inserção; preserva a base de estudos.
```

Utilizar `COMMIT` após o reajuste o confirmaria. Um `ROLLBACK` posterior não desfaz alterações já confirmadas.

### Exclusão em Cascata:

```sql
CREATE TABLE Historico (
    mat_funcionario NUMBER(5)
        REFERENCES Funcionario(mat) ON DELETE CASCADE,
    data_entrada DATE DEFAULT SYSDATE
);

INSERT INTO Funcionario (mat, primeiro, ultimo, salario)
VALUES (90, 'Teste', 'Historico', 3000);
INSERT INTO Historico (mat_funcionario) VALUES (90);

DELETE FROM Funcionario WHERE mat = 90;
SELECT * FROM Historico; -- O registro dependente também foi excluído.

ROLLBACK;
DROP TABLE Historico;
```

## Formas de Remoção:

| Comando | Efeito | `ROLLBACK` antes da confirmação |
|---|---|---|
| `DELETE FROM {tabela}` | Exclui linhas; aceita `WHERE`. | Pode desfazer. |
| `TRUNCATE TABLE {tabela}` | Esvazia uma tabela comum por DDL; não aceita filtro. | Não desfaz. |
| `DROP TABLE {tabela}` | Remove a tabela. | Não desfaz. |

## Geração de Identificadores:

Uma **sequência** (*sequence*) gera números que podem identificar registros. Os números utilizados não são devolvidos por `ROLLBACK`, portanto podem existir lacunas.

- `CREATE SEQUENCE {nome} START WITH {início} INCREMENT BY {passo}` - Cria a sequência.

- `{sequência}.NEXTVAL` - Obtém o próximo número.

- `{sequência}.CURRVAL` - Obtém o último número gerado pela sequência na própria sessão, após um `NEXTVAL`.

- `DROP SEQUENCE {nome}` - Remove a sequência.

```sql
CREATE SEQUENCE seq_Exemplo START WITH 90 INCREMENT BY 1;

INSERT INTO Funcionario (mat, primeiro, ultimo, salario)
VALUES (seq_Exemplo.NEXTVAL, 'Teste', 'Sequencia', 3000);

SELECT seq_Exemplo.CURRVAL AS ultimo_id FROM DUAL; -- 90.
SELECT mat, primeiro FROM Funcionario WHERE mat = 90;
ROLLBACK;

DROP SEQUENCE seq_Exemplo;
```

---

# 4. Funções e Expressões

As funções de linha única calculam um resultado para cada linha. Podem receber valores literais, colunas ou resultados de outras funções, como em `ROUND(ABS(variacao), 2)`.

## Funções Numéricas:

- `ABS({n})` - Valor absoluto: `ABS(-5)` resulta em `5`.

- `CEIL({n})` - Menor inteiro maior ou igual ao valor.

- `FLOOR({n})` - Maior inteiro menor ou igual ao valor.

- `MOD({a}, {b})` - Resto da divisão de `a` por `b`.

- `POWER({a}, {b})` - Potência de base `a` e expoente `b`.

- `ROUND({n}, {casas})` - Arredonda para a quantidade de casas decimais indicada.

- `TRUNC({n}, {casas})` - Retira as casas excedentes sem arredondar.

- `SIGN({n})` - Retorna `-1`, `0` ou `1`, conforme o sinal do número.

- `SQRT({n})` - Raiz quadrada de um número não negativo.

- `GREATEST({a}, {b}, ...)` / `LEAST({a}, {b}, ...)` - Maior ou menor valor entre os argumentos da mesma linha.

> `FLOOR(-3.2)` resulta em `-4`; `TRUNC(-3.2)` resulta em `-3`. Em `GREATEST` e `LEAST`, um argumento nulo produz resultado nulo.

```sql
SELECT nome, preco, ROUND(preco, 2) AS arredondado,
       TRUNC(preco, 2) AS truncado, CEIL(preco) AS teto
FROM Produto WHERE prod_id = 3;
-- Teclado Mecânico: 249.9950; 250.00; 249.99; 250.

SELECT nome, estoque, MOD(estoque, 2) AS resto
FROM Produto WHERE MOD(estoque, 2) <> 0; -- Estoques ímpares.

UPDATE Produto SET variacao = ABS(variacao) WHERE variacao < 0;
ROLLBACK; -- A função também pode ser utilizada em alterações.
```

## Funções de Texto:

- `CONCAT({a}, {b})` - Concatena dois textos; para várias partes, também pode ser utilizado `||`.

- `LOWER({texto})` / `UPPER({texto})` - Converte para minúsculas ou maiúsculas.

- `INITCAP({texto})` - Converte a inicial de cada palavra para maiúscula e as demais letras para minúsculas.

- `LPAD({texto}, {tamanho}, {preenchimento})` / `RPAD(...)` - Completa à esquerda ou à direita até o tamanho final indicado.

- `LTRIM({texto} [, {caracteres}])` / `RTRIM(...)` - Remove caracteres das bordas esquerda ou direita; por padrão, espaços.

- `TRIM({texto})` - Remove espaços das duas bordas.

- `REPLACE({texto}, {busca}, {substituto})` - Substitui ocorrências de uma sequência de caracteres.

- `TRANSLATE({texto}, {origem}, {destino})` - Substitui caractere a caractere, conforme a posição nos argumentos.

- `SUBSTR({texto}, {início} [, {quantidade}])` - Extrai um trecho; posições negativas contam a partir do final.

- `INSTR({texto}, {busca})` - Retorna a posição da primeira ocorrência ou `0` se não encontrar.

- `LENGTH({texto})` - Quantidade de caracteres.

- `CHR({código})` / `ASCII({texto})` - Caractere de um código ou representação numérica do primeiro caractere, conforme a codificação do banco.

> As posições de texto começam em `1`. Em `LTRIM` e `RTRIM`, o argumento opcional representa um conjunto de caracteres removíveis, não uma palavra inteira.

```sql
SELECT primeiro || ' ' || ultimo AS nome,
       INITCAP(primeiro) AS nome_padronizado,
       SUBSTR(ultimo, 1, 3) AS inicio_sobrenome,
       LENGTH(primeiro) AS tamanho
FROM Funcionario
WHERE UPPER(primeiro) LIKE 'RA%'; -- Rafael e raquel.

SELECT REPLACE('Oracle SQL', ' ', '_') AS substituicao,
       TRANSLATE('banana', 'aeiou', '*****') AS vogais,
       LPAD('12', 5, '0') AS codigo
FROM DUAL; -- Oracle_SQL; b*n*n*; 00012.
```

## Datas e Conversões:

- `SYSDATE` - Data e horário atuais do servidor.

- `DATE 'AAAA-MM-DD'` - Literal de data em formato fixo, à meia-noite.

- `ADD_MONTHS({data}, {n})` - Acrescenta ou retira meses, ajustando o dia quando necessário.

- `LAST_DAY({data})` - Último dia do mês.

- `MONTHS_BETWEEN({a}, {b})` - Diferença em meses, que pode ser fracionária.

- `NEXT_DAY({data}, {dia_da_semana})` - Próxima ocorrência do dia da semana, posterior à data; o nome depende de `NLS_DATE_LANGUAGE`.

- `TO_CHAR({valor}, {formato} [, {parâmetros}])` - Converte e formata um valor como texto.

- `TO_DATE({texto}, {formato})` - Interpreta um texto como data.

- `TO_NUMBER({texto} [, {formato} [, {parâmetros}]])` - Interpreta um texto como número.

Subtrair dois valores `DATE` retorna dias, incluindo frações correspondentes ao horário. Somar um número a um `DATE` acrescenta dias. Já o formato exibido é controlado pelo cliente ou por `TO_CHAR`.

```sql
SELECT DATE '2026-05-22' - DATE '2026-05-20' AS dias,
       TO_CHAR(ADD_MONTHS(DATE '2026-01-31', 1), 'DD/MM/YYYY') AS proximo_mes
FROM DUAL; -- 2; 28/02/2026.

SELECT primeiro, TO_CHAR(admissao, 'DD/MM/YYYY') AS admissao,
       FLOOR(SYSDATE - admissao) AS dias_completos
FROM Funcionario
WHERE admissao >= TO_DATE('01/01/2020', 'DD/MM/YYYY');

SELECT TO_CHAR(1500.75, 'FM999G990D00',
               'NLS_NUMERIC_CHARACTERS = '',.''') AS valor_formatado,
       TO_NUMBER('1500,75', '9999D99',
                 'NLS_NUMERIC_CHARACTERS = '',.''') AS valor_numerico
FROM DUAL; -- Texto: 1.500,75; número: 1500.75.
```

Nos modelos numéricos, `G` representa o separador de grupos, `D` o decimal e `0` uma posição obrigatória. `FM` retira preenchimentos; repetir `FM` alterna seu efeito. Máscaras explícitas evitam depender do formato padrão da sessão.

## Nulos e Condições:

- `NVL({valor}, {substituto})` - Utiliza o substituto quando o primeiro argumento é nulo.

- `COALESCE({a}, {b}, ...)` - Retorna o primeiro argumento não nulo.

- `NULLIF({a}, {b})` - Retorna `NULL` se os argumentos forem iguais; caso contrário, retorna `a`.

- `CASE WHEN {condição} THEN {resultado} ... [ELSE {alternativa}] END` - Retorna o resultado da primeira condição verdadeira; sem correspondência nem `ELSE`, retorna `NULL`.

```sql
SELECT 
	nome,
    ROUND(preco * (1 - NVL(desconto, 0)), 2) AS preco_final,
    preco / NULLIF(estoque, 0) AS razao_preco_estoque,
    CASE
        WHEN estoque < 30 THEN 'Baixo'
        WHEN estoque < 100 THEN 'Regular'
        ELSE 'Alto'
	END AS situacao
FROM Produto;
```

Nesse exemplo, ausência de desconto é tratada como zero; estoque zero produz uma razão nula. A substituição de valores ausentes depende da regra do problema.

---

# 5. Agrupamento e Agregação

As funções de grupo resumem várias linhas. `GROUP BY` separa as linhas em conjuntos; sem ele, a agregação considera todo o resultado selecionado como um grupo.

## Funções e Cláusulas:

- `COUNT(*)` - Conta linhas. Esse comando permite variações:

	- `COUNT({expressão})` - Conta valores não nulos. 
	- `COUNT(DISTINCT {expressão})` - Conta os distintos.

- `SUM({expressão})` - Soma dos valores não nulos.

- `AVG({expressão})` - Tira a média dos valores não nulos.

- `MAX({expressão})` / `MIN({expressão})` - Maior ou menor valor não nulo entre as linhas do grupo.

- `VARIANCE({expressão})` - Variância dos valores: uma medida de dispersão em torno da média.

- `GROUP BY {expressões}` - Define os grupos.

- `HAVING {condição}` - Filtra os grupos formados.

`WHERE` filtra linhas antes de agrupar; `HAVING` filtra os grupos. Nos exemplos simples, as colunas selecionadas sem agregação também devem aparecer no `GROUP BY`.

> `MAX`/`MIN` comparam linhas do grupo; `GREATEST`/`LEAST` comparam argumentos de uma linha. `COUNT` retorna zero quando não há itens; funções como `SUM` e `AVG` retornam `NULL` quando não há valores.

## Exemplo Integrado:

```sql
SELECT dept_id, COUNT(*) AS quantidade,
       ROUND(AVG(salario), 2) AS media, SUM(salario) AS total
FROM Funcionario
WHERE salario >= 3500
GROUP BY dept_id
HAVING COUNT(*) > 1 AND AVG(salario) > 4000
ORDER BY dept_id;
-- Departamento 10: 3 funcionários; média 5433.50; total 16300.50.
-- Departamento 20: 2 funcionários; média 4850.00; total 9700.00.
-- Departamento 40: 2 funcionários; média 5550.13; total 11100.25.

SELECT COUNT(*) AS linhas, COUNT(comissao) AS com_comissao,
       AVG(comissao) AS media_informada,
       AVG(NVL(comissao, 0)) AS media_com_zeros
FROM Funcionario;
-- Onze linhas, seis com comissão: as médias usam quantidades diferentes.
```

---

# 6. Consultas com Múltiplas Tabelas

## Junções:

Junções (*joins*) combinam linhas de acordo com uma condição. Os apelidos de tabela identificam a origem das colunas: em `Funcionario F`, `F.mat` pertence a essa fonte.

- `{A} JOIN {B} ON {condição}` - Junção interna: mantém as combinações que atendem à condição; equivale a `INNER JOIN`.

- `{A} LEFT JOIN {B} ON {condição}` - Preserva todas as linhas de `A`, preenchendo com `NULL` as colunas de `B` sem correspondência.

- `{A} RIGHT JOIN {B} ON {condição}` - Preserva as linhas de `B`.

- `{A} FULL OUTER JOIN {B} ON {condição}` - Preserva as linhas dos dois lados.

- `{A} CROSS JOIN {B}` - Produz todas as combinações; com `m` e `n` linhas, retorna `m * n` linhas.

### Exemplo Integrado:

```sql
SELECT F.primeiro, D.nome AS departamento
FROM Funcionario F
JOIN Departamento D ON F.dept_id = D.dept_id
ORDER BY F.mat; -- Não inclui Bruno, que está sem departamento.

SELECT D.dept_id, D.nome, COUNT(F.mat) AS funcionarios
FROM Departamento D
LEFT JOIN Funcionario F ON D.dept_id = F.dept_id
GROUP BY D.dept_id, D.nome
ORDER BY D.dept_id; -- Inclui Gerência com zero funcionários.

SELECT F.primeiro, D.nome AS departamento
FROM Funcionario F
FULL OUTER JOIN Departamento D ON F.dept_id = D.dept_id
ORDER BY D.dept_id, F.mat; -- Inclui Bruno e Gerência.
```

No `LEFT JOIN`, `COUNT(F.mat)` ignora a referência nula e retorna zero para Gerência. `COUNT(*)` contaria a linha preservada pela junção e retornaria um.

### Filtros em Junções Externas:

```sql
SELECT D.nome, F.primeiro, F.salario
FROM Departamento D
LEFT JOIN Funcionario F
    ON D.dept_id = F.dept_id AND F.salario > 5000
ORDER BY D.dept_id, F.mat;
```

No `ON`, o filtro limita as correspondências e mantém todos os departamentos. Movê-lo para `WHERE F.salario > 5000` elimina os departamentos sem correspondência, pois o salário dessas linhas é nulo.

### Autojunção:

Uma autojunção (*self join*) utiliza a mesma tabela em papéis diferentes, como funcionário e supervisor.

```sql
ALTER TABLE Funcionario ADD mat_supervisor NUMBER(5)
CONSTRAINT fk_supervisor REFERENCES Funcionario(mat);

UPDATE Funcionario SET mat_supervisor = 1 WHERE mat IN (2, 3);

SELECT F.primeiro AS funcionario, G.primeiro AS supervisor
FROM Funcionario F
LEFT JOIN Funcionario G ON F.mat_supervisor = G.mat
ORDER BY F.mat; -- Rafael e Daniela têm Carlos como supervisor.

ROLLBACK;
ALTER TABLE Funcionario DROP COLUMN mat_supervisor;
```

## Subconsultas:

Uma subconsulta (*subquery*) é um `SELECT` dentro de outro comando. Pode fornecer um valor, uma lista ou uma fonte de linhas; é correlacionada quando utiliza uma coluna da consulta externa.

- `({SELECT})` em uma expressão - Fornece um único valor: uma coluna e até uma linha. Sem linhas, retorna `NULL`; com mais de uma, ocorre erro.

- `FROM ({SELECT}) {apelido}` - Utiliza o resultado interno como fonte da consulta externa.

- `{valor} IN ({SELECT})` / `NOT IN (...)` - Verifica presença ou ausência no resultado.

- `{valor} {comparador} ANY ({SELECT})` - Exige comparação verdadeira com ao menos um valor.

- `{valor} {comparador} ALL ({SELECT})` - Exige comparação verdadeira com todos os valores.

- `EXISTS ({SELECT})` / `NOT EXISTS (...)` - Verifica a existência ou ausência de linhas correspondentes.

> Se um `NOT IN` escalar receber uma lista com `NULL`, nenhuma linha passa por essa condição. Para verificar ausência de correspondências, `NOT EXISTS` costuma expressar melhor a intenção.

### Exemplos Combinados:

```sql
-- Salários acima da média geral.
SELECT primeiro, salario FROM Funcionario
WHERE salario > (SELECT AVG(salario) FROM Funcionario)
ORDER BY salario DESC;

-- Usa o agrupamento como uma fonte de linhas.
SELECT R.dept_id, R.media
FROM (
    SELECT dept_id, ROUND(AVG(salario), 2) AS media
    FROM Funcionario GROUP BY dept_id
) R
WHERE R.media > 4000
ORDER BY R.dept_id;

-- Subconsulta correlacionada: departamento sem funcionários.
SELECT D.dept_id, D.nome FROM Departamento D
WHERE NOT EXISTS (
    SELECT 1 FROM Funcionario F WHERE F.dept_id = D.dept_id
); -- 60, Gerência.

-- Salário maior que todos os salários do departamento 30.
SELECT primeiro, salario FROM Funcionario
WHERE salario > ALL (
    SELECT salario FROM Funcionario WHERE dept_id = 30
);
```

Com os salários não nulos do departamento 30, `> ANY` equivale a superar o menor e `> ALL`, o maior. Para um resultado vazio, `ANY` é falso e `ALL` é verdadeiro; nulos também exigem cuidado nessa comparação.

## Operações de Conjunto:

Combinam linhas de consultas com a mesma quantidade de colunas e tipos compatíveis em cada posição.

- `{SELECT} UNION {SELECT}` - Reúne os resultados e elimina duplicatas.

- `{SELECT} UNION ALL {SELECT}` - Reúne os resultados preservando duplicatas.

- `{SELECT} INTERSECT {SELECT}` - Mantém as linhas presentes nos dois resultados, sem duplicatas.

- `{SELECT} MINUS {SELECT}` - Mantém as linhas do primeiro resultado ausentes no segundo, sem duplicatas.

```sql
SELECT mat FROM Funcionario WHERE dept_id = 10
UNION
SELECT mat FROM Funcionario WHERE dept_id = 30
ORDER BY mat; -- 1, 2, 4, 7 e 9.

SELECT dept_id FROM Departamento
INTERSECT
SELECT dept_id FROM Funcionario
ORDER BY dept_id; -- 10, 20, 30, 40 e 50.

SELECT dept_id FROM Departamento
MINUS
SELECT dept_id FROM Funcionario
ORDER BY dept_id; -- 60.
```

---

# 7. Visões

Uma **visão** (*view*) é uma consulta armazenada, utilizada como uma tabela virtual. Funciona como uma janela sobre os dados: pode simplificar consultas e expor apenas as linhas e colunas necessárias.

## Comandos:

- `CREATE VIEW {nome} AS {SELECT}` - Cria uma visão.

- `CREATE OR REPLACE VIEW {nome} AS {SELECT}` - Cria ou substitui sua definição.

- `SELECT {expressões} FROM {visão}` - Consulta a visão.

- `WITH CHECK OPTION` - Ao final da definição, impede escritas pela visão que deixariam a linha fora de seu filtro.

- `DROP VIEW {nome}` - Remove a visão, preservando as tabelas base.

Alterações por uma visão atualizável atingem as tabelas base. Agregações, operações de conjunto e certas junções impedem ou restringem a atualização direta.

## Exemplo Integrado:

```sql
CREATE OR REPLACE VIEW vw_Depto10 AS
SELECT mat, primeiro, ultimo, salario, dept_id
FROM Funcionario
WHERE dept_id = 10
WITH CHECK OPTION;
```

```sql
SELECT primeiro, salario FROM vw_Depto10 ORDER BY mat;

UPDATE vw_Depto10 SET salario = salario + 100 WHERE mat = 1;
SELECT salario FROM Funcionario WHERE mat = 1; -- 4000 na tabela base.
ROLLBACK;

-- Caso inválido, para testar separadamente:
-- UPDATE vw_Depto10 SET dept_id = 20 WHERE mat = 1;
-- WITH CHECK OPTION rejeita a saída do departamento 10.
```

```sql
DROP VIEW vw_Depto10;
```

---

# 8. *Triggers*

Um **gatilho** (*trigger*) executa automaticamente em resposta a um evento. Os exemplos utilizam operações DML para validar datas, calcular uma coluna e bloquear inserções.

## Estrutura e Eventos:

- `CREATE OR REPLACE TRIGGER {nome} ...` - Cria ou substitui o gatilho.

- `BEFORE {eventos} ON {tabela}` / `AFTER ...` - Executa antes ou depois da operação no ponto definido pelo gatilho.

- `INSERT OR UPDATE OR DELETE` - Eventos DML que podem ser selecionados ou combinados.

- `UPDATE OF {colunas}` - Restringe o evento às atualizações que mencionem essas colunas.

- `FOR EACH ROW` - Executa para cada linha afetada; sem a cláusula, executa uma vez por instrução.

- `DROP TRIGGER {nome}` - Remove o gatilho.

O corpo PL/SQL fica entre `BEGIN` e `END`. Declarações opcionais ficam em `DECLARE`, antes de `BEGIN`; atribuições usam `:=` e condições utilizam `IF ... THEN ... END IF`.

- `RAISE_APPLICATION_ERROR({código}, {mensagem})` - Gera um erro da aplicação; códigos de `-20000` a `-20999`.

`AFTER` não significa depois do `COMMIT`: os gatilhos DML comuns participam da mesma transação do comando. Restrições simples, como obrigatoriedade e unicidade, continuam sendo expressas diretamente por *constraints*.

## Valores da Linha:

| Evento | `:OLD` | `:NEW` |
|---|---|---|
| `INSERT` | Campos nulos. | Nova linha. |
| `UPDATE` | Valores anteriores. | Valores após a atualização. |
| `DELETE` | Linha excluída. | Campos nulos. |

Esses pseudorregistros são utilizados nos gatilhos por linha. Um gatilho `BEFORE INSERT OR UPDATE` pode modificar `:NEW`; campos não alterados pelo `UPDATE` já mantêm seus valores em `:NEW`.

## Validação e Cálculo Automático:

O gatilho abaixo rejeita datas invertidas e calcula dias completos. Sem data final, a duração fica nula. A chave de `Ocupa` permite apenas um registro por par funcionário/cargo.

```sql
CREATE OR REPLACE TRIGGER trg_Ocupa_Datas
BEFORE INSERT OR UPDATE ON Ocupa
FOR EACH ROW
BEGIN
    IF :NEW.data_fim IS NOT NULL AND :NEW.data_fim < :NEW.data_inicio THEN
        RAISE_APPLICATION_ERROR(-20001, 'data_fim anterior a data_inicio.');
    END IF;

    :NEW.quantidade_dias := NULL;
    IF :NEW.data_fim IS NOT NULL THEN
        :NEW.quantidade_dias := FLOOR(:NEW.data_fim - :NEW.data_inicio);
    END IF;
END;
/
```

```sql
INSERT INTO Ocupa (codigo_funcionario, codigo_cargo, data_inicio, data_fim)
VALUES (1, 1, DATE '2026-05-22', DATE '2026-06-21'); -- 30 dias.

INSERT INTO Ocupa (codigo_funcionario, codigo_cargo, data_inicio, data_fim)
VALUES (2, 2, DATE '2026-05-11', DATE '2026-05-11'); -- Zero dias.

SELECT F.nome, C.descricao, O.quantidade_dias
FROM Ocupa O
JOIN Funcionario F ON F.codigo = O.codigo_funcionario
JOIN Cargo C ON C.codigo = O.codigo_cargo
ORDER BY F.codigo;

UPDATE Ocupa SET data_fim = NULL
WHERE codigo_funcionario = 1 AND codigo_cargo = 1;
SELECT quantidade_dias FROM Ocupa
WHERE codigo_funcionario = 1 AND codigo_cargo = 1; -- NULL.

-- Caso inválido, para testar separadamente:
-- UPDATE Ocupa SET data_fim = DATE '2020-01-01'
-- WHERE codigo_funcionario = 1 AND codigo_cargo = 1;

ROLLBACK;
```

## Bloqueio por Instrução:

Sem `FOR EACH ROW`, o gatilho atua sobre o comando e não possui uma linha individual para acessar por `:NEW` ou `:OLD`.

```sql
CREATE OR REPLACE TRIGGER trg_Trava_Funcionario
BEFORE INSERT ON Funcionario
BEGIN
    RAISE_APPLICATION_ERROR(-20002, 'Inserções temporariamente bloqueadas.');
END;
/
```

```sql
-- Caso inválido enquanto o gatilho existir:
-- INSERT INTO Funcionario (codigo, nome) VALUES (4, 'Mariana');

DROP TRIGGER trg_Trava_Funcionario;

INSERT INTO Funcionario (codigo, nome) VALUES (4, 'Mariana'); -- Agora funciona.
ROLLBACK;
```

## Erros e Limpeza:

- `SHOW ERRORS TRIGGER {nome}` - Exibe erros de compilação no *SQL\*Plus*; comando do cliente, sem `;`.
- `SELECT name, line, text FROM user_errors` - Consulta os erros dos objetos do próprio usuário.

```sql
SELECT name, line, text FROM user_errors
WHERE type = 'TRIGGER' ORDER BY name, sequence;

DROP TRIGGER trg_Ocupa_Datas;
```

Apagar uma tabela também remove os gatilhos associados a ela. A limpeza das tabelas deste cenário está no apêndice.

---

# 9. Ferramentas Administrativas

## Ambiente e Conexão:

A **instância** reúne memória e processos que acessam o banco. Em uma instalação *multitenant*, o **CDB** contém bancos conectáveis, os **PDBs**, que compartilham infraestrutura. O **serviço** identifica o destino da conexão.

O *schema* agrupa os objetos de um usuário. `usuario.Funcionario` identifica sua tabela; outro usuário pode ter uma tabela de mesmo nome. O prefixo escolhe o objeto, mas não concede acesso a ele.

- `sqlplus {usuário}@//{host}:{porta}/{serviço}` - Abre a conexão pelo terminal, solicitando a senha.

- `sqlplus sys@//{host}:{porta}/{serviço} as sysdba` - Abre uma conexão administrativa.

- `SHOW USER` / `SHOW CON_NAME` - Mostra usuário ou contêiner atual no *SQL\*Plus*.

- `SET AUTOCOMMIT OFF` - Desativa a confirmação automática do cliente, preservando os efeitos de confirmação implícita do banco.

`SHOW` e `SET` acima são comandos do cliente, sem `;`. Os exemplos utilizam `localhost:1521/FREEPDB1`; ajuste esses valores à instalação.

| Acesso    | Utilização                                                                 |
| --------- | -------------------------------------------------------------------------- |
| Padrão    | Privilégios concedidos ao usuário e papéis habilitados.                    |
| `SYSDBA`  | Administração ampla do banco.                                              |
| `SYSOPER` | Operações administrativas mais restritas, como iniciar e desligar o banco. |

`SYSDBA` e `SYSOPER` são privilégios administrativos especiais. `SYS` exige conexão administrativa apropriada; `SYSTEM` pode operar em conexão padrão com seus privilégios. Utilize um usuário próprio para os exemplos.

## Usuários e Quotas:

- `CREATE USER {usuário} IDENTIFIED BY "{senha}"` - Cria o usuário.

- `ALTER USER {usuário} IDENTIFIED BY "{senha}"` - Altera a senha, respeitando privilégios e política aplicada.

- `ALTER USER {usuário} QUOTA {limite} ON {tablespace}` - Define o espaço que o usuário pode alocar; `UNLIMITED` retira esse limite de quota.

- `ALTER USER {usuário} ACCOUNT LOCK` / `ACCOUNT UNLOCK` - Bloqueia novas autenticações ou desbloqueia a conta.

- `DROP USER {usuário} CASCADE` - Remove o usuário e seus objetos.

Uma *tablespace* organiza o armazenamento. Ter permissão para criar tabelas não dispensa a quota necessária para alocá-las.

## Permissões:

- `GRANT {privilégios} TO {usuário}` - Concede privilégios de sistema, como `CREATE SESSION` ou `CREATE TABLE`.

- `REVOKE {privilégios} FROM {usuário}` - Revoga os privilégios indicados.

- `GRANT {operações} ON {objeto} TO {usuário}` - Concede acesso a um objeto, como `SELECT`, `INSERT`, `UPDATE` ou `DELETE`.

- `REVOKE {operações} ON {objeto} FROM {usuário}` - Retira as concessões indicadas sobre o objeto.

`ALL` pode substituir a lista de privilégios de objeto concedíveis naquele contexto; não significa administração completa do banco. Revogar `CREATE TABLE` não apaga as tabelas existentes.

### Preparação de um Usuário:

Execute como administrador conectado ao **PDB desejado**, com a *tablespace* `USERS` disponível. Depois, conecte-se como `usuario` para preparar o cenário do apêndice.

```sql
CREATE USER usuario IDENTIFIED BY "Troque_Esta_Senha_2026"
DEFAULT TABLESPACE USERS QUOTA 50M ON USERS;

GRANT CREATE SESSION, CREATE TABLE, CREATE VIEW,
      CREATE SEQUENCE, CREATE PROCEDURE, CREATE TRIGGER TO usuario;
```

### Compartilhamento de uma Tabela:

Este bloco pressupõe `Funcionario` criada por `usuario` e uma conta `outro_usuario` já existente no mesmo PDB.

```sql
-- Executado pelo proprietário da tabela.
GRANT SELECT, UPDATE ON Funcionario TO outro_usuario;
REVOKE UPDATE ON Funcionario FROM outro_usuario;
-- OUTRO_USUARIO mantém SELECT em usuario.Funcionario.
```

## Consultas Administrativas:

| Fonte | Informação |
|---|---|
| `USER_TABLES` | Tabelas do próprio usuário. |
| `USER_OBJECTS` | Objetos do próprio usuário. |
| `ALL_USERS` | Usuários visíveis no contêiner. |
| `SESSION_PRIVS` | Privilégios de sistema disponíveis na sessão. |
| `DBA_USERS` | Usuários e situação das contas. |
| `DBA_SYS_PRIVS` | Concessões de privilégios de sistema. |

As visões `DBA_` exigem acesso apropriado, não necessariamente `SYSDBA`. Uma consulta de concessões diretas a um usuário não inclui automaticamente as permissões herdadas de papéis.

```sql
SELECT table_name FROM user_tables ORDER BY table_name;
SELECT object_name, object_type FROM user_objects ORDER BY object_type, object_name;
SELECT privilege FROM session_privs ORDER BY privilege;
```

```sql
SELECT username, account_status FROM dba_users ORDER BY username;
SELECT privilege FROM dba_sys_privs WHERE grantee = 'USUARIO';
```

---

# Apêndice A. Tabelas dos Exemplos

Prepare um cenário, execute seus exemplos e faça a limpeza ao final. Os cenários de consultas e gatilhos reutilizam o nome `Funcionario` com estruturas diferentes; no mesmo usuário, limpe o primeiro antes de preparar o segundo.

## Capítulos 1 - 7:

### Criação e Dados:

```sql
-- Criação das tabelas:

CREATE TABLE Departamento (
    dept_id NUMBER(3) PRIMARY KEY,
    nome VARCHAR2(30 CHAR) NOT NULL,
    cidade VARCHAR2(30 CHAR)
);

CREATE TABLE Funcionario (
    mat NUMBER(5) PRIMARY KEY,
    primeiro VARCHAR2(20 CHAR) NOT NULL,
    ultimo VARCHAR2(20 CHAR) NOT NULL,
    salario NUMBER(10,2) NOT NULL,
    comissao NUMBER(5,2),
    sexo CHAR(1) CHECK (sexo IN ('M', 'F')),
    admissao DATE DEFAULT SYSDATE,
    dept_id NUMBER(3) REFERENCES Departamento(dept_id)
);

CREATE TABLE Produto (
    prod_id NUMBER(5) PRIMARY KEY,
    nome VARCHAR2(40 CHAR) NOT NULL,
    preco NUMBER(10,4) NOT NULL,
    estoque NUMBER(7) NOT NULL,
    desconto NUMBER(5,4),
    variacao NUMBER(10,4)
);

-- Inserção dos Dados:

INSERT INTO Departamento VALUES (10, 'Tecnologia', 'São Paulo');
INSERT INTO Departamento VALUES (20, 'Financeiro', 'Rio de Janeiro');
INSERT INTO Departamento VALUES (30, 'RH', 'Belo Horizonte');
INSERT INTO Departamento VALUES (40, 'Marketing', 'Curitiba');
INSERT INTO Departamento VALUES (50, 'Logística', 'Porto Alegre');
INSERT INTO Departamento VALUES (60, 'Gerência', 'Brasília');

INSERT INTO Funcionario VALUES (1, 'Carlos', 'Ramos', 3900, 0.10, 'M', DATE '2018-03-15', 10);
INSERT INTO Funcionario VALUES (2, 'Rafael', 'Rodrigues', 5200.50, NULL, 'M', DATE '2015-07-01', 10);
INSERT INTO Funcionario VALUES (3, 'Daniela', 'Silva', 4100, 0.15, 'F', DATE '2020-11-22', 20);
INSERT INTO Funcionario VALUES (4, 'raquel', 'Dourado', 3750.75, NULL, 'F', DATE '2019-01-30', 30);
INSERT INTO Funcionario VALUES (5, 'Marcos', 'Rabelo', 6800, 0.20, 'M', DATE '2012-06-10', 40);
INSERT INTO Funcionario VALUES (6, 'Isabelle', 'Castilho', 4950, NULL, 'F', DATE '2021-09-05', 50);
INSERT INTO Funcionario VALUES (7, 'wellington', 'Dallagnol', 3200, 0.05, 'M', DATE '2023-02-28', 30);
INSERT INTO Funcionario VALUES (8, 'Patricia', 'Rulloch', 5600, 0.12, 'F', DATE '2017-04-18', 20);
INSERT INTO Funcionario VALUES (9, 'André', 'Albuquerque', 7200, NULL, 'M', DATE '2010-12-01', 10);
INSERT INTO Funcionario VALUES (10, 'Camila', 'Fonseca', 4300.25, 0.08, 'F', DATE '2022-08-14', 40);
INSERT INTO Funcionario VALUES (11, 'Bruno', 'Reis', 2800, NULL, 'M', DATE '2024-02-01', NULL);

INSERT INTO Produto VALUES (1, 'Notebook Pro', 3599.9990, 47, 0.10, 250.5000);
INSERT INTO Produto VALUES (2, 'Monitor 24"', 899.9900, 100, NULL, -30.7500);
INSERT INTO Produto VALUES (3, 'Teclado Mecânico', 249.9950, 83, 0.05, 15.0000);
INSERT INTO Produto VALUES (4, 'Mouse Ergonômico', 89.9900, 144, NULL, -89.9900);
INSERT INTO Produto VALUES (5, 'Webcam HD', 199.9000, 36, 0.15, 0);
INSERT INTO Produto VALUES (6, 'Headset Gamer', 329.9900, 25, NULL, -120.0000);
INSERT INTO Produto VALUES (7, 'SSD 1TB', 399.9000, 64, 0.08, 40.0000);
INSERT INTO Produto VALUES (8, 'Hub USB-C', 79.9900, 200, NULL, -5.5000);
INSERT INTO Produto VALUES (9, 'Suporte Notebook', 129.9900, 49, 0.03, 8.2500);
INSERT INTO Produto VALUES (10, 'Cadeira Gamer', 1499.9900, 10, 0.12, -75.0000);

COMMIT;
```

### Limpeza:

```sql
DROP TABLE Funcionario;
DROP TABLE Departamento;
DROP TABLE Produto;
```

## Capítulo 8:

`Ocupa` começa vazia; os registros são inseridos nos exemplos, depois da criação do gatilho.

### Criação e Dados:

```sql
CREATE TABLE Funcionario (
    codigo NUMBER(12) PRIMARY KEY,
    nome VARCHAR2(100 CHAR) NOT NULL
);

CREATE TABLE Cargo (
    codigo NUMBER(12) PRIMARY KEY,
    descricao VARCHAR2(100 CHAR) NOT NULL
);

CREATE TABLE Ocupa (
    codigo_funcionario NUMBER(12) REFERENCES Funcionario(codigo),
    codigo_cargo NUMBER(12) REFERENCES Cargo(codigo),
    data_inicio DATE NOT NULL,
    data_fim DATE,
    quantidade_dias NUMBER(8),
    PRIMARY KEY (codigo_funcionario, codigo_cargo)
);

INSERT INTO Funcionario VALUES (1, 'Carlos');
INSERT INTO Funcionario VALUES (2, 'Cleitin');
INSERT INTO Funcionario VALUES (3, 'Manuel');
INSERT INTO Cargo VALUES (1, 'Analista de Sistemas');
INSERT INTO Cargo VALUES (2, 'Gerente de Projetos');
INSERT INTO Cargo VALUES (3, 'Chefe de Segurança');
COMMIT;
```

### Limpeza:

```sql
DROP TABLE Ocupa;
DROP TABLE Cargo;
DROP TABLE Funcionario;
```

---

# Fontes

- BARROS, Evandrino Gomes. Disciplina: Banco de Dados I. Curso de graduação em Engenharia de Computação - Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2026.

- ORACLE. *SQL Language Reference*. *Oracle Database* 19c. [S. l.]: Oracle, 2026. Disponível em: [https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/). Acesso em: 2 out. 2026.
- ORACLE. *PL/SQL Language Reference*. *Oracle Database* 19c. [S. l.]: Oracle, 2026. Disponível em: [https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/). Acesso em: 2 out. 2026.
- ORACLE. *Database Administrator’s Guide*. *Oracle Database* 19c. [S. l.]: Oracle, 2026. Disponível em: [https://docs.oracle.com/en/database/oracle/oracle-database/19/admin/](https://docs.oracle.com/en/database/oracle/oracle-database/19/admin/). Acesso em: 2 out. 2026.
- ORACLE. *Multitenant Administrator’s Guide*. *Oracle Database* 19c. [S. l.]: Oracle, 2025. Disponível em: [https://docs.oracle.com/en/database/oracle/oracle-database/19/multi/](https://docs.oracle.com/en/database/oracle/oracle-database/19/multi/). Acesso em: 2 out. 2026.
- ORACLE. *SQL\*Plus User’s Guide and Reference*. *Oracle Database* 19c. [S. l.]: Oracle, 2025. Disponível em: [https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/). Acesso em: 2 out. 2026.
- JUNIATA COLLEGE. *Three Level Database Architecture*. [S. l.]: Juniata College, [s. d.]. Disponível em: [https://jcsites.juniata.edu/faculty/rhodes/dbms/dbarch.htm](https://jcsites.juniata.edu/faculty/rhodes/dbms/dbarch.htm). Acesso em: 2 out. 2026.
