# SWITCH
*   **Purpose:** A "cleaner" alternative to nested `IF` statements. It evaluates an expression against a list of values and returns one of multiple possible result expressions.
*   **Why it's important:** Makes your code much more readable when you have multiple conditions.
    *   **Example:**
        ```dax
        Region Group = SWITCH(TRUE(),
            [Region] IN {"North", "South"}, "Group A",
            [Region] IN {"East", "West"}, "Group B",
            "Other"
        )
        ```

<hr style="border: none; border-top: 3px solid black;">

# SWITCH
Cleaner alternative to nested IF statements for multiple conditions.
```DAX
Region Name = SWITCH([Region Code], 1, "North", 2, "South", 3, "East", "Other")
```

<hr style="border: none; border-top: 3px solid black;">

# SWITCH

Cleaner multi-branch logic; great for mapping bins/labels.

**Examples**

```DAX
-- Segment label remap
Segment (Clean) :=
SWITCH (
    TRUE(),
    SELECTEDVALUE ( Customers[Segment] ) IN { "Corporate", "Enterprise" }, "B2B",
    SELECTEDVALUE ( Customers[Segment] ) = "Consumer", "B2C",
    "Other"
)

-- Dynamic metric selector from slicer (Values: "Sales","Profit","Margin %")
Metric Selector :=
VAR pick = SELECTEDVALUE ( 'Selectors'[Metric], "Sales" )
RETURN
SWITCH (
    pick,
    "Sales",  [Total Sales],
    "Profit", [Profit],
    "Margin %", [Margin %],
    BLANK()
)
```
<hr style="border: none; border-top: 3px solid black;">
**Examples**

``` DAX
SWITCH (
   TRUE (),
   Product[Size] = "XL" && Product[Color] = "Red", "Red and XL",
   Product[Size] = "XL" && Product[Color] = "Blue", "Blue and XL",
   Product[Size] = "L" && Product[Color] = "Green", "Green and L"
)
```

<hr style="border: none; border-top: 3px solid black;">
**Examples**

``` DAX
'Product'[SizeDesc] =
SWITCH (
    'Product'[Size],
    "S", "Small",
    "M", "Medium",
    "L", "Large",
    "XL", "Extra Large",
    "Other"
)
```

Examples


``` DAX
SWITCH (
    [A],
    0, "Zero",
    1, "One",
    2, "Two",
    "Other numbers"
)
```

<hr style="border: none; border-top: 3px solid black;">

Examples

``` DAX
SWITCH (
    TRUE,
    [A] > [B], "First case",
    [A] = [B], "Second case",
    [A] = 0, "Third case",
    "Fourth case"
)
```


<hr style="border: none; border-top: 3px solid black;">

Examples

``` DAX
--  SWITCH evaluates its first argument (value) and then uses the next
--  remaining parameters in pair: the first element is used to match the value
--  the second as the result if there is a match.
--  The last argument, alone, provides the no-match value and it defaults
--  to blank.
DEFINE MEASURE Sales[Discounted Sales] =
    SUMX (
        VALUES ( 'Product'[Category] ),
        VAR DiscountPct =
            SWITCH (
                'Product'[Category],
                "Audio", 0.15,
                "Computers", 0.2,
                "Cell phones", 0.13,
                0
            )
        RETURN
            [Sales Amount] * (1 - DiscountPct )
    )
EVALUATE
SUMMARIZECOLUMNS (
    'Product'[Category],
    "Sales Amount", [Sales Amount],
    "Discounted sales", [Discounted Sales]
)
ORDER BY [Category]
```
<hr style="border: none; border-top: 3px solid black;">

**Examples**


``` DAX
--  A common usage of SWITCH is to use a constant as the value argument
--  and expressions in the pairs. This technique allows more flexibility
--  even though it somewhat lacks in readability.
DEFINE MEASURE Sales[Discounted Sales] =
    SUMX (
        SUMMARIZE ( Sales, Sales[Net Price], Product[Category] ),
        VAR DiscountPct =
            SWITCH (
                TRUE,
                Sales[Net Price] <= 150, 0.15,
                Sales[Net Price] <= 1000, 0.2,
                Product[Category] = "Audio", 0.13,
                0
            )
        RETURN
            [Sales Amount] * (1 - DiscountPct )
    )
EVALUATE
SUMMARIZECOLUMNS (
    'Product'[Category],
    "Sales Amount", [Sales Amount],
    "Discounted sales", [Discounted Sales]
)
ORDER BY [Category]

```

&nbsp;
<hr style="border: none; border-top: 3px solid black;">

**Examples**


``` DAX
--  Using SWITCH the first condition met defines the result.
--  In the following example, the second condition (<= 150) will never be
--  met, because the first one is less restrictive.
DEFINE MEASURE Sales[Discounted Sales] =
    SUMX (
        SUMMARIZE ( Sales, Sales[Net Price], Product[Category] ),
        VAR DiscountPct =
            SWITCH (
                TRUE,
                Sales[Net Price] <= 1000, 0.2,
                Sales[Net Price] <= 150, 0.15,
                Product[Category] = "Audio", 0.13,
                0
            )
        RETURN
            [Sales Amount] * (1 - DiscountPct )
    )
EVALUATE
SUMMARIZECOLUMNS (
    'Product'[Category],
    "Sales Amount", [Sales Amount],
    "Discounted sales", [Discounted Sales]
)
ORDER BY [Category]

```

<hr style="border: none; border-top: 3px solid black;">

# SWITCH

**Applies to:** ![](media/icons/yes.png) [Calculated column](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-column-dax) ![](media/icons/yes.png) [Calculated table](/en-us/power-bi/transform-model/desktop-calculations-options#calculated-table) ![](media/icons/yes.png) [Measure](/en-us/power-bi/transform-model/desktop-calculations-options#measures) ![](media/icons/yes.png) [Visual calculation](/en-us/power-bi/transform-model/desktop-calculations-options#visual-calculation)

Evaluates an expression against a list of values and returns one of multiple possible result expressions. This function can be used to avoid having multiple nested [IF](if-function-dax) statements.

## Syntax

DAX Copy

```
SWITCH(<expression>, <value>, <result>[, <value>, <result>]…[, <else>])
```

### Parameters

Expand table

| Term | Definition |
| --- | --- |
| expression | Any DAX expression that returns a single scalar value where the expression is to be evaluated multiple times (for each row/context). |
| value | A constant value to be matched with the results of expression. |
| result | Any scalar expression to be evaluated if the results of expression match the corresponding value. |
| else | Any scalar expression to be evaluated if the result of expression doesn't match any of the value arguments. |

## Return value

If there’s a match with a `value`, a scalar value from the corresponding `result` is returned. If there isn’t a match with a `value`, a value from `else` is returned. If none of the `values` match and `else` isn’t specified, BLANK is returned.

## Remarks

-   The `expression` to be evaluated can be a constant value or an expression. A common use of this function is to set the first parameter to `TRUE`. See examples below.
-   All `result` expressions and the `else` expression must be of the same data type.
-   The order of conditions matters. As soon as one `value` matches, the corresponding `result` is returned, and other subsequent `values` aren’t evaluated. Make sure the most restrictive `values` to be evaluated are specified before less restrictive `values`. See examples below.



<hr style="border: none; border-top: 3px solid black;">



<hr style="border: none; border-top: 3px solid black;">