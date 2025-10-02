# ALL  (note: `REMOVEFILTERS()` is the modern synonym)

Removes filters from specified columns/tables—crucial for % of total, benchmarks, and rankings.

REMOVEFILTERS is an alias for ALL, but it can be used only as a CALCULATE modifier and not as a table expression like ALL.  
# ALL  
**Purpose**: Removes filters from a table/column (ignores slicers/filters).  
**Why it matters**: Essential for % of total, YoY comparisons, and base calculations.  
**Example**:  
```dax
% of Total Sales = DIVIDE(SUM(Sales[Amount]), CALCULATE(SUM(Sales[Amount]), ALL(Sales)))
```
**Examples**

```DAX
-- % of total sales (over all filters on Customers and Products, but keep time)
% of Total Sales :=
DIVIDE ( [Total Sales], CALCULATE ( [Total Sales], ALL ( Customers ), ALL ( Products ) ) )

-- Category share within current time window, ignoring only Subcategory
Category Share :=
DIVIDE (
    [Total Sales],
    CALCULATE ( [Total Sales], ALL ( Products[Subcategory] ) )
)

-- Same using REMOVEFILTERS
Sales (Ignore Products) :=
CALCULATE ( [Total Sales], REMOVEFILTERS ( Products ) )
```



# ALLSELECTED  
*   **Purpose:** A more advanced version of `ALL`. It removes filters from a table or column *except* for the filters applied by the user (e.g., slicers, report-level filters).
*   **Why it's important:** Essential for creating accurate "percentage of parent total" visualizations and dynamic rankings.
*   **Example:** `% of Selected Region = DIVIDE(SUM(Sales[Amount]), CALCULATE(SUM(Sales[Amount]), ALLSELECTED(Geography)))`


---

# ALLEXCEPT

Remove filters from a table except for specified columns—handy for “within group” calculations.

**Examples**

```DAX
-- Rank product within its Category while keeping Category filter
Product Rank (Keep Category) :=
RANKX (
    CALCULATETABLE ( VALUES ( Products[ProductID] ), ALLEXCEPT ( Products, Products[Category] ) ),
    [Total Sales],
    ,
    DESC
)

-- Category % of total but keep Region
Category % in Region :=
DIVIDE (
    [Total Sales],
    CALCULATE ( [Total Sales], ALLEXCEPT ( Customers, Customers[Region] ) )
)
```
<hr style="border: none; border-top: 3px solid black;">

# ALL

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied. This function is useful for clearing filters and creating calculations on all the rows in a table.

## Syntax

DAX Copy

