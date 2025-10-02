# CALCULATE rules

**Essentials and context**

* CALCULATE operates within an existing **evaluation context** composed of a **filter context** and possibly one or more **row contexts** (the *original* context).
* When invoked, CALCULATE constructs a **new filter context** in which it evaluates its first argument (the expression). Row contexts do **not** carry over; they are converted into filters via **context transition**.

**Accepted parameter types**

* **Expression (first argument):** evaluated in the new filter context.
* **Explicit filter arguments:** manipulate filters; each can be paired with modifiers like **KEEPFILTERS**.
* **CALCULATE modifiers:** alter model filtering or relationship behavior (e.g., **USERELATIONSHIP**, **CROSSFILTER**, **ALL*** family).

**Context transition details**

* If one or more row contexts exist in the original context, CALCULATE performs a **context transition**, adding implicit filters based on the current row-context column values (not necessarily yielding a single-row filter).
* Implicit filters produced from row contexts that iterate table expressions marked with **KEEPFILTERS** are themselves affected by **KEEPFILTERS**.

**Evaluation algorithm (precise order)**

1. **Evaluate explicit filter arguments** in the **original evaluation context** (both its filter and any row contexts). Each explicit filter is evaluated independently at this stage.
2. **Copy the original filter context** to start building the **new filter context**; discard original row contexts.
3. **Apply context transition**: convert active row contexts into corresponding filters and apply them to the new filter context. (Skipped if no row context exists.)
4. **Apply CALCULATE modifiers** (**USERELATIONSHIP**, **CROSSFILTER**, **ALL***). Because this occurs **after** context transition, these modifiers can change or neutralize its effects (e.g., ALL can remove filters introduced by the transition).
5. **Apply the results of explicit filter arguments**—as evaluated in step 1—to the new filter context from step 4. These can overwrite prior filters and persist even when ALL* removed earlier filters, since their *evaluation* happened in the original context and is not influenced by other modifiers within the same CALCULATE call.
6. The filter context resulting from step 5 becomes the **new filter context** in which CALCULATE **evaluates the expression** (first argument).

**Key takeaways**

* CALCULATE always evaluates its expression in a **new filter context** built systematically from: (a) a copy of the original filter context, (b) filters introduced by **context transition**, (c) relationship and filter **modifiers**, and (d) **explicit filters** applied last.
* The **order of operations** is critical: explicit filters are *evaluated first* (in the original context) but *applied last* (after context transition and modifiers), enabling them to override earlier steps.

&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
CALCULATE (
    <expression>,
    table[column] = 10
)
 
CALCULATE (
    <expression>,
    FILTER (
        ALL ( table[column] ),
        table[column] = 10
    )
)
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
--  The compact syntax (boolean) is expanded in the full syntax
--  prior to the evaluation
DEFINE
    MEASURE Sales[Red Sales] =
        CALCULATE ( [Sales Amount], 'Product'[Color] = "Red" )
    MEASURE Sales[Red Sales Full] =
        CALCULATE (
            [Sales Amount],
            FILTER ( ALL ( 'Product'[Color] ), 'Product'[Color] = "Red" )
        )
EVALUATE
SUMMARIZECOLUMNS (
    'Product'[Color],
    "Sales Amount", [Sales Amount],
    "Red Sales", [Red Sales],
    "Red Sales Full", [Red Sales Full]
)
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
--  You can use any condition as an argument, as long as it can
--  be converted into a table by the DAX engine
DEFINE
    MEASURE Sales[Red Blue Sales] =
        CALCULATE ( [Sales Amount], 'Product'[Color] IN { "Red", "Blue" } )
    MEASURE Sales[Red Blue Sales Full] =
        CALCULATE (
            [Sales Amount],
            FILTER ( ALL ( 'Product'[Color] ), 'Product'[Color] IN { "Red", "Blue" } )
        )
EVALUATE
SUMMARIZECOLUMNS (
    'Product'[Color],
    "Sales Amount", [Sales Amount],
    "Red Blue Sales", [Red Blue Sales],
    "Red Blue Sales Full", [Red Blue Sales Full]
)
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
--  The KEEPFILTERS modifier does not remove an existing filter
DEFINE
    MEASURE Sales[Red Blue Sales Keepfilters] =
        CALCULATE (
            [Sales Amount],
            KEEPFILTERS ( 'Product'[Color] IN { "Red", "Blue" } )
        )
    MEASURE Sales[Red Blue Sales] =
        CALCULATE (
            [Sales Amount],
            'Product'[Color] IN { "Red", "Blue" }
        )
EVALUATE
SUMMARIZECOLUMNS (
    'Product'[Color],
    "Sales Amount", [Sales Amount],
    "Red Blue Sales", [Red Blue Sales],
    "Red Blue Sales Keepfilters", [Red Blue Sales Keepfilters]
)
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
--  When CALCULATE is executed in a row context, it transforms
--  the row contexts in equivalent filter contexts
DEFINE
    MEASURE Sales[Yearly Avg] =
        AVERAGEX (
            VALUES ( 'Date'[Calendar Year] ),
            CALCULATE (
                SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )
            )
        )
EVALUATE
SUMMARIZECOLUMNS (
    'Product'[Color],
    "Sales Amount", [Sales Amount],
    "Yearly Avg", [Yearly Avg]
)
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
--  CALCULATE is implicitly added to any measure reference
DEFINE
    MEASURE Sales[Sales Amount] =
        SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )
    MEASURE Sales[Yearly Avg] =
        AVERAGEX (
            VALUES ( 'Date'[Calendar Year] ),
            CALCULATE (
                SUMX ( Sales, Sales[Quantity] * Sales[Net Price] )
            )
        )
    MEASURE Sales[Yearly Avg 2] =
        AVERAGEX (
            VALUES ( 'Date'[Calendar Year] ),
            [Sales Amount]
        )
EVALUATE
SUMMARIZECOLUMNS (
    'Product'[Color],
    "Sales Amount", [Sales Amount],
    "Yearly Avg", [Yearly Avg],
    "Yearly Avg 2", [Yearly Avg 2]
)
```
&nbsp;
<hr style="border: none; border-top: 3px solid black;">  

**Examples**  
``` DAX
--  CALCULATE evaluation steps:
--      1. Evaluation of filter arguments
--      2. Context transition
--      3. Evaluation of CALCULATE modifiers
--      4. Application of filter arguments and KEEPFILTERS
DEFINE
    MEASURE Sales[Test] =
        AVERAGEX (
            VALUES ( 'Date'[Calendar Year] ),
            CALCULATE (
                [Sales Amount],
                'Product'[Category] = "Audio",
                KEEPFILTERS ( 'Product'[Color] IN { "Red", "Blue" } ),
                USERELATIONSHIP ( Sales[Delivery Date], 'Date'[Date] )
            )
        )
EVALUATE
SUMMARIZECOLUMNS (
    'Product'[Color],
    "Sales Amount", [Test]
)
```
