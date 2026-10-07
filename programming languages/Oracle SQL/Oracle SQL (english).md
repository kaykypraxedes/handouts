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

# 0. Basic Concepts

> This handout covers SQL in Oracle Database. The administrative examples use `FREEPDB1` as the service name (this name depends on the installation).

## Databases and SQL:

A **database** brings together organized information; a **Database Management System (DBMS)** stores, queries, and controls access to that information. Oracle Database is a DBMS.

In a relational database, **tables** organize records into **rows**, with their information distributed across **columns**. An employee table might contain an employee ID, name, and department.

**SQL** allows you to define structures, query data, and modify data. It is declarative: it specifies the desired result, such as “employees in department 10”, without describing how to traverse the records. **PL/SQL** adds variables, conditions, and programming blocks, used here in *triggers*.

## Three-Schema Architecture:

This architecture separates what each application sees, the overall organization of the data, and how the data is stored.

| Level | Purpose | Example |
| --------------------------------------- | --------------------------------------------------- | ------------------------------------------------------- |
| **VDL (View Definition Language)** | External. A view of the data for each audience. | HR queries salaries; the internal directory shows names. |
| **DDL (Data Definition Language)** | Conceptual. Database structures, relationships, and rules. | Employees linked to departments. |
| **SDL (Storage Definition Language)** | Internal. Storage and access structures. | Files and indexes. |

In Oracle, definition commands can address more than one of these needs.

- **Physical independence:** allows storage to change without altering the conceptual structure.
- **Logical independence:** aims to preserve external interfaces when the conceptual structure changes.

## Conventions and Examples:

Keywords are written in uppercase by convention. Names without double quotes do not distinguish between uppercase and lowercase (the language is not *case-sensitive*); text uses single quotes (`'Carlos'`), and delimited identifiers use double quotes (`"Nome Completo"`).

- `-- {comment}` - Comments out the rest of the line.

- `/* {comment} */` - Delimits a comment spanning one or more lines.

- `;` - Terminates SQL commands in the clients used here.

- `/` - On a line of its own, executes the PL/SQL block in SQL\*Plus script mode.

---

# 1. Table Structure

## Data Types:

| Type | Usage |
|---|---|
| `NUMBER(p,s)` | Number with precision `p` and scale `s`; for example, `NUMBER(8,2)` allows six integer digits and two decimal digits. |
| `NUMBER(p)` | Equivalent to `NUMBER(p,0)`, used for integers of up to `p` digits. |
| `VARCHAR2(n)` | Variable-length text, up to the specified limit. |
| `CHAR(n)` | Fixed-length text, padded with spaces. |
| `DATE` | Date and time, down to seconds. |
| `TIMESTAMP` | Date and time, including fractional seconds. |

For text, `VARCHAR2(30 CHAR)` limits characters, and `VARCHAR2(30 BYTE)` limits bytes (in UTF-8, accented characters, for example, take up two bytes).

> Numbers do not preserve leading zeros: to keep `001` as an identifier, use text.

## DDL Commands:

**DDL (Data Definition Language)** groups commands that define structures.

- `CREATE TABLE {table} ({column} {type}, ...)` - Creates a table with the specified columns.

- `ALTER TABLE {table} ADD {column} {type}` - Adds a column.

- `ALTER TABLE {table} MODIFY {column} {definition}` - Changes a column's type, size, required status, or default value.

- `ALTER TABLE {table} RENAME COLUMN {old} TO {new}` - Renames a column.

- `ALTER TABLE {table} DROP COLUMN {column}` - Removes a column.

- `ALTER TABLE {table} RENAME TO {new_name}` - Renames the table.

- `DROP TABLE {table}` - Removes the table and its data.

> Changes to types and constraints must be compatible with existing data. For example, setting `NOT NULL` requires the column to contain no nulls.

## Default Values and Constraints:

**`NULL` represents the absence of a value**, not zero or the word `'NULL'`. In Oracle, empty text (`''`) is also treated as null.

- `DEFAULT {value}` - Defines a default value for the column. It is used when the column is omitted from an insertion or when the assignment uses `DEFAULT`.

Changing the default does not replace existing data. With an ordinary `DEFAULT` declaration, explicitly supplying `NULL` does not apply the default.

***Constraints*** define the values and relationships accepted by the table.

| Constraint | Rule |
| ------------- | --------------------------------------------------------------------------------- |
| `PRIMARY KEY` | Unique, non-null identifier for the row. |
| `FOREIGN KEY` | Reference to a primary or unique key. |
| `UNIQUE` | Prevents duplicate values; a column with this constraint can contain multiple nulls. |
| `NOT NULL` | Requires a value. |
| `CHECK` | Rejects values that make the condition false. |

A **composite key** uses the combination of columns: `PRIMARY KEY (a, b)` accepts `(1,2)` and `(1,3)`, but not two occurrences of `(1,2)`. None of its columns can be null.

