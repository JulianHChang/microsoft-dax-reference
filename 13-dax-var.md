# VAR

The VAR keyword introduces variables in an expression.
The syntax after VAR defines a variable, which can be consumed in following VAR statements or within the mandatory RETURN statement following the declaration of one or more variables.
If VAR is used in the DEFINE section of an EVALUATE statement, it can be used in any expression of the statement and the RETURN keyword cannot be used.
<hr style="border: none; border-top: 3px solid black;">


Stores intermediate calculations for better performance and readability.
```DAX
Profit Ratio = 
VAR TotalRevenue = SUM(Sales[Revenue])
VAR TotalCost = SUM(Sales[Cost])
RETURN DIVIDE(TotalRevenue - TotalCost, TotalRevenue)
```
<hr style="border: none; border-top: 3px solid black;">
Examples


``` DAX
VAR <name> = <expression>
[VAR <name2> = <expression2> [...]]
RETURN <result_expression>

```
<hr style="border: none; border-top: 3px solid black;">
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

Use variables to improve your DAX formulas

As a data modeler, writing and debugging some DAX calculations can be challenging. It's common that complex calculation requirements often involve writing compound or complex expressions. Compound expressions can involve the use of many nested functions, and possibly the reuse of expression logic.

Using variables in your DAX formulas can help you write more complex and efficient calculations. Variables can improve performance, reliability, readability, and reduce complexity.

In this article, we'll demonstrate the first three benefits by using an example measure for year-over-year (YoY) sales growth. (The formula for YoY sales growth is period sales, minus sales for the same period last year, divided by sales for the same period last year.)

Let's start with the following measure definition.

```DAX

Copy
Sales YoY Growth % =
DIVIDE(
    ([Sales] - CALCULATE([Sales], PARALLELPERIOD('Date'[Date], -12, MONTH))),
    CALCULATE([Sales], PARALLELPERIOD('Date'[Date], -12, MONTH))
)
```
The measure produces the correct result, yet let's now see how it can be improved.

Improve performance
Notice that the formula repeats the expression that calculates "same period last year". This formula is inefficient, as it requires Power BI to evaluate the same expression twice. The measure definition can be made more efficient by using a variable, VAR.

The following measure definition represents an improvement. It uses an expression to assign the "same period last year" result to a variable named SalesPriorYear. The variable is then used twice in the RETURN expression.

```DAX

Copy
Sales YoY Growth % =
VAR SalesPriorYear =
    CALCULATE([Sales], PARALLELPERIOD('Date'[Date], -12, MONTH))
RETURN
    DIVIDE(([Sales] - SalesPriorYear), SalesPriorYear)
```
The measure continues to produce the correct result, and does so in about half the query time.

Improve readability
In the previous measure definition, notice how the choice of variable name makes the RETURN expression simpler to understand. The expression is short and self-describing.

Simplify debugging
Variables can also help you debug a formula. To test an expression assigned to a variable, you temporarily rewrite the RETURN expression to output the variable.

The following measure definition returns only the SalesPriorYear variable. Notice how it comments-out the intended RETURN expression. This technique allows you to easily revert it back once your debugging is complete.

```DAX

Copy
Sales YoY Growth % =
VAR SalesPriorYear =
    CALCULATE([Sales], PARALLELPERIOD('Date'[Date], -12, MONTH))
RETURN
    --DIVIDE(([Sales] - SalesPriorYear), SalesPriorYear)
    SalesPriorYear
```  
Reduce complexity
In earlier versions of DAX, variables were not yet supported. Complex expressions that introduced new filter contexts were required to use the EARLIER or EARLIEST DAX functions to reference outer filter contexts. Unfortunately, data modelers found these functions difficult to understand and use.

Variables are always evaluated outside the filters your RETURN expression applies. For this reason, when you use a variable within a modified filter context, it achieves the same result as the EARLIEST function. The use of the EARLIER or EARLIEST functions can therefore be avoided. It means you can now write formulas that are less complex, and that are easier to understand.

Consider the following calculated column definition added to the Subcategory table. It evaluates a rank for each product subcategory based on the Subcategory Sales column values.

```DAX

Copy
Subcategory Sales Rank =
COUNTROWS(
    FILTER(
        Subcategory,
        EARLIER(Subcategory[Subcategory Sales]) < Subcategory[Subcategory Sales]
    )
) + 1
```
The EARLIER function is used to refer to the Subcategory Sales column value in the current row context.

The calculated column definition can be improved by using a variable instead of the EARLIER function. The CurrentSubcategorySales variable stores the Subcategory Sales column value in the current row context, and the RETURN expression uses it within a modified filter context.

```DAX

Subcategory Sales Rank =
VAR CurrentSubcategorySales = Subcategory[Subcategory Sales]
RETURN
    COUNTROWS(
        FILTER(
            Subcategory,
            CurrentSubcategorySales < Subcategory[Subcategory Sales]
        )
    ) + 1
```