
This is a list of the most common use Iterator Functions: SUMX, AVERAGEX, MAXX/MINXX, RANKX, COUNTX.

<hr style="border: none; border-top: 3px solid black;">

# X-Functions (Iterator Functions) - e.g., SUMX
*   **Purpose:** These functions **iterate** over a table and evaluate an expression for each row, then sum (or average, count, etc.) the results.
*   **Why it's important:** They are fundamental for row-context calculations. `SUMX` is the most common, but `AVERAGEX`, `COUNTX`, `RANKX` are equally vital.
*   **Example:** `Total Sales (SUMX) = SUMX(Sales, Sales[Quantity] * Sales[Unit Price])` (This calculates a total by multiplying two columns for each row before summing).


<hr style="border: none; border-top: 3px solid black;">


# RANKX

Ranks values over a given table and expression.

**Examples**

```DAX
-- Rank customers by sales in current filter context
Customer Rank by Sales :=
RANKX ( ALL ( Customers[CustomerID] ), [Total Sales], , DESC, DENSE )

-- Rank products within each Category (respect slicers on Category)
Product Rank in Category :=
RANKX (
    ALL ( Products[ProductID] ),
    [Total Sales],
    ,
    DESC,
    DENSE
)

-- Rank regions by profit
Region Rank by Profit :=
RANKX ( ALL ( Customers[Region] ), [Profit], , DESC )
```


<hr style="border: none; border-top: 3px solid black;">

# AVERAGEX

Like SUMX but averages an expression (useful for weighted/logical avgs).

**Examples**

```DAX
-- Average order value (AOV) over distinct orders
AOV :=
AVERAGEX ( VALUES ( Sales[OrderID] ), CALCULATE ( [Total Sales] ) )

-- Average monthly sales across visible months
Avg Monthly Sales :=
AVERAGEX ( VALUES ( Calendar[Month] ), [Total Sales] )

-- Average category profit margin
Avg Margin % by Category :=
AVERAGEX (
    VALUES ( Products[Category] ),
    DIVIDE ( [Total Sales] - [Total Cost], [Total Sales] )
)
```


<hr style="border: none; border-top: 3px solid black;">

