# SELECTEDVALUE

**Purpose**: Returns a value when a single value is selected in a slicer/filter.  
**Why it matters**: Dynamic titles, conditional formatting, and user-driven logic.  
Returns the selected scalar if a single value is in context; otherwise returns BLANK (or a provided alternate). Great for slicer-driven labels and logic.

**Example**:  
```dax
Report Title = "Sales for " & SELECTEDVALUE(Region[Name], "All Regions")
```

**Examples**

```DAX
-- Friendly title piece
Title - Region :=
"Region: " & SELECTEDVALUE ( Customers[Region], "All" )

-- Parameterize a metric (used earlier)
Chosen Metric :=
SELECTEDVALUE ( 'Selectors'[Metric], "Sales" )

-- Switch behavior based on a chosen threshold
Dynamic Threshold Sales :=
VAR Threshold = SELECTEDVALUE ( 'Selectors'[Threshold], 100000 )
RETURN
CALCULATE ( [Total Sales], FILTER ( Sales, Sales[SalesAmount] >= Threshold ) )
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">

**Examples**


``` DAX
-- Shows "unknown" when there are more values in the filter context
-- for the columns specified in the first argument of SELECTEDVALUE
EVALUATE
CALCULATETABLE (
    SUMMARIZECOLUMNS (
        Product[Brand],
        ROLLUPADDISSUBTOTAL ( 'Product'[Category], "Total Category" ),
         
        "Current Brand",    SELECTEDVALUE ( 'Product'[Brand],        "**Unknown Brand**" ),
        "Current Category", SELECTEDVALUE ( 'Product'[Category],     "**Unknown Category**" ),
        "Current Product",  SELECTEDVALUE ( 'Product'[Product Name], "**Unknown Product**" )
    ),
     
    TREATAS ( { "Litware", "A. Datum" }, Product[Brand] )
)

```

<hr style="border: none; border-top: 3px solid black;">

# SELECTEDVALUE

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Returns the value when the context for columnName has been filtered down to one distinct value only. Otherwise returns alternateResult.

## Syntax

DAX Copy

```
SELECTEDVALUE(<columnName>[, <alternateResult>])
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| columnName | The name of an existing column, using standard DAX syntax. It cannot be an expression. |
| alternateResult | (Optional) The value returned when the context for columnName has been filtered down to zero or more than one distinct value. When not provided, the default value is BLANK(). |

## Return value

The value when the context for columnName has been filtered down to one distinct value only. Else, alternateResult.

## Remarks

-   An equivalent expression for `SELECTEDVALUE(<columnName>, <alternateResult>)` is `IF(HASONEVALUE(<columnName>), VALUES(<columnName>), <alternateResult>)`.
    
-   To learn more about best practices when using SELECTEDVALUE, see [Use SELECTEDVALUE instead of VALUES in DAX](best-practices/dax-selectedvalue).
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.