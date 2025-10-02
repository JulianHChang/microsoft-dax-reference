# ALL

* **Purpose:** Returns all distinct values of specified columns or all rows of a table; as a CALCULATE modifier, behaves like removing direct filters.
* **Key behavior:** Removes **direct** filters on referenced columns/tables but **does not** clear **cross-filters** coming from related columns.
* **Expanded table effect:** Operates on the expanded table; `ALL(Sales)` clears filters across all model tables included in Sales’ expansion.
* **No-argument form:** `ALL()` with no arguments removes filters across the entire model.

# ALLEXCEPT

* **Purpose:** As a table function, returns all distinct values of a table’s columns **except** those listed. As a CALCULATE modifier, removes filters like `ALL` but **preserves** filters on the specified columns.
* **Important nuance:** `ALLEXCEPT` only **removes** filters. This differs from `ALL/VALUES` interplay where `ALL` removes filters and `VALUES` can reapply context via cross-filtering. The distinction affects how context is restored or retained.

# ALLNOBLANKROW

* **Purpose:** Same as `ALL` but excludes the **automatic blank row** introduced to repair invalid relationships.
* **Blank handling:** May still return blank rows that actually exist in the data; only the engine-added repair row is excluded.
* **As CALCULATE modifier:** Replaces current filters with a filter that **only removes the automatic blank row**, so columns effectively filter out just the blank value.

# ALLSELECTED

* **Purpose:** As a table function, returns values as defined by the **last shadow filter context**; as a CALCULATE modifier, **restores** that shadow context.
* **Multiple columns:** If columns appear in different shadow contexts, each column takes its own **most recent** shadow context.

# ALLCROSSFILTERED

* **Scope:** **Only** a CALCULATE modifier; accepts a **table** argument (not a column) and is not a table function.
* **Effect:** Clears filters on the table’s **expanded** structure **and** removes filters propagated via **bidirectional cross-filters** on relationships directly or indirectly connected to that expansion, going beyond what `ALL` alone removes.  

&nbsp;

&nbsp;

&nbsp;
&nbsp;  


| Function | Table function | CALCULATE modifier |
| --- | --- | --- |
| ALL | Returns all the distinct values of a column or of a table. | Removes any filter from columns or expanded tables. It never adds a filter; it only removes them if present. |
| ALLEXCEPT | Returns all the distinct values of a table, ignoring filters on some of the columns of the expanded table. | Removes filters from an expanded table, except from the columns (or tables) passed as further arguments. |
| ALLNOBLANKROW | Returns all the distinct values of a column or table, ignoring the blank row added for invalid relationships. | Removes any filter from columns or expanded tables; also adds a filter that only removes the blank row. Thus, even if there are no filters, it actively adds one filter to the context. |
| ALLSELECTED | Returns the distinct values of a column or a table, as they are visible in the last shadow filter context. | Restores the last shadow filter context on tables or columns, if a shadow filter context is present. Otherwise, it does not do anything. It always adds filters, even in the case where the filter shows all the values. |
| ALLCROSSFILTERED | Not available as a table function. | Removes any filter from an expanded table, including also the tables that can be reached directly or indirectly through bidirectional cross-filters. ALLCROSSFILTERED never adds a filter; it only removes filters if present. |