[AVERAGEX:](https://docs.microsoft.com/en-us/dax/averagex-function-dax)  
[COUNTAX:](https://docs.microsoft.com/en-us/dax/countax-function-dax)  
[COUNTX:](https://docs.microsoft.com/en-us/dax/countx-function-dax)  
[GEOMEANX:](https://docs.microsoft.com/en-us/dax/geomeanx-function-dax)  
[MAXX:](https://docs.microsoft.com/en-us/dax/maxx-function-dax)  
[MEDIANX:](https://docs.microsoft.com/en-us/dax/meadianx-function-dax)  
[MINX:](https://docs.microsoft.com/en-us/dax/minx-function-dax)  
[PRODUCTX:](https://docs.microsoft.com/en-us/dax/productx-function-dax)  
[STDEVX.P:](https://docs.microsoft.com/en-us/dax/stdevx-p-function-dax)  
[STDEVX.S:](https://docs.microsoft.com/en-us/dax/stdevx-s-function-dax)  
[SUMX:](https://docs.microsoft.com/en-us/dax/sumx-function-dax)  
[VAR.P:](https://docs.microsoft.com/en-us/dax/varx-p-function-dax)  
[VAR.S:](https://docs.microsoft.com/en-us/dax/varx-s-function-dax)  
[Optimizing nested iterators in DAX:](https://www.sqlbi.com/articles/optimizing-nested-iterators-in-dax/)  


<hr style="border: none; border-top: 3px solid black;">

# COUNTX

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Counts the number of rows that contain a non-blank value or an expression that evaluates to a non-blank value, when evaluating an expression over a table.

## Syntax

DAX Copy

```
COUNTX(<table>,<expression>)
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| table | The table containing the rows to be counted. |
| expression | An expression that returns the set of values that contains the values you want to count. |

## Return value

An integer.

## Remarks

-   The COUNTX function takes two arguments. The first argument must always be a table, or any expression that returns a table. The second argument is the column or expression that is searched by COUNTX.
    
-   The COUNTX function counts only values, dates, or strings. If the function finds no rows to count, it returns a blank.
    
-   If you want to count logical values, use the COUNTAX function.
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.


<hr style="border: none; border-top: 3px solid black;">

# RANKX

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Returns the ranking of a number in a list of numbers for each row in the `table` argument.

## Syntax

DAX Copy

```
RANKX(<table>, <expression>[, <value>[, <order>[, <ties>]]])
```

### Parameters

`table` Any DAX expression that returns a table of data over which the expression is evaluated.

`expression` Any DAX expression that returns a single scalar value. The expression is evaluated for each row of `table`, to generate all possible values for ranking. See the remarks section to understand the function behavior when `expression` evaluates to BLANK.

`value` (Optional) Any DAX expression that returns a single scalar value whose rank is to be found. See the remarks section to understand the function's behavior when `value` is not found in the expression.

When the `value` parameter is omitted, the value of expression at the current row is used instead.

`order` (Optional) A value that specifies how to rank `value`, low to high or high to low:

Expand table

| value | alternate value | Description |
| --- | --- | --- |
| 0 (zero) | FALSE | Ranks in descending order of values of expression. If value is equal to the highest number in expression then RANKX returns 1.This is the default value when order parameter is omitted. |
| 1 | TRUE | Ranks in ascending order of expression. If value is equal to the lowest number in expression then RANKX returns 1. |

`ties` (Optional) An enumeration that defines how to determine ranking when there are ties.

Expand table

| enumeration | Description |
| --- | --- |
| Skip | The next rank value, after a tie, is the rank value of the tie plus the count of tied values. For example if five (5) values are tied with a rank of 11 then the next value will receive a rank of 16 (11 + 5).This is the default value when ties parameter is omitted. |
| Dense | The next rank value, after a tie, is the next rank value. For example if five (5) values are tied with a rank of 11 then the next value will receive a rank of 12. |

## Return value

The rank number of `value` among all possible values of `expression` evaluated for all rows of `table` numbers.

## Remarks

-   If `expression` or `value` evaluates to BLANK it is treated as a 0 (zero) for all expressions that result in a number, or as an empty text for all text expressions.
    
-   If `value` is not among all possible values of `expression` then RANKX temporarily adds `value` to the values from `expression` and re-evaluates RANKX to determine the proper rank of `value`.
    
-   Optional arguments might be skipped by placing an empty comma (,) in the argument list, such as `RANKX(Inventory, [InventoryCost],,,"Dense")`.
    
-   Equality related comparisons (`=`, `<`, `>`, `>=` and `<=`) between values with the _Decimal Number_ data type can potentially return unexpected results when using RANKX function. Incorrect results can occur because values with _Decimal Number_ data type are stored as IEEE Standard 754 floating point numbers and have inherent limitations in their precision. To avoid unexpected results, change the data type to _Fixed Decimal Number_ or do a forced rounding using [ROUND](/en-us/dax/round-function-dax).
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.

<hr style="border: none; border-top: 3px solid black;">

# MAXX

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Returns the highest value that results from evaluating an expression for each row of a table.

## Syntax

DAX Copy

```
MAXX(<table>,<expression>,[<variant>])  
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| table | The table containing the rows for which the expression will be evaluated. |
| expression | The expression to be evaluated for each row of the table. |
| variant | (Optional) If TRUE, and if there are variant or mixed value types, the highest value based on ORDER BY DESC is returned. |

## Return value

The highest value.

## Remarks

-   The `table` argument to the MAXX function can be a table name or an expression that evaluates to a table. The second argument indicates the expression to be evaluated for each row of the table.
    
-   Of the values to evaluate, only the following are counted:
    
    -   Numbers
    -   Texts
    -   Dates
-   Blank values are skipped. `TRUE`/`FALSE` values are not supported.
    
-   If the expression has variant or mixed value types such as text and number, then by default MAXX considers only numbers. If `<variant> =` TRUE\`\`, the maximum value is returned.
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.

<hr style="border: none; border-top: 3px solid black;">

# AVERAGEX
**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Calculates the average (arithmetic mean) of a set of expressions evaluated over a table.

## Syntax

DAX Copy

```
AVERAGEX(<table>,<expression>)
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| table | Name of a table, or an expression that specifies the table over which the aggregation can be performed. |
| expression | An expression with a scalar result, which will be evaluated for each row of the table in the first argument. |

## Return value

A decimal number.

## Remarks

-   The AVERAGEX function enables you to evaluate expressions for each row of a table, and then take the resulting set of values and calculate its arithmetic mean. Therefore, the function takes a table as its first argument, and an expression as the second argument.
    
-   In all other respects, AVERAGEX follows the same rules as AVERAGE. You cannot include non-numeric or null cells. Both the table and expression arguments are required.
    
-   When there are no rows to aggregate, the function returns a blank. When there are rows, but none of them meet the specified criteria, then the function returns 0.
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.


<hr style="border: none; border-top: 3px solid black;">
<hr style="border: none; border-top: 3px solid black;">
