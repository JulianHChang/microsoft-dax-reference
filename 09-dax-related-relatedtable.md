# RELATED
Retrieves related values from another table (many-to-one relationship).
```DAX
Product Category = RELATED(Products[Category])
```


<hr style="border: none; border-top: 3px solid black;">

# RELATED  
**Purpose**: Fetches a value from a related table (1-to-many relationships).  
**Why it matters**: Pulls data from dimension tables into fact tables.  
**Example**:  
```dax
Product Category = RELATED(Product[Category])
```

# RELATED

Pulls a scalar value from a *related* (one-side) table; used mostly in calculated columns (but also valid in measures inside row context).

**Examples**

```DAX
-- In a Sales calculated column: Category of the row's product
Sales Category :=
RELATED ( Products[Category] )

-- In a Sales calc column: Region of the row’s customer
Sales Region :=
RELATED ( Customers[Region] )

-- Add tax rate from a related table to compute tax amount per row
Tax Amount :=
Sales[SalesAmount] * RELATED ( Products[TaxRate] )
```

# RELATED
*   **Purpose:** Fetches a value from a related table (on the "one" side of a relationship).
*   **Why it's important:** Used in **calculated columns** to denormalize data and bring related information into the current table for easier filtering and reporting.
*   **Example:** In the `Sales` table, `Product Category = RELATED('Product'[Category])`

<hr style="border: none; border-top: 3px solid black;">
Examples


``` DAX
--  RELATED is needed to access columns of the expanded table
DEFINE
    MEASURE Sales[Sales Amount] =
        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )
    MEASURE Sales[Sales at List Price] =
        SUMX ( Sales, Sales[Quantity] * RELATED ( 'Product'[List Price] ) )
EVALUATE
SUMMARIZECOLUMNS (
    'Date'[Calendar Year],
    "Sales Amount", [Sales Amount],
    "Sales at List Price", [Sales at List Price]
)
```

<hr style="border: none; border-top: 3px solid black;">

# RELATED


**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/no.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Returns a related value from another table.

## Syntax

DAX Copy

```
RELATED(<column>)
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| column | The column that contains the values you want to retrieve. |

## Return value

A single value that is related to the current row.

## Remarks

-   The RELATED function requires that a relationship exists between the current table and the table with related information. You specify the column that contains the data that you want, and the function follows an existing many-to-one relationship to fetch the value from the specified column in the related table. If a relationship does not exist, you must create a relationship.
    
-   When the RELATED function performs a lookup, it examines all values in the specified table regardless of any filters that may have been applied.
    
-   The RELATED function needs a row context; therefore, it can only be used in calculated column expression, where the current row context is unambiguous, or as a nested function in an expression that uses a table scanning function. A table scanning function, such as SUMX, gets the value of the current row value and then scans another table for instances of that value.
    
-   The RELATED function cannot be used to fetch a column across a [limited relationship](/en-us/power-bi/transform-model/desktop-relationships-understand#limited-relationships).


<hr style="border: none; border-top: 3px solid black;">

  

## RELATEDTABLE  
**Purpose**: Returns a table of related rows (many-to-1 relationships).  
**Why it matters**: Aggregates data from fact tables into dimension tables.  
**Example**:  
```dax
Total Sales per Product = SUMX(RELATEDTABLE(Sales), Sales[Amount])
```  

## RELATEDTABLE
Returns all related rows from another table (one-to-many relationship).
```DAX
Customer Sales Count = COUNTROWS(RELATEDTABLE(Sales))
```


## RELATEDTABLE
*   **Purpose:** Returns a table containing all the rows related to the current row (from the "many" side of a relationship).
*   **Why it's important:** Often used with aggregation functions to perform context-aware calculations from the related table.
*   **Example:** `Number of Products Sold = COUNTROWS(RELATEDTABLE(Sales))` (This would be placed in a `Product` table to count how many times each product was sold).

<hr style="border: none; border-top: 3px solid black;">

Examples


``` DAX
--  RELATEDTABLE is an alias - albeit limited - of CALCULATETABLE
DEFINE
    MEASURE Sales[Sales Amount] =
        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )
    MEASURE Sales[TX/Customer w/rel] =
        AVERAGEX ( Customer, COUNTROWS ( RELATEDTABLE ( Sales ) ) )
    MEASURE Sales[TX/Customer w/calc] =
        AVERAGEX ( Customer, COUNTROWS ( CALCULATETABLE ( Sales ) ) )
 
EVALUATE
SUMMARIZECOLUMNS (
    'Date'[Calendar Year],
    "Sales Amount", [Sales Amount],
    "TX/Customer w/rel", [TX/Customer w/rel],
    "TX/Customer w/calc", [TX/Customer w/calc]
)
```