`CHECK (sexo IN ('M','F'))` accepts `'M'`, `'F'`, or `NULL`. To require a value, combine it with `NOT NULL`. A single-column foreign key also accepts `NULL` when this requirement is absent.

### Declaration and Modification:

- `{column} {type} CONSTRAINT {constraint_name} {constraint}` - Declares a constraint alongside the column.

- `CONSTRAINT {name} PRIMARY KEY ({columns})` - Declares a primary key in the table body; allows composite keys.

- `CONSTRAINT {name} FOREIGN KEY ({columns}) REFERENCES {table} ({columns})` - Declares a foreign key in the table body.

- `ALTER TABLE {table} ADD CONSTRAINT {name} {constraint}` - Adds a constraint.

- `ALTER TABLE {table} DROP CONSTRAINT {name}` - Removes the constraint, preserving the data.

- `ALTER TABLE {table} MODIFY {column} NOT NULL` - Makes the column required; `NULL` allows missing values again.

### Dependencies and Deletion:

| Foreign key option | When deleting a parent row with dependents |
| -------------------------- | -------------------------------------------------------------- |
| Without `ON DELETE` | Rejects the deletion. |
| `ON DELETE CASCADE` | Deletes the corresponding child rows. |
| `ON DELETE SET NULL` | Sets the reference to null, if the other rules allow it. |

- `DROP TABLE {table} CASCADE CONSTRAINTS` - Removes the table and the referential constraints that depend on its keys; preserves the child tables.

- `ALTER TABLE {table} DROP COLUMN {column} CASCADE CONSTRAINTS` - Removes the column and dependent constraints.

```sql
-- Table creation.
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

-- Constraint added after creation.
ALTER TABLE Empregado_Exemplo
ADD CONSTRAINT fk_emp_exemplo FOREIGN KEY (dept_id)
REFERENCES Departamento_Exemplo(id) ON DELETE CASCADE;

-- Changes to columns and default values.
ALTER TABLE Empregado_Exemplo ADD email VARCHAR2(60 CHAR);
ALTER TABLE Empregado_Exemplo ADD CONSTRAINT uk_emp_email UNIQUE (email);
ALTER TABLE Empregado_Exemplo MODIFY nome VARCHAR2(80 CHAR);
ALTER TABLE Empregado_Exemplo MODIFY salario DEFAULT 2500;
ALTER TABLE Empregado_Exemplo RENAME COLUMN nome TO nome_completo;
ALTER TABLE Empregado_Exemplo DROP CONSTRAINT uk_emp_email;
ALTER TABLE Empregado_Exemplo DROP COLUMN email;

-- Renames the parent table, then removes its key and dependencies.
ALTER TABLE Departamento_Exemplo RENAME TO Departamento_Aux;
ALTER TABLE Departamento_Aux DROP COLUMN id CASCADE CONSTRAINTS;

-- Cleanup of the auxiliary scenario.
DROP TABLE Empregado_Exemplo;
DROP TABLE Departamento_Aux;
```

---

# 2. Basic Queries

> The applications and SQL command examples from this point onward use the structures defined in the appendix, allowing you to test the commands interactively.

## Selection and Filters:

- `SELECT {expressions} FROM {table}` - Queries columns or expressions; `*` selects all columns.

Additional parts can be added to `SELECT` to format the data and control how it is returned:

- `AS {alias}` - Defines the column name in the result.

- `WHERE {condition}` - Keeps only rows whose condition is true.

- `DISTINCT {expressions}` - Eliminates duplicates of the selected combination.

- `ORDER BY {column} {ASC|DESC}, ...` - Sorts the result; `ASC` is ascending and the default (implicit), while `DESC` is descending.

- `NULLS FIRST` / `NULLS LAST` - Defines where nulls appear in a sorting criterion.

- `FETCH FIRST {n} ROWS ONLY` - Limits the number of rows returned.

| Operator | Usage |
|---|---|
| `=`, `<>` or `!=` | Equal or not equal. |
| `<`, `>`, `<=`, `>=` | Ordering comparisons. |
| `AND`, `OR`, `NOT` | Combination or negation of conditions. |
| `BETWEEN a AND b` | Range including both endpoints. |
| `IN (...)` | Match with any value in the list. |
| `LIKE {pattern}` | Text comparison: `%` represents zero or more characters; `_`, one character. |
| `IS NULL` / `IS NOT NULL` | Absence or presence of a value. |

Comparisons such as `coluna = NULL` are unknown, not true. Likewise, `coluna <> 10` does not include rows with null values. To include them, use `coluna <> 10 OR coluna IS NULL`.

> `AND` takes precedence over `OR`; parentheses make the grouping explicit. Without `ORDER BY`, row order is not guaranteed.


