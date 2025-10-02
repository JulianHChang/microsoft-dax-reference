# IF
*   **Purpose:** Checks a condition and returns one value if `TRUE`, and another if `FALSE`.
*   **Why it's important:** Fundamental for conditional logic and creating custom categorizations.
*   **Example:** `Sales Category = IF([Total Sales] > 1000, "High", "Low")`



<hr style="border: none; border-top: 3px solid black;">  

# IF
- **Purpose**: Evaluates a condition and returns one value if true, another if false.
- **Use Case**: Categorize data, like marking high sales as "Top Performer."
- **Example**: `SalesCategory = IF(SUM(Sales[Amount]) > 10000, "High", "Low")`

<hr style="border: none; border-top: 3px solid black;">

# IF  
**Purpose**: Conditional logic (like Excel’s `IF`).  
**Why it matters**: Handles business rules, thresholds, and dynamic outputs.  
**Example**:  
```dax
Performance = IF([Sales] > 10000, "High", "Low")
```

# IF

Conditional branching (useful for badges, flags, thresholds).

**Examples**

```DAX
-- Flag profitable vs loss-making
Profitability Flag :=
IF ( [Profit] >= 0, "Profitable", "Loss" )

-- Thresholded KPI badges
Sales KPI :=
IF ( [Total Sales] >= 1000000, "🟢 Target", IF ( [Total Sales] >= 800000, "🟡 Watch", "🔴 Miss" ) )

-- Guard rails (e.g., show measure only after selection)
Sales When Category Selected :=
IF ( NOT ISEMPTY ( VALUES ( Products[Category] ) ), [Total Sales], BLANK () )
```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">

**Examples**


``` DAX
--  IF evaluates one of two branches, depending on the condition
EVALUATE
ADDCOLUMNS (
    VALUES ( 'Product'[Brand] ),
    "Sales 1", [Sales Amount],
    "Sales 2",
        VAR SalesAmount = [Sales Amount]
        RETURN
            IF ( SalesAmount > 3000000, 3000000, SalesAmount ),
    "Sales 3", MIN ( [Sales Amount], 3000000 )
)
ORDER BY 'Product'[Brand]
```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">

**Examples**


``` DAX
--  The "else" branch is optional.
--  If not specified, it defaults to BLANK
EVALUATE
ADDCOLUMNS (
    VALUES ( 'Product'[Brand] ),
    "Sales 1", [Sales Amount],
    "Sales 2",
        VAR SalesAmount = [Sales Amount]
        RETURN
            IF ( SalesAmount > 3000000, 3000000 )
)
ORDER BY 'Product'[Brand]
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">

**Examples**


``` DAX
--  The two branches of IF can use different datatypes for
--  their result. In that case, the IF function returns a
--  VARIANT datatype
EVALUATE
ADDCOLUMNS (
    VALUES ( 'Product'[Brand] ),
    "Sales 1", [Sales Amount],
    "Sales 2",
        VAR SalesAmount = [Sales Amount]
        RETURN
            IF ( SalesAmount > 3000000, 3000000, "Less than 3K" )
)
ORDER BY 'Product'[Brand]
```



&nbsp;
<hr style="border: none; border-top: 3px solid black;">

**Examples**


``` DAX
EVALUATE
VAR SelectedYear = SELECTEDVALUE ( 'Date'[Calendar Year Number] )
VAR CurrentYear = YEAR ( TODAY () )
VAR Period =
    IF (
        SelectedYear = CurrentYear,
        DATESYTD ( 'Date'[Date] ),     -- Table
        VALUES ( 'Date'[Date] )        -- Table
    )
VAR Result = CALCULATE ( [Sales Amount], Period )
RETURN { Result }
```

<hr style="border: none; border-top: 3px solid black;">

# IF

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Checks a condition, and returns one value when it's `TRUE`, otherwise it returns a second value.

## Syntax

DAX Copy

```
IF(<logical_test>, <value_if_true>[, <value_if_false>])
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| logical_test | Any value or expression that can be evaluated to TRUE or FALSE. |
| value_if_true | The value that's returned if the logical test is TRUE. |
| value_if_false | (Optional) The value that's returned if the logical test is FALSE. If omitted, BLANK is returned. |

## Return value

