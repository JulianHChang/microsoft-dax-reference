# Statements
## In this category


| Statement | Description |
| --- | --- |
| DEFINE | (Keyword) Introduces one or more entity definitions that can be applied to one or more EVALUATE statements. |
| EVALUATE | (Keyword) Introduces a statement containing a table expression required to execute a DAX query. |
| MEASURE | (Keyword) Introduces a measure definition that can be used in one or more EVALUATE statements in a query. |
| ORDER BY | (Keyword) Introduces a statement that defines the sort order of query results returned by an EVALUATE statement. |
| START AT | (Keyword) Introduces a statement that defines the starting value at which the query results of an ORDER BY statement are returned. |
| VAR | (Keyword) Stores the result of an expression as a named variable, which can then be passed as an argument to other measure expressions. |

* * *


&nbsp;
<hr style="border: none; border-top: 3px solid black;">

# DEFINE  
**Examples**

``` DAX
DEFINE
    VAR LastSalesDate =
        MAX ( Sales[Order Date] )
EVALUATE
CALCULATETABLE (
    SUMMARIZECOLUMNS (
        'Product'[Product Name],
        "Balance", CALCULATE (
            [Amount],
            'Date'[Date] = LastSalesDate
        )
    )
)
 
EVALUATE
CALCULATETABLE (
    SUMMARIZECOLUMNS (
        Store[Store Name],
        "Balance", CALCULATE (
            [Amount],
            'Date'[Date] = LastSalesDate
        )
    )
)
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">

# EVALUATE
**Examples**

``` DAX
-- Definition section
[DEFINE {  MEASURE <tableName>[<name>] = <expression> }
        {  COLUMN <tableName>[<name>] = <expression> }
        {  TABLE <tableName> = <expression> }
        {  VAR <name> = <expression>}]
-- Query expression
EVALUATE <table> 
-- Result modifiers
[ORDER BY {<expression> [{ASC | DESC}]}[, …] 
[START AT {<value>|<parameter>} [, …]]] 
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">

# EVALUATE
**Examples**

``` DAX
DEFINE
    VAR MinimumAmount = 2000000
    VAR MaximumAmount = 8000000
EVALUATE
FILTER (
    ADDCOLUMNS (
        SUMMARIZE ( Sales, 'Product'[Category] ),
        "CategoryAmount", [Sales Amount]
    ),
    AND (
        [CategoryAmount] >= MinimumAmount,
        [CategoryAmount] <= MaximumAmount
    )
)
ORDER BY [CategoryAmount]
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">

# FUNCTION
**Examples**

``` DAX
DEFINE
FUNCTION SumTwoNumbers = (
    a : INT64 VAL,
    b : INT64 VAL
) => a + b
 
EVALUATE
{
    SumTwoNumbers( 3, 2 )
}
 
-- Result is 5
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">

# MEASURE
**Examples**

``` DAX
DEFINE
    MEASURE Sales[Sales Amount] = SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )
    MEASURE Sales[Total Cost] = SUMX ( Sales, Sales[Quantity] * Sales[Unit Cost] )
    MEASURE Sales[Margin] = [Sales Amount] - [Total Cost]
EVALUATE
SUMMARIZECOLUMNS (
    Customer[CountryRegion],
    "Sales Amount", [Sales Amount],
    "Margin", [Margin]
)
```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">

# ORDER BY  
**Examples**

``` DAX
EVALUATE
ALL (
    'Product'[Brand],
    'Product'[Color]
)
ORDER BY
    'Product'[Brand] ASC,
    'Product'[Color] DESC
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">

# START AT
**Examples**

``` DAX
EVALUATE
ALL ( 'Product'[Brand], 'Product'[Color] )
ORDER BY 'Product'[Brand], 'Product'[Color]
START AT "Proseware", "Red"
```
# DEFINE

Introduces a statement with one or more entity definitions that can be applied to one or more EVALUATE statements of a [DAX query](dax-queries).

## Syntax

DAX Copy

```
[DEFINE 
    (
     (COLUMN <table name>[<column name>] = <scalar expression>) |
     (FUNCTION <function name> = ([parameter name]: [parameter type], ...) => <function body>) |
     (MEASURE <table name>[<measure name>] = <scalar expression>) | 
     (TABLE <table name> = <virtual table definition>) | 
     (VAR <var name> = <table or scalar expression>) |
    ) + 
]

