# SUM / SUMX   
- **SUM**: Aggregates a single column.  
- **SUMX**: Iterates over a table to sum an expression (e.g., `Price * Quantity`).  
**Why it matters**: `SUMX` handles row-by-row calculations; `SUM` is for simple aggregation.  
**Example**:  
```dax
Total Revenue = SUMX(Sales, Sales[Price] * Sales[Quantity])
```


| **SUM** | Aggregation | Sums all the numbers in a column. |
|---|-------|---------|
| **SUMX** | Iterator | Evaluates an expression for each row of a table and then sums the resulting values. Essential for row-by-row calculations before aggregation (like calculating total revenue as Quantity * Price). |

<hr style="border: none; border-top: 3px solid black;">

# SUMX

Row-by-row iteration over a table; sums an expression.

**Examples**

```DAX
-- Margin by row then sum (robust when row granularity matters)
Total Margin (Row) :=
SUMX ( Sales, Sales[SalesAmount] - Sales[Cost] )

-- Weighted average price: SUM(Price*Qty)/SUM(Qty)
Avg Price (Weighted) :=
DIVIDE (
    SUMX ( Sales, Sales[SalesAmount] ),
    SUM ( Sales[Quantity] )
)

-- Simulate a “virtual table” then sum
Sales (Top 100 Orders by Amount) :=
VAR t =
    TOPN ( 100, Sales, Sales[SalesAmount], DESC )
RETURN
SUMX ( t, Sales[SalesAmount] )
```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">
Examples

``` DAX
--  SUM is the short version of SUMX, when used with one column only
--  SUMX is required to evaluate formulas, instead of columns
DEFINE
    MEASURE Sales[# Quantity 1] = SUM ( Sales[Quantity] )
    MEASURE Sales[# Quantity 2] = SUMX ( Sales, Sales[Quantity] )
    MEASURE Sales[Sales Amount] =
        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )
EVALUATE
SUMMARIZECOLUMNS (
    'Product'[Color],
    "Quantity 1", [# Quantity 1],
    "Quantity 2", [# Quantity 2],
    "Sales Amount", [Sales Amount]
)
```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">
Examples

``` DAX
--  SUMX is needed to iterate the content of a variable,
--  indeed SUM works only with columns in the model
DEFINE
    MEASURE Sales[Sales Amount] =
        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )
    MEASURE Sales[SUM Monthly Sales] =
        VAR MonthlySales =
            ADDCOLUMNS (
                DISTINCT ( 'Date'[Calendar Year Month] ),
                "@MonthlySales", [Sales Amount]
            )
        VAR FilteredSales =
            FILTER ( MonthlySales, [@MonthlySales] > 10000 )
        VAR Result =
            -- Iterator required to aggregate the @MonthlySales column       
            SUMX ( FilteredSales, [@MonthlySales] )
        RETURN
            Result
EVALUATE
SUMMARIZECOLUMNS (
    'Product'[Color],
    "SUM Monthly Sales", [SUM Monthly Sales]
)
```

<hr style="border: none; border-top: 3px solid black;">

# SUM

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Adds all the numbers in a column.

## Syntax

DAX Copy

```
SUM(<column>)
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| column | The column that contains the numbers to sum. |

## Return value

A decimal number.

## Remarks

If you want to filter the values that you are summing, you can use the SUMX function and specify an expression to sum over.

<hr style="border: none; border-top: 3px solid black;">

# SUMX

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Returns the sum of an expression evaluated for each row in a table.

## Syntax

DAX Copy

```
SUMX(<table>, <expression>)
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| table | The table containing the rows for which the expression will be evaluated. |
| expression | The expression to be evaluated for each row of the table. |

## Return value

A decimal number.

## Remarks

-   The SUMX function takes as its first argument a table, or an expression that returns a table. The second argument is a column that contains the numbers you want to sum, or an expression that evaluates to a column.
    
-   The SUMX is an [iterator function](dax-glossary#iterator-function).
    
-   Only the numbers in the column are counted. Blanks, logical values, and text are ignored.
    
-   For more complex examples of SUMX in formulas, see [ALL](all-function-dax) and [CALCULATETABLE](calculatetable-function-dax).
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.
    

<hr style="border: none; border-top: 3px solid black;">