```sql
-- Filters departments and salary range; breaks ties by employee ID.
SELECT 
	mat, 
	primeiro AS nome, 
	salario
FROM Funcionario
WHERE 
	dept_id IN (10, 20) AND salario BETWEEN 4000 AND 6000
ORDER BY salario DESC, mat;
-- Patricia: 5600; Rafael: 5200.50; Daniela: 4100.

-- Each combination appears only once.
SELECT DISTINCT sexo, dept_id FROM Funcionario ORDER BY sexo, dept_id; -- Indentation and splitting the command across lines do not affect its execution.

-- Text and absence of a department assignment.
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

-- Three highest salaries.
SELECT 
	primeiro, 
	salario 
FROM Funcionario
ORDER BY salario DESC, mat FETCH FIRST 3 ROWS ONLY;
-- André: 7200; Marcos: 6800; Patricia: 5600.
```

## Expressions:

- `+`, `-`, `*`, `/` - Perform arithmetic operations.

- `||` - Concatenates text.

- `SELECT {expression} FROM DUAL` - Evaluates an expression without relying on an application table.

```sql
SELECT 
	primeiro || ' ' || ultimo AS nome_completo, 
	salario * 1.10 AS salario_simulado
FROM Funcionario;

SELECT 2 + 3 AS resultado FROM DUAL; -- 5.
```

The simulated salary appears only in the query. To store the new value, use an update command.

---

# 3. Data Modification and Transactions

## DML Commands:

**DML (Data Modification Language)** groups commands that modify data:

- `INSERT INTO {table} ({columns}) VALUES ({values})` - Inserts a row into the specified columns.

- `INSERT INTO {table} VALUES ({values})` - Inserts values in the order of the table's columns.

- `UPDATE {table} SET {column} = {value}, ... WHERE {condition}` - Updates the selected rows.

- `DELETE FROM {table} WHERE {condition}` - Deletes the selected rows, preserving the table.

Columns omitted from an insertion receive their default or `NULL`, provided this respects the constraints. Without `WHERE`, `UPDATE` and `DELETE` affect all rows.

## Transaction Control:

A **transaction** groups pending changes. The session itself can already see its modifications; other sessions do not read those changes before they are committed.

- `COMMIT` - Commits the transaction's changes and ends the transaction.
- `ROLLBACK` - Undoes the transaction's pending changes, including changes across multiple tables.
- `SAVEPOINT {name}` - Marks a position within the transaction.
- `ROLLBACK TO {name}` - Undoes changes after the mark, keeping the transaction open.

> DDL such as `CREATE`, `ALTER`, and `DROP` causes an implicit commit before executing a syntactically valid command and after it completes (it cannot be undone with `ROLLBACK`).

```sql
INSERT INTO Funcionario (mat, primeiro, ultimo, salario, dept_id)
VALUES (90, 'Manuel', 'Souza', 3000, 10);

UPDATE Funcionario SET salario = salario + 500 WHERE mat = 90;
SAVEPOINT reajuste;

DELETE FROM Funcionario WHERE mat = 90;
SELECT mat FROM Funcionario WHERE mat = 90; -- No rows.

ROLLBACK TO reajuste;
SELECT mat, salario FROM Funcionario WHERE mat = 90; -- 90, 3500.

ROLLBACK; -- Also undoes the insertion; preserves the study dataset.
```

Using `COMMIT` after the salary adjustment would commit it. A later `ROLLBACK` does not undo changes that have already been committed.

### Cascading Deletion:

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
SELECT * FROM Historico; -- The dependent record was also deleted.

ROLLBACK;
DROP TABLE Historico;
```

## Removal Methods:

| Command | Effect | `ROLLBACK` before commit |
|---|---|---|
| `DELETE FROM {table}` | Deletes rows; accepts `WHERE`. | Can undo. |
| `TRUNCATE TABLE {table}` | Empties an ordinary table through DDL; does not accept a filter. | Cannot undo. |
| `DROP TABLE {table}` | Removes the table. | Cannot undo. |

## Identifier Generation:

A ***sequence*** generates numbers that can identify records. Used numbers are not returned by `ROLLBACK`, so gaps can occur.

- `CREATE SEQUENCE {name} START WITH {start} INCREMENT BY {step}` - Creates the sequence.

- `{sequence}.NEXTVAL` - Gets the next number.

- `{sequence}.CURRVAL` - Gets the last number generated by the sequence in the current session, after a `NEXTVAL`.

- `DROP SEQUENCE {name}` - Removes the sequence.

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

# 4. Functions and Expressions

Single-row functions calculate a result for each row. They can take literal values, columns, or the results of other functions, as in `ROUND(ABS(variacao), 2)`.

## Numeric Functions:

- `ABS({n})` - Absolute value: `ABS(-5)` returns `5`.

- `CEIL({n})` - Smallest integer greater than or equal to the value.

- `FLOOR({n})` - Largest integer less than or equal to the value.

- `MOD({a}, {b})` - Remainder of dividing `a` by `b`.

- `POWER({a}, {b})` - Power with base `a` and exponent `b`.

- `ROUND({n}, {places})` - Rounds to the specified number of decimal places.

- `TRUNC({n}, {places})` - Removes excess decimal places without rounding.

- `SIGN({n})` - Returns `-1`, `0`, or `1`, depending on the number's sign.

- `SQRT({n})` - Square root of a nonnegative number.

- `GREATEST({a}, {b}, ...)` / `LEAST({a}, {b}, ...)` - Largest or smallest value among the arguments in the same row.

> `FLOOR(-3.2)` returns `-4`; `TRUNC(-3.2)` returns `-3`. In `GREATEST` and `LEAST`, a null argument produces a null result.

```sql
SELECT nome, preco, ROUND(preco, 2) AS arredondado,
       TRUNC(preco, 2) AS truncado, CEIL(preco) AS teto
