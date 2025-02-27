---
title: "Search condition (Transact-SQL)"
description: A search condition is a combination of one or more predicates that use the logical operators AND, OR, and NOT.
author: VanMSFT
ms.author: vanto
ms.reviewer: randolphwest
ms.date: 07/09/2024
ms.service: sql
ms.subservice: t-sql
ms.topic: reference
ms.custom:
  - ignite-2024
f1_keywords:
  - "search"
  - "Search condition"
  - "condition"
helpviewer_keywords:
  - "OR operator [Transact-SQL]"
  - "CONTAINS predicate (Transact-SQL)"
  - "ESCAPE keyword"
  - "operators [Transact-SQL], search condition"
  - "search conditions [SQL Server]"
  - "WHERE clause, search conditions"
  - "ALL keyword"
  - "FREETEXT predicate (Transact-SQL)"
  - "EXISTS keyword"
  - "search conditions [SQL Server], about search conditions"
  - "NOT operator [Transact-SQL]"
  - "IS [NOT] DISTINCT FROM [Transact-SQL]"
  - "BETWEEN operator"
  - "SOME | ANY keyword"
  - "predicates [full-text search]"
  - "AND, about search conditions"
  - "logical operators [SQL Server], about search conditions"
  - "precedence [SQL Server], logical operators"
  - "logical operators [SQL Server], precedence"
  - "LIKE comparisons"
dev_langs:
  - "TSQL"
monikerRange: ">=aps-pdw-2016 || =azuresqldb-current || =azure-sqldw-latest || >=sql-server-2016 || >=sql-server-linux-2017 || =azuresqldb-mi-current || =fabric"
---
# Search condition (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw-fabricse-fabricdw-fabricsqldb](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw-fabricse-fabricdw-fabricsqldb.md)]

A combination of one or more predicates that use the logical operators `AND`, `OR`, and `NOT`.

