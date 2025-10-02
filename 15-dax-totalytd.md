# TOTALYTD (and friends: TOTALQTD, TOTALMTD)
## TOTALMTD, TOTALQTD, TOTALWTD, TOTALYTD
DAX Function (Time Intelligence)  Context Transition
Built-in time-intelligence aggregators over a date column.

### TOTALYTD / TOTALQTD / TOTALMTD
**Purpose**: Year-to-date (YTD), quarter-to-date (QTD), or month-to-date (MTD) totals.  
**Why it matters**: Standard for financial reporting.  
**Example**:  
```dax
YTD Sales = TOTALYTD(SUM(Sales[Amount]), 'Date'[Date])
```
### TOTALYTD / TOTALQTD / TOTALMTD
Calculates year-to-date, quarter-to-date, or month-to-date totals.
```DAX
YTD Sales = TOTALYTD(SUM(Sales[Amount]), Calendar[Date])
```
#### TOTALYTD / TOTALQTD / TOTALMTD
    *   **Purpose:** Calculates the Year-to-Date, Quarter-to-Date, or Month-to-Date total of an expression.
    *   **Why it's important:** Essential for comparing cumulative performance against goals or previous periods.
    *   **Example:** `Sales YTD = TOTALYTD(SUM(Sales[Amount]), 'Date'[Date])`

#### TOTALYTD
    - **Purpose**: Calculates year-to-date totals for a measure.
    - **Use Case**: Track cumulative sales or performance within a year.
    - **Example**: `YTDSales = TOTALYTD(SUM(Sales[Amount]), Sales[Date])`


**Examples**

```DAX
-- Standard YTD sales
Sales YTD :=
TOTALYTD ( [Total Sales], Calendar[Date] )

-- YTD with non-standard fiscal year starting July 1
Sales YTD (Fiscal Jul-Jun) :=
TOTALYTD ( [Total Sales], Calendar[Date], "6/30" )

-- YTD Profit Margin
Margin % YTD :=
DIVIDE ( TOTALYTD ( [Profit], Calendar[Date] ), TOTALYTD ( [Total Sales], Calendar[Date] ) )
```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">
Examples


``` DAX
--  TOTALYTD is just syntax sugar for CALCULATE / DATESYTD
EVALUATE
CALCULATETABLE (
    { (
        CALCULATE (           
            [Sales Amount],
            DATESYTD ( 'Date'[Date] ) -- 2007-01-01 : 2007-05-12
        ),
        TOTALYTD (           -- 2007-01-01 : 2007-05-12
            [Sales Amount],
            'Date'[Date]      
        )
    ) },
    'Date'[Date] = DATE ( 2007, 5, 12 )
)

```

<hr style="border: none; border-top: 3px solid black;">

# TOTALYTD

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/discouraged.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Note

This function is discouraged for use in [visual calculations](/en-us/power-bi/transform-model/desktop-visual-calculations-overview) as it likely returns meaningless results.

Evaluates the year-to-date value of the `expression` in the current context.

## Syntax

Copy

```
TOTALYTD(<expression>,<dates> or <calendar>[,<filter>][,<year_end_date>])
```

### Parameters

Expand table

| Parameter | Definition |
| --- | --- |
| expression | An expression that returns a scalar value. |
| dates or calendar | A column that contains dates or a calendar reference |
| filter | (optional) An expression that specifies a filter to apply to the current context. |
| year_end_date | (optional) A literal string with a date that defines the year-end date. The default is December 31. |

## Return value

A scalar value that represents the `expression` evaluated for the current year-to-date `dates` or `calendar`.

## Remarks

-   The `dates` argument can be any of the following:
    
    -   A reference to a date/time column.
    -   A table expression that returns a single column of date/time values.
    -   A Boolean expression that defines a single-column table of date/time values.
-   Constraints on Boolean expressions are described in the topic, [CALCULATE](calculate-function-dax).
    
-   The `filter` expression has restrictions described in the topic, [CALCULATE](calculate-function-dax).
    
-   The `year_end_date` parameter is a string literal of a date, in the same locale as the locale of the client where the workbook was created. The year portion of the date is not required and is ignored. For example, the following formula specifies a (fiscal) year\_end\_date of 6/30 in an EN-US locale workbook.
    
    DAX Copy
    
    ```
    = TOTALYTD(SUM(InternetSales_USD[SalesAmount_USD]),DateTime[DateKey], ALL('DateTime'), "6/30")
    ```
    
    In this example, year\_end\_date can be specified as "6/30", "Jun 30", "30 June", or any string that resolves to a month/day. However, it is recommended you specify year\_end\_date using "month/day" (as shown) to ensure the string resolves to a date.
    
-   The `year_end_date` parameter must not be specified when a calendar is used.
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.
    