FROM Produto WHERE prod_id = 3;
-- Teclado Mecânico: 249.9950; 250.00; 249.99; 250.

SELECT nome, estoque, MOD(estoque, 2) AS resto
FROM Produto WHERE MOD(estoque, 2) <> 0; -- Odd stock quantities.

UPDATE Produto SET variacao = ABS(variacao) WHERE variacao < 0;
ROLLBACK; -- The function can also be used in updates.
```

## Text Functions:

- `CONCAT({a}, {b})` - Concatenates two pieces of text; for multiple parts, `||` can also be used.

- `LOWER({text})` / `UPPER({text})` - Converts to lowercase or uppercase.

- `INITCAP({text})` - Converts the first letter of each word to uppercase and the remaining letters to lowercase.

- `LPAD({text}, {length}, {padding})` / `RPAD(...)` - Pads on the left or right up to the specified final length.

- `LTRIM({text} [, {characters}])` / `RTRIM(...)` - Removes characters from the left or right edge; spaces by default.

- `TRIM({text})` - Removes spaces from both edges.

- `REPLACE({text}, {search}, {replacement})` - Replaces occurrences of a sequence of characters.

- `TRANSLATE({text}, {source}, {destination})` - Substitutes character by character, according to their positions in the arguments.

- `SUBSTR({text}, {start} [, {count}])` - Extracts a portion of text; negative positions count from the end.

- `INSTR({text}, {search})` - Returns the position of the first occurrence, or `0` if no match is found.

- `LENGTH({text})` - Number of characters.

- `CHR({code})` / `ASCII({text})` - Character corresponding to a code, or numeric representation of the first character, according to the database encoding.

> Text positions start at `1`. In `LTRIM` and `RTRIM`, the optional argument represents a set of removable characters, not an entire word.

```sql
SELECT primeiro || ' ' || ultimo AS nome,
       INITCAP(primeiro) AS nome_padronizado,
       SUBSTR(ultimo, 1, 3) AS inicio_sobrenome,
       LENGTH(primeiro) AS tamanho
FROM Funcionario
WHERE UPPER(primeiro) LIKE 'RA%'; -- Rafael and raquel.

SELECT REPLACE('Oracle SQL', ' ', '_') AS substituicao,
       TRANSLATE('banana', 'aeiou', '*****') AS vogais,
       LPAD('12', 5, '0') AS codigo
FROM DUAL; -- Oracle_SQL; b*n*n*; 00012.
```

## Dates and Conversions:

- `SYSDATE` - Current server date and time.

- `DATE 'YYYY-MM-DD'` - Date literal in a fixed format, at midnight.

- `ADD_MONTHS({date}, {n})` - Adds or subtracts months, adjusting the day when necessary.

- `LAST_DAY({date})` - Last day of the month.

- `MONTHS_BETWEEN({a}, {b})` - Difference in months, which can be fractional.

- `NEXT_DAY({date}, {day_of_week})` - Next occurrence of the day of the week after the date; the name depends on `NLS_DATE_LANGUAGE`.

- `TO_CHAR({value}, {format} [, {parameters}])` - Converts and formats a value as text.

- `TO_DATE({text}, {format})` - Interprets text as a date.

- `TO_NUMBER({text} [, {format} [, {parameters}]])` - Interprets text as a number.

Subtracting two `DATE` values returns days, including fractions corresponding to the time. Adding a number to a `DATE` adds days. The displayed format is controlled by the client or by `TO_CHAR`.

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
FROM DUAL; -- Text: 1.500,75; number: 1500.75.
```

In numeric format models, `G` represents the group separator, `D` the decimal separator, and `0` a required position. `FM` removes padding; repeating `FM` toggles its effect. Explicit format masks avoid relying on the session's default format.

## Nulls and Conditions:

- `NVL({value}, {replacement})` - Uses the replacement when the first argument is null.

- `COALESCE({a}, {b}, ...)` - Returns the first non-null argument.

- `NULLIF({a}, {b})` - Returns `NULL` if the arguments are equal; otherwise, returns `a`.

- `CASE WHEN {condition} THEN {result} ... [ELSE {alternative}] END` - Returns the result of the first true condition; with no match and no `ELSE`, returns `NULL`.

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

In this example, a missing discount is treated as zero; zero stock produces a null ratio. Replacing missing values depends on the problem's rules.

---

# 5. Grouping and Aggregation

Group functions summarize multiple rows. `GROUP BY` separates rows into sets; without it, aggregation treats the entire selected result as one group.

