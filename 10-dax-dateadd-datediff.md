# DATEADD

Shifts a date column by an interval; foundation for custom time-intelligence.

**Examples**

```DAX
-- Prior month sales
Sales PM :=
CALCULATE ( [Total Sales], DATEADD ( Calendar[Date], -1, MONTH ) )

-- Quarter-over-quarter delta
Sales QoQ Δ :=
[Total Sales] - CALCULATE ( [Total Sales], DATEADD ( Calendar[Date], -1, QUARTER ) )

-- YoY % change
Sales YoY % :=
DIVIDE (
    [Total Sales] - CALCULATE ( [Total Sales], DATEADD ( Calendar[Date], -1, YEAR ) ),
    CALCULATE ( [Total Sales], DATEADD ( Calendar[Date], -1, YEAR ) )
)
```
<hr style="border: none; border-top: 3px solid black;">
Examples


``` DAX
--  DATEADD is a more generic functions.
--  It shifts a period back and forth over time using
--  DAY, MONTH, QUARTER, YEAR
--  This example produces the same result as SAMEPERIODLASTYEAR
EVALUATE
VAR StartDate = DATE ( 2008, 07, 25 )
VAR EndDate =   DATE ( 2008, 07, 31 )
RETURN
    CALCULATETABLE (
        DATEADD ( 'Date'[Date], -1, YEAR ),
        'Date'[Date] >= StartDate &&
        'Date'[Date] <= EndDate
    )
ORDER BY [Date]
```
<hr style="border: none; border-top: 3px solid black;">

# DATEADD

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/discouraged.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Note

This function is discouraged for use in [visual calculations](/en-us/power-bi/transform-model/desktop-visual-calculations-overview) as it likely returns meaningless results.

If input is a date column, returns a table that contains a column of dates, shifted either forward or backward in time by the specified number of intervals from the dates in the current context.  
If the input is a calendar, the function returns dates shifted forward or backward in time by the specified number of intervals, based on the current context. The output includes the primary tagged columns as well as time-related columns.

## Syntax

Copy

```
DATEADD(<dates> or <calendar>, <number_of_intervals>, <interval>[,<Extension>],[,<Truncation>])
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| dates or calendar | A column that contains dates or a calendar reference. |
| number_of_intervals | An integer that specifies the number of intervals to add to or subtract from the dates. |
| interval | The interval by which to shift the dates. The value for interval can be one of the following: year, quarter, month, week, day. The week enum is only applicable when a calendar reference is provided. |
| extension | Only applicable when a calendar reference is provided. Define behavior when the original time period has fewer dates than the resulting time period. Valid values are: EXTENDING (Default), PRECISE. |
| truncation | Only applicable when a calendar reference is provided. Define behavior when the original time period has more dates than the resulting time period. Valid values are: BLANKS (Default), ANCHORED. |

## Return value

For date column input, a table containing a single column of date values.  
For calendar input, a table that contains all primary tagged columns and time related columns for the shifted periods, in the current context.

## Remarks

The `dates` argument can be any of the following:

-   A reference to a date/time column,
    
-   A table expression that returns a single column of date/time values,
    
-   A Boolean expression that defines a single-column table of date/time values.
    
    Note
    
    Constraints on Boolean expressions are described in the topic, [CALCULATE function](calculate-function-dax).
    
-   If the number specified for `number_of_intervals` is positive, the dates in `dates` are moved forward in time; if the number is negative, the dates in `dates` are shifted back in time.
    
-   The `interval` parameter is an enumeration, not a set of strings; therefore values should not be enclosed in quotation marks. Also, the values: `year`, `quarter`, `month`, `day` should be spelled in full when using them.
    
-   The result table includes only dates that exist in the `dates` column.
    
-   If the date column syntax is used and the dates in the current context do not form a contiguous interval, the function returns an error.
    
-   This function is not supported for use in DirectQuery mode when used in calculated columns or row-level security (RLS) rules.



<hr style="border: none; border-top: 3px solid black;">


# DATEDIFF

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Returns the number of interval boundaries between two dates.

## Syntax

DAX Copy

```
DATEDIFF(<Date1>, <Date2>, <Interval>)
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| Date1 | A scalar datetime value. |
| Date2 | A scalar datetime value. |
| Interval | The interval to use when comparing dates. The value can be one of the following:- SECOND- MINUTE- HOUR- DAY- WEEK- MONTH- QUARTER- YEAR |

## Return value

The count of interval boundaries between two dates.

## Remarks

A positive result is returned if Date2 is larger than Date1. A negative result is returned if Date1 is larger than Date2.


<hr style="border: none; border-top: 3px solid black;">



<hr style="border: none; border-top: 3px solid black;">

```DAX
--  DATEDIFF computes the delta between two dates, using different units of measure
--  YEAFRAC returns the delta as a fraction (in years)
EVALUATE
VAR StartDate =  DATE ( 2011, 01, 01 )
VAR EndDate =    DATE ( 2012, 12, 15 )
RETURN
    {
        ( "DATEDIFF Year",     DATEDIFF ( StartDate, EndDate, YEAR ) ),
        ( "DATEDIFF Quarter",  DATEDIFF ( StartDate, EndDate, QUARTER ) ),
        ( "DATEDIFF Month",    DATEDIFF ( StartDate, EndDate, MONTH ) ),
        ( "DATEDIFF Day",      DATEDIFF ( StartDate, EndDate, DAY ) ),
        ( "Subtraction-1",     INT ( EndDate - StartDate ) ),
        ( "Subtraction-2",     CONVERT ( EndDate - StartDate, INTEGER ) ),
        ( "YEARFRAC",          YEARFRAC ( StartDate, EndDate ) )
    }   
```