(EVALUATE <table expression>) +
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| Entity | COLUMN1, FUNCTION, MEASURE, TABLE1, or VAR. |
| name | The name of a column, function, measure, table, or var definition. It cannot be an expression. The name does not have to be unique. The name exists only for the duration of the query. |
| expression | Any DAX expression that returns a table or scalar value. The expression can use any of the defined entities. If there is a need to convert a scalar expression into a table expression, wrap the expression inside a table constructor with curly braces {}, or use the ROW() function to return a single row table. |
| parameter type, parameter name, function body | See FUNCTION statement. |

\[1\] **Caution:** Query scoped TABLE and COLUMN definitions are meant for internal use only. While you can define TABLE and COLUMN expressions for a query without syntax error, they may produce runtime errors and are not recommended.

## Remarks

-   A DAX query can have multiple EVALUATE statements, but can have only one DEFINE statement. Definitions in the DEFINE statement can apply to any EVALUATE statements in the query.
    
-   At least one definition is required in a DEFINE statement.
    
-   Measure definitions for a query override model measures of the same name.
    
-   VAR names have unique restrictions. To learn more, see [VAR - Parameters](var-dax#parameters).
    
-   To learn more about how a DEFINE statement is used, see [DAX queries](dax-queries).
    
-   To learn more about virtual column, see [Virtual Column](virtual-column-statement-dax)
    
-   To learn more about virtual table, see [Virtual Table](virtual-table-statement-dax)
    
-   To learn more about DAX user defined functions, see [DAX User Defined Functions](function-statement-dax)

<hr style="border: none; border-top: 3px solid black;">

# EVALUATE

Introduces a statement containing a table expression required in a [DAX query](dax-queries).

## Syntax

DAX Copy

```
EVALUATE <table>
```

## Parameters

Expand table

| Term | Definition |
| --- | --- |
| table | A table expression |

## Return value

The result of a table expression.

## Remarks

-   A DAX query can contain multiple EVALUATE statements.
    
-   To learn more about how EVALUATE statements are used, see [DAX queries](dax-queries).
    
<hr style="border: none; border-top: 3px solid black;">


# MEASURE

Introduces a measure definition in a DEFINE statement of a [DAX query](dax-queries).

## Syntax

DAX Copy

```
[DEFINE 
    (
      MEASURE <table name>[<measure name>] = <scalar expression>
    ) + 
]

(EVALUATE <table expression>) +
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| table name | The name of a table containing the measure. |
| measure name | The name of the measure. It cannot be an expression. The name does not have to be unique. The name exists only for the duration of the query. |
| scalar expression | A DAX expression that returns a scalar value. |

## Return value

The calculated result of the measure expression.

## Remarks

-   Measure definitions for a query override model measures of the same name for the duration of the query. They will not affect the model measure.
    
-   The measure expression can be used with any other expression in the same query.
    
-   To learn more about how MEASURE statements are used, see [DAX queries](dax-queries).


<hr style="border: none; border-top: 3px solid black;">

# FUNCTION

Introduces a function definition in a DEFINE statement of a [DAX query](dax-queries).

## Syntax

DAX Copy

```
[DEFINE 
    (
      FUNCTION <function name> = ([parameter name] : [parameter type] [parameter subtype] [parameter passing mode], ...) => <function body>
    ) + 
]

(EVALUATE <table expression>) +
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| function name | The name of a function. |
| parameter name | The name of the parameter. This cannot be a reserved keyword such as measure. |
| parameter type | anyval, scalar, table or anyref. Anyval is an abstract type for scalar or table. Anyref is an abstract type for all references. |
| parameter subtype | applies only to parameter type = scalar. Can be one of the following: boolean, datetime, decimal, double, int64, numeric, string, variant. |
| parameter passing mode | val (eargerly evaluated) or expr (lazily evaluated). |
| function body | A DAX expression for the function. |

## Return value

The calculated result of the function body.

## Remarks

-   To learn more about DAX User Defined Functions, see [DAX User Defined Functions](best-practices/dax-user-defined-functions).
-   To learn more about how FUNCTION statements are used, see [DAX queries](dax-queries).


<hr style="border: none; border-top: 3px solid black;">


# ORDER BY

Introduces a statement that defines sort order of query results returned by an EVALUATE statement in a [DAX query](dax-queries).

## Syntax

DAX Copy

```
[ORDER BY {<expression> [{ASC | DESC}]}[, …]]
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| expression | Any DAX expression that returns a single scalar value. |
| ASC | (default) Ascending sort order. |
| DESC | Descending sort order. |

## Return value

The result of an EVALUATE statement in ascending (ASC) or descending (DESC) order.

## Remarks

To learn more about how ORDER BY statements are used, see [DAX queries](dax-queries).



<hr style="border: none; border-top: 3px solid black;">

# START AT

Introduces a statement that defines the starting value at which the query results of an ORDER BY clause in an EVALUATE statement in a [DAX query](dax-queries) are returned.

## Syntax

DAX Copy

```
[START AT {<value>|<parameter>} [, …]]
```

## Parameters

Expand table

| Term | Definition |
| --- | --- |
| value | A constant value. Cannot be an expression. |
| parameter | The name of a parameter in an XMLA statement prefixed with an @ character. |

## Remarks

-   START AT arguments have a one-to-one correspondence with the columns in the ORDER BY statement. There can be as many arguments in the START AT statement as there are in the ORDER BY statement, but not more. The first argument in the START AT statement defines the starting value in column 1 of the ORDER BY columns. The second argument in the START AT statement defines the starting value in column 2 of the ORDER BY columns within the rows that meet the first value for column 1.
    
-   To learn more about how START AT statements are used, see [DAX queries](dax-queries).


<hr style="border: none; border-top: 3px solid black;">

# VAR

Stores the result of an expression as a named variable, which can then be passed as an argument to other measure expressions. Once resultant values have been calculated for a variable expression, those values do not change, even if the variable is referenced in another expression.

## Syntax

DAX Copy

```
VAR <name> = <expression>
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| name | The name of the variable (identifier).Delimiters are not supported. For example, 'varName' or [varName] will result in an error.Supported character set: a-z, A-Z, 0-9.0-9 are not valid as first character.__ (double underscore) is allowed as a prefix to the identifier name.No other special characters are supported.Reserved keywords not allowed.Names of existing tables are not allowed.Empty spaces are not allowed. |
| expression | A DAX expression which returns a scalar or table value. |

## Return value

A named variable containing the result of the expression argument.

## Remarks

-   An expression passed as an argument to VAR can contain another VAR declaration.
    
-   When referencing a variable:
    
    -   Measures cannot refer to variables defined outside the measure expression, but can refer to functional scope variables defined within the expression.
    -   Variables can refer to measures.
    -   Variables can refer to previously defined variables.
    -   Columns in table variables cannot be referenced via TableName\[ColumnName\] syntax.
-   For best practices when using VAR, see [Use variables to improve your DAX formulas](best-practices/dax-variables).
    
-   To learn more about how VAR is used within a DAX Query, see [DAX queries](dax-queries).


<hr style="border: none; border-top: 3px solid black;">




<hr style="border: none; border-top: 3px solid black;">



<hr style="border: none; border-top: 3px solid black;">