## Functions and Clauses:

- `COUNT(*)` - Counts rows. This command allows variations:

	- `COUNT({expression})` - Counts non-null values.
	- `COUNT(DISTINCT {expression})` - Counts distinct values.

- `SUM({expression})` - Sum of non-null values.

- `AVG({expression})` - Calculates the average of non-null values.

- `MAX({expression})` / `MIN({expression})` - Largest or smallest non-null value among the group's rows.

- `VARIANCE({expression})` - Variance of the values: a measure of dispersion around the mean.

- `GROUP BY {expressions}` - Defines the groups.

- `HAVING {condition}` - Filters the resulting groups.

`WHERE` filters rows before grouping; `HAVING` filters groups. In the simple examples, selected columns without aggregation must also appear in `GROUP BY`.

> `MAX`/`MIN` compare rows in the group; `GREATEST`/`LEAST` compare arguments in one row. `COUNT` returns zero when there are no items; functions such as `SUM` and `AVG` return `NULL` when there are no values.

## Integrated Example:

```sql
SELECT dept_id, COUNT(*) AS quantidade,
       ROUND(AVG(salario), 2) AS media, SUM(salario) AS total
FROM Funcionario
WHERE salario >= 3500
GROUP BY dept_id
HAVING COUNT(*) > 1 AND AVG(salario) > 4000
ORDER BY dept_id;
-- Department 10: 3 employees; average 5433.50; total 16300.50.
-- Department 20: 2 employees; average 4850.00; total 9700.00.
-- Department 40: 2 employees; average 5550.13; total 11100.25.

SELECT COUNT(*) AS linhas, COUNT(comissao) AS com_comissao,
       AVG(comissao) AS media_informada,
       AVG(NVL(comissao, 0)) AS media_com_zeros
FROM Funcionario;
-- Eleven rows, six with commission: the averages use different counts.
```

---

# 6. Queries with Multiple Tables

## Joins:

*Joins* combine rows according to a condition. Table aliases identify the source of columns: in `Funcionario F`, `F.mat` belongs to that source.

- `{A} JOIN {B} ON {condition}` - Inner join: keeps combinations that satisfy the condition; equivalent to `INNER JOIN`.

- `{A} LEFT JOIN {B} ON {condition}` - Preserves all rows from `A`, filling unmatched columns from `B` with `NULL`.

- `{A} RIGHT JOIN {B} ON {condition}` - Preserves rows from `B`.

- `{A} FULL OUTER JOIN {B} ON {condition}` - Preserves rows from both sides.

- `{A} CROSS JOIN {B}` - Produces all combinations; with `m` and `n` rows, returns `m * n` rows.

### Integrated Example:

```sql
SELECT F.primeiro, D.nome AS departamento
FROM Funcionario F
JOIN Departamento D ON F.dept_id = D.dept_id
ORDER BY F.mat; -- Does not include Bruno, who has no department.

SELECT D.dept_id, D.nome, COUNT(F.mat) AS funcionarios
FROM Departamento D
LEFT JOIN Funcionario F ON D.dept_id = F.dept_id
GROUP BY D.dept_id, D.nome
ORDER BY D.dept_id; -- Includes Gerência with zero employees.

SELECT F.primeiro, D.nome AS departamento
FROM Funcionario F
FULL OUTER JOIN Departamento D ON F.dept_id = D.dept_id
ORDER BY D.dept_id, F.mat; -- Includes Bruno and Gerência.
```

In `LEFT JOIN`, `COUNT(F.mat)` ignores the null reference and returns zero for Gerência. `COUNT(*)` would count the row preserved by the join and return one.

### Filters in Outer Joins:

```sql
SELECT D.nome, F.primeiro, F.salario
FROM Departamento D
LEFT JOIN Funcionario F
    ON D.dept_id = F.dept_id AND F.salario > 5000
ORDER BY D.dept_id, F.mat;
```

In `ON`, the filter limits matches and keeps all departments. Moving it to `WHERE F.salario > 5000` eliminates departments without a match, because the salary in those rows is null.

### Self Join:

A *self join* uses the same table in different roles, such as employee and supervisor.

```sql
ALTER TABLE Funcionario ADD mat_supervisor NUMBER(5)
CONSTRAINT fk_supervisor REFERENCES Funcionario(mat);

UPDATE Funcionario SET mat_supervisor = 1 WHERE mat IN (2, 3);

SELECT F.primeiro AS funcionario, G.primeiro AS supervisor
FROM Funcionario F
LEFT JOIN Funcionario G ON F.mat_supervisor = G.mat
ORDER BY F.mat; -- Rafael and Daniela have Carlos as their supervisor.

ROLLBACK;
ALTER TABLE Funcionario DROP COLUMN mat_supervisor;
```

## Subqueries:

A *subquery* is a `SELECT` inside another command. It can provide a value, a list, or a source of rows; it is correlated when it uses a column from the outer query.

