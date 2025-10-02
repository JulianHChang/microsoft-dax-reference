# CALCULATETABLE

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)


Evaluates a table expression in a modified filter context.

Note

There's also the [CALCULATE](calculate-function-dax) function. It performs exactly the same functionality, except it modifies the [filter context](dax-overview#filter-context) applied to an expression that returns a _scalar value_.

## Syntax

DAX Copy

```
CALCULATETABLE(<expression>[, <filter1> [, <filter2> [, …]]])
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| expression | The table expression to be evaluated. |
| filter1, filter2,… | (Optional) Boolean expressions or table expressions that defines filters, or filter modifier functions. |

The expression used as the first parameter must be a model table or a function that returns a table.

Filters can be:

-   Boolean filter expressions
-   Table filter expressions
-   Filter modification functions

When there are multiple filters, they're evaluated by using the AND [logical operator](dax-operator-reference#logical-operators). That means all conditions must be `TRUE` at the same time.

#### Boolean filter expressions

A Boolean expression filter is an expression that evaluates to `TRUE` or `FALSE`. There are several rules that they must abide by:

-   They can reference only a single column.
-   They cannot reference measures.
-   They cannot use a nested CALCULATE function.

Beginning with the September 2021 release of Power BI Desktop, the following also apply:

-   They cannot use functions that scan or return a table unless they are passed as arguments to aggregation functions.
-   They _can_ contain an aggregation function that returns a scalar value.

#### Table filter expression

A table expression filter applies a table object as a filter. It could be a reference to a model table, but more likely it's a function that returns a table object. You can use the [FILTER](filter-function-dax) function to apply complex filter conditions, including those that cannot be defined by a Boolean filter expression.

#### Filter modifier functions

Filter modifier functions allow you to do more than simply add filters. They provide you with additional control when modifying filter context.

Expand table

| Function | Purpose |
| --- | --- |
| REMOVEFILTERS | Remove all filters, or filters from one or more columns of a table, or from all columns of a single table. |
| ALL 1, ALLEXCEPT, ALLNOBLANKROW | Remove filters from one or more columns, or from all columns of a single table. |
| KEEPFILTERS | Add filter without removing existing filters on the same columns. |
| USERELATIONSHIP | Engage an inactive relationship between related columns, in which case the active relationship will automatically become inactive. |
| CROSSFILTER | Modify filter direction (from both to single, or from single to both) or disable a relationship. |

1 The ALL function and its variants behave as both filter modifiers and as functions that return table objects. If the REMOVEFILTERS function is supported by your tool, it's better to use it to remove filters.

## Return value

A table of values.

## Remarks

-   When filter expressions are provided, the CALCULATETABLE function modifies the filter context to evaluate the expression. For each filter expression, there are two possible standard outcomes when the filter expression is not wrapped in the KEEPFILTERS function:
    
    -   If the columns (or tables) aren't in the filter context, then new filters will be added to the filter context to evaluate the expression.
    -   If the columns (or tables) are already in the filter context, the existing filters will be overwritten by the new filters to evaluate the CALCULATETABLE expression.
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.
 

&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
CALCULATETABLE (
    <table_expression>,
    table[column] = 10
)
 
CALCULATETABLE (
    <table_expression>,
    FILTER (
        ALL ( table[column] ),
        table[column] = 10
    )
)
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
-- Returns the colors of Proseware branded products
EVALUATE
CALCULATETABLE (
    VALUES ( 'Product'[Color] ),
    'Product'[Brand] = "Proseware"
)
```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
Delivered Amount 2007 v2 :=
CALCULATE (
    [Sales Amount],
    CALCULATETABLE (
        FILTER (
            Sales,
            RELATED ( 'Date'[Calendar Year] ) = "CY 2007"
        ),
        USERELATIONSHIP (
            Sales[Delivery Date],
            'Date'[Date]
        )
    )
)
```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
Red Products CALCULATETABLE =
CALCULATETABLE (
    ADDCOLUMNS (
        VALUES ( 'Product'[Color] ),
        "Num of Products", COUNTROWS ( 'Product' )
    ),
    'Product'[Color] = "Red"
)
```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
CustomersBuyingInTwoYears =
VAR Customers2007 =
    CALCULATETABLE (
        SUMMARIZE ( Sales, Customer[Customer Code] ),
        'Date'[Calendar Year] = "CY 2007"
    )
VAR Customers2008 =
    CALCULATETABLE (
        SUMMARIZE ( Sales, Customer[Customer Code] ),
        'Date'[Calendar Year] = "CY 2008"
    )
VAR Result =
    INTERSECT ( Customers2007, Customers2008 )
RETURN Result
```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX

```&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
SalesInCountriesWithNoStores :=
VAR CountriesWithActiveStores =
    CALCULATETABLE (
        SUMMARIZE ( Sales, Store[CountryRegion] ),
        ALL ( Sales )
    )
VAR CountriesWithSales =
    SUMMARIZE ( Sales, Customer[CountryRegion] )
VAR CountriesWithNoStores =
    EXCEPT ( CountriesWithSales, CountriesWithActiveStores )
VAR Result =
    CALCULATE (
        [Sales Amount],
        CountriesWithNoStores
    )
RETURN Result
```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX

```