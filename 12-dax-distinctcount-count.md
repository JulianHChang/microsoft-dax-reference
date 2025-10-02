# COUNT
   - **Purpose**: Counts non-blank values in a column.
   - **Use Case**: Count the number of transactions or records.
   - **Example**: `TransactionCount = COUNT(Sales[OrderID])`

# DISTINCTCOUNT
   - **Purpose**: Counts unique values in a column.
   - **Use Case**: Count unique customers or products sold.
   - **Example**: `UniqueCustomers = DISTINCTCOUNT(Sales[CustomerID])`

# DISTINCTCOUNT  
**Purpose**: Counts unique values in a column.  
**Why it matters**: Essential for metrics like unique customers/products.  
**Example**:  
```dax
Unique Products Sold = DISTINCTCOUNT(Sales[ProductID])
```

# COUNTROWS  
**Purpose**: Counts rows in a table.  
**Why it matters**: Measures distinct counts, active customers, etc.  
**Example**:  
```dax
Active Customers = COUNTROWS(Customers)
```


<hr style="border: none; border-top: 3px solid black;">  

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Counts the number of rows in the specified column that contain non-blank values.

## Syntax

DAX Copy

```
COUNT(<column>)
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| column | The column that contains the values to be counted. |

## Return value

A whole number.

## Remarks

-   The only argument allowed to this function is a column. The COUNT function counts rows that contain the following kinds of values:
    
    -   Numbers
    -   Dates
    -   Strings
-   When the function finds no rows to count, it returns a blank.
    
-   Blank values are skipped. `TRUE`/`FALSE` values are not supported.
    
-   If you want to evaluate a column of `TRUE`/`FALSE` values, use the COUNTA function.
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.
    
-   For best practices when using COUNT, see [Use COUNTROWS instead of COUNT](best-practices/dax-countrows).

<hr style="border: none; border-top: 3px solid black;">

# DISTINCTCOUNT

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Counts the number of distinct values in a column.

## Syntax

DAX Copy

```
DISTINCTCOUNT(<column>)
```

### Parameters

Expand table

| Term | Description |
| --- | --- |
| column | The column that contains the values to be counted |

## Return value

The number of distinct values in `column`.

## Remarks

-   The only argument allowed to this function is a column. You can use columns containing any type of data. When the function finds no rows to count, it returns a BLANK, otherwise it returns the count of distinct values.
    
-   DISTINCTCOUNT function counts the BLANK value. To skip the BLANK value, use the [DISTINCTCOUNTNOBLANK](distinctcountnoblank-function-dax) function.
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.