- `({SELECT})` in an expression - Provides a single value: one column and at most one row. With no rows, returns `NULL`; with more than one, an error occurs.

- `FROM ({SELECT}) {alias}` - Uses the inner result as the source for the outer query.

- `{value} IN ({SELECT})` / `NOT IN (...)` - Checks for presence or absence in the result.

- `{value} {comparison_operator} ANY ({SELECT})` - Requires a true comparison with at least one value.

- `{value} {comparison_operator} ALL ({SELECT})` - Requires a true comparison with every value.

- `EXISTS ({SELECT})` / `NOT EXISTS (...)` - Checks for the presence or absence of matching rows.

> If a scalar `NOT IN` receives a list containing `NULL`, no row passes that condition. To check for the absence of matches, `NOT EXISTS` often expresses the intent more clearly.

### Combined Examples:

```sql
-- Salaries above the overall average.
SELECT primeiro, salario FROM Funcionario
WHERE salario > (SELECT AVG(salario) FROM Funcionario)
ORDER BY salario DESC;

-- Uses grouping as a source of rows.
SELECT R.dept_id, R.media
FROM (
    SELECT dept_id, ROUND(AVG(salario), 2) AS media
    FROM Funcionario GROUP BY dept_id
) R
WHERE R.media > 4000
ORDER BY R.dept_id;

-- Correlated subquery: department without employees.
SELECT D.dept_id, D.nome FROM Departamento D
WHERE NOT EXISTS (
    SELECT 1 FROM Funcionario F WHERE F.dept_id = D.dept_id
); -- 60, Gerência.

-- Salary greater than every salary in department 30.
SELECT primeiro, salario FROM Funcionario
WHERE salario > ALL (
    SELECT salario FROM Funcionario WHERE dept_id = 30
);
```

With non-null salaries in department 30, `> ANY` means exceeding the lowest, and `> ALL`, the highest. For an empty result, `ANY` is false and `ALL` is true; nulls also require care in this comparison.

## Set Operations:

Combine rows from queries with the same number of columns and compatible types in each position.

- `{SELECT} UNION {SELECT}` - Combines the results and removes duplicates.

- `{SELECT} UNION ALL {SELECT}` - Combines the results, preserving duplicates.

- `{SELECT} INTERSECT {SELECT}` - Keeps rows present in both results, without duplicates.

- `{SELECT} MINUS {SELECT}` - Keeps rows from the first result that are absent from the second, without duplicates.

```sql
SELECT mat FROM Funcionario WHERE dept_id = 10
UNION
SELECT mat FROM Funcionario WHERE dept_id = 30
ORDER BY mat; -- 1, 2, 4, 7 and 9.

SELECT dept_id FROM Departamento
INTERSECT
SELECT dept_id FROM Funcionario
ORDER BY dept_id; -- 10, 20, 30, 40 and 50.

SELECT dept_id FROM Departamento
MINUS
SELECT dept_id FROM Funcionario
ORDER BY dept_id; -- 60.
```

---

# 7. Views

A ***view*** is a stored query used as a virtual table. It works as a window onto the data: it can simplify queries and expose only the necessary rows and columns.

## Commands:

- `CREATE VIEW {name} AS {SELECT}` - Creates a view.

- `CREATE OR REPLACE VIEW {name} AS {SELECT}` - Creates or replaces its definition.

- `SELECT {expressions} FROM {view}` - Queries the view.

- `WITH CHECK OPTION` - At the end of the definition, prevents writes through the view that would leave the row outside its filter.

- `DROP VIEW {name}` - Removes the view, preserving the base tables.

Changes through an updatable view affect the base tables. Aggregations, set operations, and certain joins prevent or restrict direct updates.

## Integrated Example:

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
SELECT salario FROM Funcionario WHERE mat = 1; -- 4000 in the base table.
ROLLBACK;

