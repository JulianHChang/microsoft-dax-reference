
## Putting It Together (mini patterns)

**% of Category Total Within Region**

```DAX
Sales % of Category in Region :=
DIVIDE (
    [Total Sales],
    CALCULATE ( [Total Sales], ALLEXCEPT ( Products, Products[Category] ), ALLEXCEPT ( Customers, Customers[Region] ) )
)
```

**Top 5 Subcategories by Profit (dynamic)**

```DAX
Top N Subcats Profit :=
VAR N = SELECTEDVALUE ( 'Selectors'[TopN], 5 )
VAR t =
    TOPN ( N, VALUES ( Products[Subcategory] ), [Profit], DESC )
RETURN
SUMX ( t, [Profit] )
```

**12-Month Rolling Sales**

```DAX
Sales 12M Rolling :=
VAR StartDate = MIN ( Calendar[Date] )
RETURN
CALCULATE ( [Total Sales], DATESINPERIOD ( Calendar[Date], MAX ( Calendar[Date] ), -12, MONTH ) )
```

**YoY Rank of Customers**

```DAX
Customer Rank YoY Sales :=
VAR SalesCY = [Total Sales]
VAR SalesPY = CALCULATE ( [Total Sales], DATEADD ( Calendar[Date], -1, YEAR ) )
VAR YoY     = DIVIDE ( SalesCY - SalesPY, SalesPY )
RETURN
RANKX ( ALL ( Customers[CustomerID] ), YoY, , DESC )
```

---