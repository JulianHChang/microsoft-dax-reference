# CALCULATE  
*   **Purpose:** The most powerful function in DAX. It modifies the context in which a calculation is performed.
*   **Why it's important:** It's the gateway to dynamic filtering. Used for YTD, previous period calculations, and "what-if" scenarios.
*   **Example:** `Sales in California = CALCULATE(SUM(Sales[Amount]), Geography[State] = "California")`



<hr style="border: none; border-top: 3px solid black;">


# CALCULATE  
**Purpose**: Modifies the filter context—**the single most important DAX function**.  
**Why it matters**: Enables dynamic calculations by overriding/adding filters.  
**Example**:  
```dax
Sales in 2023 = CALCULATE(SUM(Sales[Amount]), Sales[Year] = 2023)
```



<hr style="border: none; border-top: 3px solid black;">

# CALCULATE  
- **Purpose**: Modifies the filter context to evaluate an expression.
- **Use Case**: Perform calculations with specific filters, like sales for a particular region.
- **Example**: `SalesInRegion = CALCULATE(SUM(Sales[Amount]), Sales[Region] = "West")`

<hr style="border: none; border-top: 3px solid black;">

# CALCULATE

Re-evaluates an expression in a modified filter context. Core of most measures.

**Examples**

```DAX
-- Base measures
Total Sales := SUM ( Sales[SalesAmount] )
Total Cost  := SUM ( Sales[Cost] )

-- Profit with filter change
Profit :=
CALCULATE ( [Total Sales] - [Total Cost] )

-- Sales for current Region only (often redundant but illustrative)
Sales (Current Region) :=
CALCULATE ( [Total Sales], KEEPFILTERS ( VALUES ( Customers[Region] ) ) )

-- Sales ignoring all filters on Products
Sales (Ignore Products) :=
CALCULATE ( [Total Sales], ALL ( Products ) )

-- Sales for Category = "Furniture" regardless of slicers
Sales (Furniture) :=
CALCULATE ( [Total Sales], Products[Category] = "Furniture" )

-- Sales last year using time-intel table
Sales LY :=
CALCULATE ( [Total Sales], DATEADD ( Calendar[Date], -1, YEAR ) )
```



<hr style="border: none; border-top: 3px solid black;">

# CALCULATE

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Evaluates an expression in a modified filter context.

Note

There's also the [CALCULATETABLE](calculatetable-function-dax) function. It performs exactly the same functionality, except it modifies the [filter context](dax-overview#filter-context) applied to an expression that returns a _table object_.

## Syntax

DAX Copy

```
CALCULATE(<expression>[, <filter1> [, <filter2> [, …]]])
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| expression | The expression to be evaluated. |
| filter1, filter2,… | (Optional) Boolean expressions or table expressions that defines filters, or filter modifier functions. |

The expression used as the first parameter is essentially the same as a measure.

Filters can be:

-   Boolean filter expressions
-   Table filter expressions
-   Filter modification functions

When there are multiple filters, they can be evaluated by using the AND (`&&`) [logical operator](dax-operator-reference#logical-operators), meaning all conditions must be `TRUE`, or by the OR (`||`) logical operator, meaning either condition can be true.

#### Boolean filter expressions

A Boolean expression filter is an expression that evaluates to `TRUE` or `FALSE`. There are several rules that they must abide by:

-   They can reference columns from a single table.
-   They cannot reference measures.
-   They cannot use a nested CALCULATE function.

Beginning with the September 2021 release of Power BI Desktop, the following also apply:

-   They cannot use functions that scan or return a table unless they are passed as arguments to aggregation functions.
-   They _can_ contain an aggregation function that returns a scalar value. For example,
    
    DAX Copy
    
    ```
    Total sales on the last selected date =
    CALCULATE (
        SUM ( Sales[Sales Amount] ),
        'Sales'[OrderDateKey] = MAX ( 'Sales'[OrderDateKey] )
    )
    ```
    

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

The value that is the result of the expression.

## Remarks

-   When filter expressions are provided, the CALCULATE function modifies the filter context to evaluate the expression. For each filter expression, there are two possible standard outcomes when the filter expression is not wrapped in the KEEPFILTERS function:
    
    -   If the columns (or tables) aren't in the filter context, then new filters will be added to the filter context to evaluate the expression.
    -   If the columns (or tables) are already in the filter context, the existing filters will be overwritten by the new filters to evaluate the CALCULATE expression.
-   The CALCULATE function used _without filters_ achieves a specific requirement. It transitions row context to filter context. It's required when an expression (not a model measure) that summarizes model data needs to be evaluated in row context. This scenario can happen in a calculated column formula or when an expression in an iterator function is evaluated. Note that when a model measure is used in row context, context transition is automatic.
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.
    


<hr style="border: none; border-top: 3px solid black;">


<hr style="border: none; border-top: 3px solid black;">