-- Invalid case, to test separately:
-- UPDATE vw_Depto10 SET dept_id = 20 WHERE mat = 1;
-- WITH CHECK OPTION rejects moving out of department 10.
```

```sql
DROP VIEW vw_Depto10;
```

---

# 8. Triggers

A ***trigger*** runs automatically in response to an event. The examples use DML operations to validate dates, calculate a column, and block insertions.

## Structure and Events:

- `CREATE OR REPLACE TRIGGER {name} ...` - Creates or replaces the trigger.

- `BEFORE {events} ON {table}` / `AFTER ...` - Runs before or after the operation at the point defined by the trigger.

- `INSERT OR UPDATE OR DELETE` - DML events that can be selected or combined.

- `UPDATE OF {columns}` - Restricts the event to updates that mention those columns.

- `FOR EACH ROW` - Runs for each affected row; without the clause, runs once per statement.

- `DROP TRIGGER {name}` - Removes the trigger.

The PL/SQL body goes between `BEGIN` and `END`. Optional declarations go in `DECLARE`, before `BEGIN`; assignments use `:=`, and conditions use `IF ... THEN ... END IF`.

- `RAISE_APPLICATION_ERROR({code}, {message})` - Raises an application error; codes range from `-20000` to `-20999`.

`AFTER` does not mean after `COMMIT`: ordinary DML triggers participate in the same transaction as the command. Simple rules, such as required values and uniqueness, continue to be expressed directly through *constraints*.

## Row Values:

| Event | `:OLD` | `:NEW` |
|---|---|---|
| `INSERT` | Null fields. | New row. |
| `UPDATE` | Previous values. | Values after the update. |
| `DELETE` | Deleted row. | Null fields. |

These pseudorecords are used in row-level triggers. A `BEFORE INSERT OR UPDATE` trigger can modify `:NEW`; fields not changed by `UPDATE` already retain their values in `:NEW`.

## Validation and Automatic Calculation:

The trigger below rejects reversed dates and calculates complete days. Without an end date, the duration is null. The key of `Ocupa` allows only one record per employee/position pair.

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
VALUES (1, 1, DATE '2026-05-22', DATE '2026-06-21'); -- 30 days.

INSERT INTO Ocupa (codigo_funcionario, codigo_cargo, data_inicio, data_fim)
VALUES (2, 2, DATE '2026-05-11', DATE '2026-05-11'); -- Zero days.

SELECT F.nome, C.descricao, O.quantidade_dias
FROM Ocupa O
JOIN Funcionario F ON F.codigo = O.codigo_funcionario
JOIN Cargo C ON C.codigo = O.codigo_cargo
ORDER BY F.codigo;

UPDATE Ocupa SET data_fim = NULL
WHERE codigo_funcionario = 1 AND codigo_cargo = 1;
SELECT quantidade_dias FROM Ocupa
WHERE codigo_funcionario = 1 AND codigo_cargo = 1; -- NULL.

-- Invalid case, to test separately:
-- UPDATE Ocupa SET data_fim = DATE '2020-01-01'
-- WHERE codigo_funcionario = 1 AND codigo_cargo = 1;

ROLLBACK;
```

## Statement-Level Blocking:

Without `FOR EACH ROW`, the trigger acts on the command and has no individual row to access through `:NEW` or `:OLD`.

```sql
CREATE OR REPLACE TRIGGER trg_Trava_Funcionario
BEFORE INSERT ON Funcionario
BEGIN
    RAISE_APPLICATION_ERROR(-20002, 'Inserções temporariamente bloqueadas.');
END;
/
```

```sql
-- Invalid case while the trigger exists:
-- INSERT INTO Funcionario (codigo, nome) VALUES (4, 'Mariana');

DROP TRIGGER trg_Trava_Funcionario;

INSERT INTO Funcionario (codigo, nome) VALUES (4, 'Mariana'); -- Now it works.
ROLLBACK;
```

## Errors and Cleanup:

- `SHOW ERRORS TRIGGER {name}` - Displays compilation errors in SQL\*Plus; a client command, without `;`.
- `SELECT name, line, text FROM user_errors` - Queries errors in the current user's objects.

```sql
SELECT name, line, text FROM user_errors
WHERE type = 'TRIGGER' ORDER BY name, sequence;

DROP TRIGGER trg_Ocupa_Datas;
```

Dropping a table also removes its associated triggers. Cleanup for the tables in this scenario is in the appendix.

---

# 9. Administrative Tools

## Environment and Connection:

An **instance** brings together memory and processes that access the database. In a *multitenant* installation, the **CDB** contains pluggable databases, the **PDBs**, which share infrastructure. The **service** identifies the connection destination.

A *schema* groups a user's objects. `usuario.Funcionario` identifies that user's table; another user can have a table with the same name. The prefix selects the object but does not grant access to it.

- `sqlplus {user}@//{host}:{port}/{service}` - Opens a connection from the terminal, prompting for the password.

- `sqlplus sys@//{host}:{port}/{service} as sysdba` - Opens an administrative connection.

- `SHOW USER` / `SHOW CON_NAME` - Shows the current user or container in SQL\*Plus.

- `SET AUTOCOMMIT OFF` - Disables the client's automatic commits, preserving the effects of implicit commits in the database.

`SHOW` and `SET` above are client commands, without `;`. The examples use `localhost:1521/FREEPDB1`; adjust these values to your installation.

| Access | Usage |
| --------- | -------------------------------------------------------------------------- |
| Standard | Privileges granted to the user and enabled roles. |
| `SYSDBA` | Broad database administration. |
| `SYSOPER` | More restricted administrative operations, such as starting and shutting down the database. |

`SYSDBA` and `SYSOPER` are special administrative privileges. `SYS` requires an appropriate administrative connection; `SYSTEM` can use a standard connection with its privileges. Use a dedicated user for the examples.

## Users and Quotas:

- `CREATE USER {user} IDENTIFIED BY "{password}"` - Creates the user.

- `ALTER USER {user} IDENTIFIED BY "{password}"` - Changes the password, respecting privileges and the applicable policy.

- `ALTER USER {user} QUOTA {limit} ON {tablespace}` - Defines the space the user can allocate; `UNLIMITED` removes this quota limit.

