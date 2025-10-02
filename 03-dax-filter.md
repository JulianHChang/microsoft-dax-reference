# FILTER

Returns a table with rows that meet a condition; usually inside iterators or CALCULATE.


# FILTER  
*   **Purpose:** Returns a table that is a subset of another table or expression, based on a condition.
*   **Why it's important:** Often used inside `CALCULATE` to apply complex filter logic that isn't possible with simple column filters.
*   **Example:** `High Value Sales = CALCULATE(SUM(Sales[Amount]), FILTER(Sales, Sales[Amount] > 1000))`

# FILTER  
Returns a filtered table based on conditions. Often used with CALCULATE.
```DAX
High Value Sales = CALCULATE(SUM(Sales[Amount]), FILTER(Sales, Sales[Amount] > 1000))
```

# FILTER  
- **Purpose**: Returns a table filtered based on a condition.
- **Use Case**: Create a subset of data, like sales above a threshold.
- **Example**: `HighValueSales = FILTER(Sales, Sales[Amount] > 1000)`


# FILTER    
**Purpose**: Returns a filtered table. Often used inside `CALCULATE`.  
**Why it matters**: Creates dynamic subsets of data for complex logic.  
**Example**:  
```dax
High-Value Sales = CALCULATE(SUM(Sales[Amount]), FILTER(Sales, Sales[Amount] > 1000))
```

**Examples**

```DAX
-- Sales for high-value orders only
Sales (Orders > $5000) :=
CALCULATE (
    [Total Sales],
    FILTER ( Sales, Sales[SalesAmount] > 5000 )
)

-- Dynamic “Top N Customers by Sales”
Top N Customers Sales :=
VAR N = 10
RETURN
CALCULATE (
    [Total Sales],
    FILTER (
        VALUES ( Customers[CustomerID] ),
        RANKX ( ALL ( Customers[CustomerID] ), [Total Sales] ) <= N
    )
)

-- Exclude returned/negative sales rows
Sales (Positive Rows Only) :=
CALCULATE ( [Total Sales], FILTER ( Sales, Sales[SalesAmount] > 0 ) )
```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">
Examples


``` DAX
EVALUATE
FILTER (
    Customer,
    Customer[Continent] = "Europe"
)

```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">
**Examples**


``` DAX
--  Being an iterator, FILTER creates a row context. If you need
--  to access related tables, the RELATED function is needed.
--  This makes the usage of CALCULATE preferred over FILTER, when
--  possible
DEFINE
    MEASURE Sales[Red Sales] =
        SUMX (
            FILTER ( Sales, RELATED ( Product[Color] ) = "Red" ),
            Sales[Quantity] * Sales[Net Price]
        )
    MEASURE Sales[Red Sales CALCULATE] =
        CALCULATE ( [Sales Amount], KEEPFILTERS ( Product[Color] = "Red" ) )
EVALUATE
SUMMARIZECOLUMNS (
    Product[Brand],
    "Sales", [Sales Amount],
    "Red Sales", [Red Sales],
    "Red Sales CALCULATE", [Red Sales CALCULATE]
)

```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">
Examples


``` DAX
DEFINE
    MEASURE Sales[Sales Amount] =
        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )
    MEASURE Sales[Sales in countries >3M] =
        CALCULATE (
            [Sales Amount],
            FILTER (
                ALL ( Customer[CountryRegion] ),
                [Sales Amount] > 3000000
            )
       )
EVALUATE
SUMMARIZECOLUMNS (
    'Date'[Calendar Year],
    "Sales amount", [Sales Amount],
    "Sales in countries >3M", [Sales in countries >3M]
)

```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">
Examples


``` DAX
--  AVERAGE Monthly Sales computes the average sales for the months
--  with at least 10K sales
DEFINE
    MEASURE Sales[Sales Amount] =
        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )
    MEASURE Sales[AVERAGE Monthly Sales] =
        VAR MonthlySales =
            ADDCOLUMNS (
                DISTINCT ( 'Date'[Calendar Year Month] ),
                "@MonthlySales", [Sales Amount]
            )
        VAR FilteredSales =
            -- Iterator required to filter the @MonthlySales column       
            FILTER ( MonthlySales, [@MonthlySales] > 10000 )
        VAR Result =
            AVERAGEX ( FilteredSales, [@MonthlySales] )
        RETURN
            Result
EVALUATE
SUMMARIZECOLUMNS (
    'Product'[Color],
    "AVERAGE Monthly Sales", [AVERAGE Monthly Sales]
)

```



# Understanding FILTER

* **Role and syntax:** `FILTER` is a basic table function **and** an iterator. It takes a table and a logical condition—`FILTER ( <table>, <condition> )`—and returns all rows that satisfy the condition by scanning the table row by row.
* **Typical use:** Frequently used to restrict rows before iterators like `SUMX`, yielding clearer intent and fewer iterations. Conditions applied by `FILTER` add to existing filter context (e.g., category plus color).
* **Nesting and logic:** Nesting `FILTER` calls is logically equivalent to combining their conditions with `AND`. While results match, performance can differ; applying the **most selective** condition first (inner `FILTER`) reduces the rows processed and can be faster on large tables.
* **Readability and performance:** Rewriting expressions to make the filter set explicit (e.g., `COUNTROWS(FILTER(...))` instead of tallying with `SUMX` + `IF`) improves readability and often lets the DAX optimizer produce a better plan.

```DAX
FILTER ( <table>, <condition> )
```

```DAX
FabrikamProducts =
FILTER (
    'Product',
    'Product'[Brand] = "Fabrikam"
)
```

```DAX
RedSales :=
SUMX (
    FILTER (
        Sales,
        RELATED ( 'Product'[Color] ) = "Red"
    ),
    Sales[Quantity] * Sales[Net Price]
)
```

```DAX
FabrikamHighMarginProducts =
FILTER (
    FILTER (
        'Product',
        'Product'[Brand] = "Fabrikam"
    ),
    'Product'[Unit Price] > 'Product'[Unit Cost] * 3
)
```

```DAX
FabrikamHighMarginProducts =
FILTER (
    'Product',
    AND (
        'Product'[Brand] = "Fabrikam",
        'Product'[Unit Price] > 'Product'[Unit Cost] * 3
    )
)
```

```DAX
FabrikamHighMarginProducts =
FILTER (
    FILTER (
        'Product',
        'Product'[Unit Price] > 'Product'[Unit Cost] * 3
    ),
    'Product'[Brand] = "Fabrikam"
)
```

```DAX
NumOfRedProducts :=
SUMX (
    'Product',
    IF ( 'Product'[Color] = "Red", 1, 0 )
)
```

```DAX
NumOfRedProducts :=
COUNTROWS (
    FILTER ( 'Product', 'Product'[Color] = "Red" )
)
```

<hr style="border: none; border-top: 3px solid black;">

# FILTER

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Returns a table that represents a subset of another table or expression.

## Syntax

DAX Copy

```
FILTER(<table>,<filter>)
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| table | The table to be filtered. The table can also be an expression that results in a table. |
| filter | A Boolean expression that is to be evaluated for each row of the table. For example, [Amount] > 0 or [Region] = "France" |

## Return value

A table containing only the filtered rows.

## Remarks

-   You can use FILTER to reduce the number of rows in the table that you are working with, and use only specific data in calculations. FILTER is not used independently, but as a function that is embedded in other functions that require a table as an argument.
    
-   For best practices when using FILTER, see [Avoid using FILTER as a filter argument](best-practices/dax-avoid-avoid-filter-as-filter-argument).
    

Use COUNTROWS instead of COUNT in DAX

-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.