Either `value_if_true`, `value_if_false`, or `BLANK`.

## Remarks

-   The IF function can return a variant data type if `value_if_true` and `value_if_false` are of different data types, but the function attempts to return a single data type if both `value_if_true` and `value_if_false` are of numeric data types. In the latter case, the IF function will implicitly convert data types to accommodate both values.
    
    For example, the formula `IF(<condition>, TRUE(), 0)` returns `TRUE` or 0, but the formula `IF(<condition>, 1.0, 0)` returns only decimal values even though `value_if_false` is of the whole number data type. To learn more about implicit data type conversion, see [Data types](dax-overview#data-types).
    
-   To execute the branch expressions regardless of the condition expression, use [IF.EAGER](if-eager-function-dax) instead.




<hr style="border: none; border-top: 3px solid black;">

# IFERROR
&nbsp;
<hr style="border: none; border-top: 3px solid black;">
Examples


``` DAX
--  IFERROR detects if the first argument produces an error.
--  In that case, it returns the second argument, without
--  erroring out.
DEFINE
MEASURE Sales[Year Value unprotected] =
    VAR CurrentYear = SELECTEDVALUE ( 'Date'[Calendar Year] )
    RETURN VALUE ( CurrentYear )
MEASURE Sales[Year Value] =
    VAR CurrentYear = SELECTEDVALUE ( 'Date'[Calendar Year] )
    RETURN
        IFERROR (
            INT ( VALUE ( CurrentYear ) ),
            INT ( VALUE ( RIGHT ( CurrentYear, 4 ) ) )
        )
EVALUATE
    SUMMARIZECOLUMNS (
        'Date'[Calendar Year],
        // If you uncomment the following line, the query generates an error
        // "Year Value unprotected", [Year Value unprotected],
        "Year Value", [Year Value]
       )
ORDER BY [Calendar Year]

```
**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Evaluates an expression and returns a specified value if the expression returns an error; otherwise returns the value of the expression itself.

## Syntax

DAX Copy

```
IFERROR(value, value_if_error)
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| value | Any value or expression. |
| value_if_error | Any value or expression. |

## Return value

A scalar of the same type as `value`

## Remarks

-   You can use the IFERROR function to trap and handle errors in an expression.
    
-   If `value` or `value_if_error` is an empty cell, IFERROR treats it as an empty string value ("").
    
-   The IFERROR function is based on the IF function, and uses the same error messages, but has fewer arguments. The relationship between the IFERROR function and the IF function as follows:
    
    `IFERROR(A,B) := IF(ISERROR(A), B, A)`
    
    Values that are returned for A and B must be of the same data type; therefore, the column or expression used for `value` and the value returned for `value_if_error` must be the same data type.
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.
    
-   For best practices when using IFERROR, see [Appropriate use of error functions](best-practices/dax-error-functions).



---
# Logical functions

## In this article

Logical functions act upon an expression to return information about the values or sets in the expression. For example, you can use the IF function to check the result of an expression and create conditional results.

## In this category

| Function | Description |
| --- | --- |
| AND | Checks whether both arguments are TRUE, and returns TRUE if both arguments are TRUE. |
| BITAND | Returns a bitwise 'AND' of two numbers. |
| BITLSHIFT | Returns a number shifted left by the specified number of bits. |
| BITOR | Returns a bitwise 'OR' of two numbers. |
| BITRSHIFT | Returns a number shifted right by the specified number of bits. |
| BITXOR | Returns a bitwise 'XOR' of two numbers. |
| COALESCE | Returns the first expression that does not evaluate to BLANK. |
| FALSE | Returns the logical value FALSE. |
| IF | Checks a condition, and returns one value when TRUE, otherwise it returns a second value. |
| IF.EAGER | Checks a condition, and returns one value when TRUE, otherwise it returns a second value. Uses an eager execution plan which always executes the branch expressions regardless of the condition expression. |
| IFERROR | Evaluates an expression and returns a specified value if the expression returns an error |
| NOT | Changes FALSE to TRUE, or TRUE to FALSE. |
| OR | Checks whether one of the arguments is TRUE to return TRUE. |
| SWITCH | Evaluates an expression against a list of values and returns one of multiple possible result expressions. |
| TRUE | Returns the logical value TRUE. |

* * *