```
ALL( [<table> | <column>[, <column>[, <column>[,…]]]] )
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| table | The table that you want to clear filters on. |
| column | The column that you want to clear filters on. |

The argument to the ALL function must be either a reference to a base table or a reference to a base column. You cannot use table expressions or column expressions with the ALL function.

## Return value

The table or column with filters removed.

## Remarks

-   This function is not used by itself, but serves as an intermediate function that can be used to change the set of results over which some other calculation is performed.
    
-   The normal behavior for DAX expressions containing the ALL() function is that any filters applied will be ignored. However, there are some scenarios where this is not the case because of `auto-exist`, a DAX technology that optimizes filtering in order to reduce the amount of processing required for certain DAX queries. An example where auto-exist and ALL() provide unexpected results is when filtering on two or more columns of the same table (like when using slicers), and there is a measure on that same table that uses ALL(). In this case, auto-exist will _merge_ the multiple filters into one and will only filter on existing combinations of values. Because of this merge, the measure will be calculated on the existing combinations of values and the result will be based on filtered values instead of all values as expected. To learn more about auto-exist and its effect on calculations, see Microsoft MVP Alberto Ferrari's [Understanding DAX Auto-Exist](https://www.sqlbi.com/articles/understanding-dax-auto-exist/) article on sql.bi.com.
    
-   The following table describes how you can use the ALL and ALLEXCEPT functions in different scenarios.
    
    Expand table
    
    | Function and usage | Description |
    | --- | --- |
    | ALL() | Removes all filters everywhere. ALL() can only be used to clear filters but not to return a table. |
    | ALL(Table) | Removes all filters from the specified table. In effect, ALL(Table) returns all of the values in the table, removing any filters from the context that otherwise might have been applied. This function is useful when you are working with many levels of grouping, and want to create a calculation that creates a ratio of an aggregated value to the total value. The first example demonstrates this scenario. |
    | ALL (Column[, Column[, …]]) | Removes all filters from the specified columns in the table; all other filters on other columns in the table still apply. All column arguments must come from the same table. The ALL(Column) variant is useful when you want to remove the context filters for one or more specific columns and to keep all other context filters. The second and third examples demonstrate this scenario. |
    | ALLEXCEPT(Table, Column1 [,Column2]...) | Removes all context filters in the table except filters that are applied to the specified columns. This is a convenient shortcut for situations in which you want to remove the filters on many, but not all, columns in a table. |
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.
    


<hr style="border: none; border-top: 3px solid black;">


# ALLSELECTED

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Returns all rows in a table, or all the values in a columns, ignoring any filters that might have been applied inside the query, but keeping filters that come from outside.

The ALLSELECTED function gets the context that represents all rows and columns in the query, while keeping explicit filters and contexts other than row and column filters. This function can be used to obtain visual totals in queries.

## Syntax

DAX Copy

```
ALLSELECTED([<tableName> | <columnName>[, <columnName>[, <columnName>[,…]]]] )
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| tableName | The name of an existing table, using standard DAX syntax. This parameter cannot be an expression. This parameter is optional. |
| columnName | The name of an existing column using standard DAX syntax, usually fully qualified. It cannot be an expression. This parameter is optional. |

## Return value

The context of the query without any column and row filters.

## Remarks

-   If there is one argument, the argument is either `tableName` or `columnName`. If there is more than one argument, they must be columns from the same table.
    
-   This function is different from ALL() because it retains all filters explicitly set within the query, and it retains all context filters other than row and column filters.
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.


<hr style="border: none; border-top: 3px solid black;">

# ALLEXCEPT

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/no.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Removes all context filters in the table except filters that have been applied to the specified columns.

## Syntax

DAX Copy

```
ALLEXCEPT(<table>,<column>[,<column>[,…]])
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| table | The table over which all context filters are removed, except filters on those columns that are specified in subsequent arguments. |
| column | The column for which context filters must be preserved. |

The first argument to the ALLEXCEPT function must be a reference to a base table. All subsequent arguments must be references to base columns. You cannot use table expressions or column expressions with the ALLEXCEPT function.

## Return value

A table with all filters removed except for the filters on the specified columns.

## Remarks

-   This function is not used by itself, but serves as an intermediate function that can be used to change the set of results over which some other calculation is performed.
    
-   ALL and ALLEXCEPT can be used in different scenarios:
    
    Expand table
    
    | Function and usage | Description |
    | --- | --- |
    | ALL(Table) | Removes all filters from the specified table. In effect, ALL(Table) returns all of the values in the table, removing any filters from the context that otherwise might have been applied. This function is useful when you are working with many levels of grouping, and want to create a calculation that creates a ratio of an aggregated value to the total value. |
    | ALL (Column[, Column[, …]]) | Removes all filters from the specified columns in the table; all other filters on other columns in the table still apply. All column arguments must come from the same table. The ALL(Column) variant is useful when you want to remove the context filters for one or more specific columns and to keep all other context filters. |
    | ALLEXCEPT(Table, Column1 [,Column2]...) | Removes all context filters in the table except filters that are applied to the specified columns. This is a convenient shortcut for situations in which you want to remove the filters on many, but not all, columns in a table. |
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.
    



<hr style="border: none; border-top: 3px solid black;">