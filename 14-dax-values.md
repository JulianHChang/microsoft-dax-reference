# VALUES
*   **Purpose:** Returns a one-column table of unique values from the specified column. It respects the current filter context.
*   **Why it's important:** Extremely useful for detecting when a filter returns only a single value (e.g., "if a single product is selected") and for dynamic counting of visible items.
*   **Example:** `# of Selected Products = COUNTROWS(VALUES('Product'[ProductName]))`
Returns a one-column table of unique values of a column in the current filter context (can include a BLANK representing “unknown member”).

**Examples**

```DAX
-- Count of visible categories
# Categories Visible :=
COUNTROWS ( VALUES ( Products[Category] ) )

-- Detect single selection (with nicer fallback)
Chosen Category :=
SELECTEDVALUE ( Products[Category], "All Categories" )

-- Build virtual table for iterators
Sales by Visible Regions :=
SUMX ( VALUES ( Customers[Region] ), [Total Sales] )
```

> Tip: `DISTINCT(Col)` is similar but never returns the *blank* row of relationships; `VALUES` may—handy for data quality diagnostics.

&nbsp;
<hr style="border: none; border-top: 3px solid black;">
Examples


``` DAX
--  DISTINCT retrieves the distinct values of a column
--  VALUES does the same.
--  They differ in the way they handle the blank row generated
--  for invalid relationships, if present
--  DISTINCT does not return the blank row caused by an invalid relationship
--  VALUES includes the blank row caused by an invalid relationship, if present
EVALUATE
SUMMARIZECOLUMNS (
    Store[Continent],
    "#Stores (no blank row)", COUNTROWS ( DISTINCT ( Store[Store Name] ) ),
    "#Stores (blank row)",    COUNTROWS ( VALUES ( Store[Store Name] ) )
)

```





<hr style="border: none; border-top: 3px solid black;">

**Applies to:** [Calculated column] [Calculated table] [Measure] [Visual calculation]

When the input parameter is a column name, returns a one-column table that contains the distinct values from the specified column. Duplicate values are removed and only unique values are returned. A BLANK value can be added. When the input parameter is a table name, returns the rows from the specified table. Duplicate rows are preserved. A BLANK row can be added.

Note

This function cannot be used to Return values into a cell or column on a worksheet; rather, you use it as an intermediate function, nested in a formula, to get a list of distinct values that can be counted or used to filter or sum other values.

## Syntax

DAX Copy

```
VALUES(<TableNameOrColumnName>)
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| TableName or ColumnName | A column from which unique values are to be returned, or a table from which rows are to be returned. |

## Return value

When the input parameter is a column name, a single column table. When the input parameter is a table name, a table of the same columns is returned.

## Remarks

-   When you use the VALUES function in a context that has been filtered, the unique values returned by VALUES are affected by the filter. For example, if you filter by Region, and return a list of the values for City, the list will include only those cities in the regions permitted by the filter. To return all of the cities, regardless of existing filters, you must use the ALL function to remove filters from the table. The second example demonstrates use of ALL with VALUES.
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.
    
-   For best practices when using VALUES, see [Use SELECTEDVALUE instead of VALUES](best-practices/dax-selectedvalue).
    