- `ALTER USER {user} ACCOUNT LOCK` / `ACCOUNT UNLOCK` - Blocks new authentications or unlocks the account.

- `DROP USER {user} CASCADE` - Removes the user and their objects.

A *tablespace* organizes storage. Permission to create tables does not remove the need for the quota required to allocate them.

## Permissions:

- `GRANT {privileges} TO {user}` - Grants system privileges, such as `CREATE SESSION` or `CREATE TABLE`.

- `REVOKE {privileges} FROM {user}` - Revokes the specified privileges.

- `GRANT {operations} ON {object} TO {user}` - Grants access to an object, such as `SELECT`, `INSERT`, `UPDATE`, or `DELETE`.

- `REVOKE {operations} ON {object} FROM {user}` - Removes the specified grants on the object.

`ALL` can replace the list of object privileges grantable in that context; it does not mean complete database administration. Revoking `CREATE TABLE` does not delete existing tables.

### Preparing a User:

Run as an administrator connected to the **desired PDB**, with the `USERS` *tablespace* available. Then connect as `usuario` to prepare the scenario in the appendix.

```sql
CREATE USER usuario IDENTIFIED BY "Troque_Esta_Senha_2026"
DEFAULT TABLESPACE USERS QUOTA 50M ON USERS;

GRANT CREATE SESSION, CREATE TABLE, CREATE VIEW,
      CREATE SEQUENCE, CREATE PROCEDURE, CREATE TRIGGER TO usuario;
```

### Sharing a Table:

This block assumes that `usuario` has created `Funcionario` and that an `outro_usuario` account already exists in the same PDB.

```sql
-- Executed by the table owner.
GRANT SELECT, UPDATE ON Funcionario TO outro_usuario;
REVOKE UPDATE ON Funcionario FROM outro_usuario;
-- OUTRO_USUARIO retains SELECT on usuario.Funcionario.
```

## Administrative Queries:

| Source | Information |
|---|---|
| `USER_TABLES` | Current user's tables. |
| `USER_OBJECTS` | Current user's objects. |
| `ALL_USERS` | Users visible in the container. |
| `SESSION_PRIVS` | System privileges available in the session. |
| `DBA_USERS` | Users and account status. |
| `DBA_SYS_PRIVS` | System privilege grants. |

The `DBA_` views require appropriate access, not necessarily `SYSDBA`. A query of direct grants to a user does not automatically include permissions inherited from roles.

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

# Appendix A. Example Tables

Prepare a scenario, run its examples, and clean up at the end. The query and trigger scenarios reuse the name `Funcionario` with different structures; when using the same user, clean up the first before preparing the second.

## Chapters 1 - 7:

### Creation and Data:

```sql
-- Table creation:

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

-- Data Insertion:

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

### Cleanup:

```sql
DROP TABLE Funcionario;
DROP TABLE Departamento;
DROP TABLE Produto;
```

## Chapter 8:

`Ocupa` starts empty; records are inserted in the examples after the trigger is created.

### Creation and Data:

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

### Cleanup:

```sql
DROP TABLE Ocupa;
DROP TABLE Cargo;
DROP TABLE Funcionario;
```

---

# Sources

- BARROS, Evandrino Gomes. Course: Banco de Dados I (Databases I). Undergraduate program in Computer Engineering - Centro Federal de Educação Tecnológica de Minas Gerais (CEFET-MG), 2026.
- ORACLE. *SQL Language Reference*. Oracle Database 19c. [Place unknown]: Oracle, 2026. Available at: [https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/). Accessed: 2 Oct. 2026.
- ORACLE. *PL/SQL Language Reference*. Oracle Database 19c. [Place unknown]: Oracle, 2026. Available at: [https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/). Accessed: 2 Oct. 2026.
- ORACLE. *Database Administrator’s Guide*. Oracle Database 19c. [Place unknown]: Oracle, 2026. Available at: [https://docs.oracle.com/en/database/oracle/oracle-database/19/admin/](https://docs.oracle.com/en/database/oracle/oracle-database/19/admin/). Accessed: 2 Oct. 2026.
- ORACLE. *Multitenant Administrator’s Guide*. Oracle Database 19c. [Place unknown]: Oracle, 2025. Available at: [https://docs.oracle.com/en/database/oracle/oracle-database/19/multi/](https://docs.oracle.com/en/database/oracle/oracle-database/19/multi/). Accessed: 2 Oct. 2026.
- ORACLE. *SQL\*Plus User’s Guide and Reference*. Oracle Database 19c. [Place unknown]: Oracle, 2025. Available at: [https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/). Accessed: 2 Oct. 2026.
- JUNIATA COLLEGE. *Three Level Database Architecture*. [Place unknown]: Juniata College, [No date]. Available at: [https://jcsites.juniata.edu/faculty/rhodes/dbms/dbarch.htm](https://jcsites.juniata.edu/faculty/rhodes/dbms/dbarch.htm). Accessed: 2 Oct. 2026.