:::image type="icon" source="../../includes/media/topic-link-icon.svg" border="false"::: [Transact-SQL syntax conventions](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## Syntax

Syntax for [!INCLUDE [ssnoversion-md](../../includes/ssnoversion-md.md)], [!INCLUDE [ssazure-sqldb](../../includes/ssazure-sqldb.md)], and [!INCLUDE [ssazuremi-md](../../includes/ssazuremi-md.md)].

```syntaxsql
<search_condition> ::=
    MATCH (<graph_search_pattern>) | <search_condition_without_match> | <search_condition> AND <search_condition>

<search_condition_without_match> ::=
    { [ NOT ] <predicate> | ( <search_condition_without_match> ) }
    [ { AND | OR } [ NOT ] { <predicate> | ( <search_condition_without_match> ) } ]
[ ...n ]

<predicate> ::=
    { expression { = | <> | != | > | >= | !> | < | <= | !< } expression
    | string_expression [ NOT ] LIKE string_expression
  [ ESCAPE 'escape_character' ]
    | expression [ NOT ] BETWEEN expression AND expression
    | expression IS [ NOT ] NULL
    | expression IS [ NOT ] DISTINCT FROM
    | CONTAINS
  ( { column | * } , '<contains_search_condition>' )
    | FREETEXT ( { column | * } , 'freetext_string' )
    | expression [ NOT ] IN ( subquery | expression [ , ...n ] )
    | expression { = | < > | != | > | >= | ! > | < | <= | ! < }
  { ALL | SOME | ANY } ( subquery )
    | EXISTS ( subquery )     }

<graph_search_pattern> ::=
    { <node_alias> {
                    { <-( <edge_alias> )- }
                    | { -( <edge_alias> )-> }
                    <node_alias>
                   }
    }

<node_alias> ::=
    node_table_name | node_table_alias

<edge_alias> ::=
    edge_table_name | edge_table_alias
```

Syntax for Azure Synapse Analytics and Parallel Data Warehouse.

```syntaxsql
< search_condition > ::=
    { [ NOT ] <predicate> | ( <search_condition> ) }
    [ { AND | OR } [ NOT ] { <predicate> | ( <search_condition> ) } ]
[ ...n ]

<predicate> ::=
    { expression { = | <> | != | > | >= | < | <= } expression
    | string_expression [ NOT ] LIKE string_expression
    | expression [ NOT ] BETWEEN expression AND expression
    | expression IS [ NOT ] NULL
    | expression [ NOT ] IN (subquery | expression [ , ...n ] )
    | expression [ NOT ] EXISTS (subquery)
    }
```

## Arguments

#### `<search_condition>`

Specifies the conditions for the rows returned in the result set for a `SELECT` statement, query expression, or subquery. For an `UPDATE` statement, specifies the rows to be updated. For a `DELETE` statement, specifies the rows to be deleted. There's no limit to the number of predicates that can be included in a [!INCLUDE [tsql](../../includes/tsql-md.md)] statement search condition.

#### `<graph_search_pattern>`

Specifies the graph match pattern. For more information about the arguments for this clause, see [MATCH](match-sql-graph.md)

#### NOT

Negates the Boolean expression specified by the predicate. For more information, see [NOT](../language-elements/not-transact-sql.md).

#### AND

Combines two conditions and evaluates to `TRUE` when both of the conditions are `TRUE`. For more information, see [AND](../language-elements/and-transact-sql.md).

#### OR

Combines two conditions and evaluates to `TRUE` when either condition is `TRUE`. For more information, see [OR](../language-elements/or-transact-sql.md).

#### `<predicate>`

An expression that returns `TRUE`, `FALSE`, or `UNKNOWN`. For more information, see [Predicates](predicates.md).

#### *expression*

Specifies a column name, a constant, a function, a variable, a scalar subquery, or any combination of column names, constants, and functions connected by an operator or operators, or a subquery. The expression can also contain the `CASE` expression.

Non-Unicode string constants and variables use the code page that corresponds to the default collation of the database. Code page conversions can occur when working with only non-Unicode character data and referencing the non-Unicode character data types **char**, **varchar**, and **text**. [!INCLUDE [ssNoVersion](../../includes/ssnoversion-md.md)] converts non-Unicode string constants and variables to the code page that corresponds to the collation of the referenced column or specified using `COLLATE`, if that code page is different than the code page that corresponds to the default collation of the database. Any characters not found in the new code page are translated to a similar character if a [best-fit mapping](https://www.unicode.org/Public/MAPPINGS/VENDORS/MICSFT/WindowsBestFit/) can be found, or else are converted to the default replacement character of `?`.  

When you work with multiple code pages, character constants can be prefixed with the uppercase letter `N`, and Unicode variables can be used, to avoid code page conversions.

#### `=` operator

The operator used to test the equality between two expressions.

#### `<>` operator

The operator used to test the condition of two expressions not being equal to each other.

#### `!=` operator

The operator used to test the condition of two expressions not being equal to each other.

#### `>` operator

The operator used to test the condition of one expression being greater than the other.

#### `>=` operator

The operator used to test the condition of one expression being greater than or equal to the other expression.

#### `!>` operator

The operator used to test the condition of one expression not being greater than the other expression.

#### `<` operator

The operator used to test the condition of one expression being less than the other.

#### `<=` operator

The operator used to test the condition of one expression being less than or equal to the other expression.

#### `!<` operator

The operator used to test the condition of one expression not being less than the other expression.

#### *string_expression*

A string of characters and wildcard characters.

#### [ NOT ] *LIKE*

Indicates that the subsequent character string is to be used with pattern matching. For more information, see [LIKE](../language-elements/like-transact-sql.md).

#### ESCAPE '*escape_ character*'

Allows for a wildcard character to be searched for in a character string instead of functioning as a wildcard. *escape_character* is the character that is put in front of the wildcard character to indicate this special use.

#### [ NOT ] *BETWEEN*

Specifies an inclusive range of values. Use `AND` to separate the starting and ending values. For more information, see [BETWEEN](../language-elements/between-transact-sql.md).

#### IS [ NOT ] NULL

Specifies a search for null values, or for values that aren't null, depending on the keywords used. An expression with a bitwise or arithmetic operator evaluates to `NULL` if any one of the operands is `NULL`.

#### IS [ NOT ] DISTINCT FROM

Compares the equality of two expressions and guarantees a true or false result, even if one or both operands are `NULL`. For more information, see [IS [NOT] DISTINCT FROM (Transact-SQL)](is-distinct-from-transact-sql.md).

#### CONTAINS

Searches columns that contain character-based data for precise or less precise (*fuzzy*) matches to single words and phrases, the proximity of words within a certain distance of one another, and weighted matches. This option can only be used with `SELECT` statements. For more information, see [CONTAINS](contains-transact-sql.md).

#### FREETEXT

Provides a simple form of natural language query by searching columns that contain character-based data for values that match the meaning instead of the exact words in the predicate. This option can only be used with `SELECT` statements. For more information, see [FREETEXT](freetext-transact-sql.md).

#### [ NOT ] IN

Specifies the search for an expression, based on whether the expression is included in or excluded from a list. The search expression can be a constant or a column name, and the list can be a set of constants or, more typically, a subquery. Enclose the list of values in parentheses. For more information, see [IN](../language-elements/in-transact-sql.md).

#### *subquery*

Can be considered a restricted `SELECT` statement and is similar to `<query_expression>` in the `SELECT` statement. The `ORDER BY` clause and the `INTO` keyword aren't allowed. For more information, see [SELECT](select-transact-sql.md).

#### ALL

Used with a comparison operator and a subquery. Returns `TRUE` for `<predicate>` when all values retrieved for the subquery satisfy the comparison operation, or `FALSE` when not all values satisfy the comparison or when the subquery returns no rows to the outer statement. For more information, see [ALL](../language-elements/all-transact-sql.md).

#### { SOME | ANY }

Used with a comparison operator and a subquery. Returns `TRUE` for `<predicate>` when any value retrieved for the subquery satisfies the comparison operation, or `FALSE` when no values in the subquery satisfy the comparison or when the subquery returns no rows to the outer statement. Otherwise, the expression is `UNKNOWN`. For more information, see [SOME &#124; ANY](../language-elements/some-any-transact-sql.md).

#### EXISTS

Used with a subquery to test for the existence of rows returned by the subquery. For more information, see [EXISTS](../language-elements/exists-transact-sql.md).

## Remarks

The order of precedence for the logical operators is `NOT` (highest), followed by `AND`, followed by `OR`. Parentheses can be used to override this precedence in a search condition. The order of evaluation of logical operators can vary depending on choices made by the query optimizer. For more information about how the logical operators operate on logic values, see [AND](../language-elements/and-transact-sql.md), [OR](../language-elements/or-transact-sql.md), and [NOT](../language-elements/not-transact-sql.md).

## Examples

[!INCLUDE [article-uses-adventureworks](../../includes/article-uses-adventureworks.md)]

### A. Use WHERE with LIKE and ESCAPE syntax

The following example searches for the rows in which the `LargePhotoFileName` column has the characters `green_`, and uses the `ESCAPE` option because `_` is a wildcard character. If you don't specify the `ESCAPE` option, the query searches for any description values that contain the word `green` followed by any single character other than the `_` character.

```sql
USE AdventureWorks2022;
GO
SELECT *
FROM Production.ProductPhoto
WHERE LargePhotoFileName LIKE '%greena_%' ESCAPE 'a';
```

### B. Use WHERE and LIKE syntax with Unicode data

The following example uses the `WHERE` clause to retrieve the mailing address for any company that is outside the United States (`US`) and in a city whose name starts with `Pa`.

```sql
USE AdventureWorks2022;
GO

SELECT AddressLine1,
    AddressLine2,
    City,
    PostalCode,
    CountryRegionCode
FROM Person.Address AS a
INNER JOIN Person.StateProvince AS s
    ON a.StateProvinceID = s.StateProvinceID
WHERE CountryRegionCode NOT IN ('US')
    AND City LIKE N'Pa%';
```

## Examples: [!INCLUDE [ssazuresynapse-md](../../includes/ssazuresynapse-md.md)] and [!INCLUDE [ssPDW](../../includes/sspdw-md.md)]

### C. Use WHERE with LIKE

The following example searches for the rows in which the `LastName` column has the characters `and`.

```sql
-- Uses AdventureWorks

SELECT EmployeeKey,
    LastName
FROM DimEmployee
WHERE LastName LIKE '%and%';
```

### D. Use WHERE and LIKE syntax with Unicode data

The following example uses the `WHERE` clause to perform a Unicode search on the `LastName` column.

```sql
-- Uses AdventureWorks

SELECT EmployeeKey,
    LastName
FROM DimEmployee
WHERE LastName LIKE N'%and%';
```

## Related content

- [Aggregate Functions (Transact-SQL)](../functions/aggregate-functions-transact-sql.md)
- [CASE (Transact-SQL)](../language-elements/case-transact-sql.md)
- [CONTAINSTABLE (Transact-SQL)](../../relational-databases/system-functions/containstable-transact-sql.md)
- [Cursors (Transact-SQL)](../language-elements/cursors-transact-sql.md)
- [DELETE (Transact-SQL)](../statements/delete-transact-sql.md)
- [Expressions (Transact-SQL)](../language-elements/expressions-transact-sql.md)
- [FREETEXTTABLE (Transact-SQL)](../../relational-databases/system-functions/freetexttable-transact-sql.md)
- [FROM clause plus JOIN, APPLY, PIVOT (Transact-SQL)](from-transact-sql.md)
- [Operators (Transact-SQL)](../language-elements/operators-transact-sql.md)
- [UPDATE (Transact-SQL)](update-transact-sql.md)
