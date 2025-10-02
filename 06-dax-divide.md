# DIVIDE

Safe division that handles divide-by-zero elegantly.

**Examples**

```DAX
-- Profit margin %
Margin % :=
DIVIDE ( [Profit], [Total Sales] )

-- Conversion rate (avoids errors when denominator blank or 0)
Conversion Rate :=
DIVIDE ( DISTINCTCOUNT ( Sales[OrderID] ), DISTINCTCOUNT ( Customers[CustomerID] ), 0 )
```
<hr style="border: none; border-top: 3px solid black;">

# DIVIDE
*   **Purpose:** Safely performs division and handles division by zero errors by returning an alternate result (or `BLANK` by default).
*   **Why it's important:** **Always use this instead of the `/` operator** to avoid errors in your reports.
*   **Example:** `Profit Margin = DIVIDE([Total Profit], [Total Sales], 0)`


<hr style="border: none; border-top: 3px solid black;">

# DIVIDE
- **Purpose**: Performs division, handling divide-by-zero errors.
- **Use Case**: Calculate ratios or percentages safely.
- **Example**: `ProfitMargin = DIVIDE([Profit], [Revenue], 0)`


<hr style="border: none; border-top: 3px solid black;">

# DIVIDE
**Purpose**: Safe division (handles divide-by-zero errors).  
**Why it matters**: Prevents errors in ratios/percentages.  
**Example**:  
```dax
Profit Margin = DIVIDE([Profit], [Revenue], 0) // Returns 0 if [Revenue] = 0
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">

Examples


``` DAX
--  DIVIDE performs safe division protecting from division by
--  zero. In case of zero denominator, it returns its third
--  argument that defaults to a blank.
DEFINE
    MEASURE Sales[Unprotected Growth %] =
        VAR CY = [Sales Amount]
        VAR PY = CALCULATE ( [Sales Amount], SAMEPERIODLASTYEAR( 'Date'[Date] ) )
        RETURN (CY - PY) / PY
    MEASURE Sales[Protected Growth %] =
        VAR CY = [Sales Amount]
        VAR PY = CALCULATE ( [Sales Amount], SAMEPERIODLASTYEAR( 'Date'[Date] ) )
        RETURN DIVIDE ( CY - PY, PY, BLANK () )
EVALUATE
SUMMARIZECOLUMNS (
    'Date'[Calendar Year],
    "Unprotected Growth %", [Unprotected Growth %],
    "Protected Growth %",   [Protected Growth %]
)

```

<hr style="border: none; border-top: 3px solid black;">

# DIVIDE

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Performs division and returns alternate result or BLANK() on division by 0.

## Syntax

DAX Copy

```
DIVIDE(<numerator>, <denominator> [,<alternateresult>])
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| numerator | The dividend or number to divide. |
| denominator | The divisor or number to divide by. |
| alternateresult | (Optional) The value returned when division by zero results in an error. When not provided, the default value is BLANK(). |

## Return value

-   A decimal number.

## Remarks

-   Alternate result on divide by 0 must be a constant.
    
-   For best practices when using DIVIDE, see [DIVIDE function vs. divide operator (/) in DAX](best-practices/dax-divide-function-operator).
    


<hr style="border: none; border-top: 3px solid black;">

## Recommendations

It's recommended that you use the DIVIDE function whenever the denominator is an expression that _could_ return zero or BLANK.

In the case that the denominator is a constant value, we recommend that you use the divide operator. In this case, the division is guaranteed to succeed, and your expression will perform better because it will avoid unnecessary testing.

Carefully consider whether the DIVIDE function should return an alternate value. For measures, it's usually a better design that they return BLANK. Returning BLANK is better because report visuals—by default—eliminate groupings when summarizations are BLANK. It allows the visual to focus attention on groups where data exists. When necessary, in Power BI, you can configure the visual to display all groups (that return values or BLANK) within the filter context by enabling the [Show items with no data](/en-us/power-bi/create-reports/desktop-show-items-no-data) option.
