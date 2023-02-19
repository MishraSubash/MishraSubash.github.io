---
layout: post
title: Calculating Running Total In Power Query and DAX

---

A running total, or cumulative sum, is a set of partial sums that changes as more data is collected. Each calculation represents the sum of values up to that point. It is the summation of a sequence of numbers which is updated each time a new number is added to the sequence, by adding the value of the new number to the previous running total.

Calculating running total using M-Language is tricky and comparatively harder to compile. Here I am going to demonstrate calculating rolling total in M-Language (Power Query) and DAX.
 

### **Rolling total in Power Query**
Before computing rolling sum, make sure you sort the data in your preferred (Ascending/Descending) order. Adding an index column will improve the performance if you're working in a larger table.
Sort table and add index to the table. Indexing a big table will improve performance. Then add a new blank query.
 
<img src="/img/runningTotal.jpg" width="600px" style="border: 1px solid #ee6e73;"/>

Open Blank query from "Advanced Editor" and replace the code by

```
(tbl as table, sumcolumn as text, rowindex as number) =>
let
    #"Removed Other Columns" = Table.SelectColumns(tbl,{sumcolumn, "Index"}),
    #"Filtered Rows" = Table.SelectRows(#"Removed Other Columns", each [Index] <= rowindex),
    #"Renamed Columns" = Table.RenameColumns(#"Filtered Rows",{{sumcolumn, "Temp"}}),
    #"Grouped Rows" = Table.Group(#"Renamed Columns", {}, {{"RunningTotal", each List.Sum([Temp]), type number}}),
    RunningTotal = Record.Field(#"Grouped Rows"{0},"RunningTotal")
in
    RunningTotal
```

Save above function as ```fnRollingSum```

Go back to the original table and add following steps in advance editor. 

```
    #"Added Custom" = Table.AddColumn(#"Added Index", "Running Total", each fnRollingSum(#"Added Index","Amount",[Index])),
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Custom",{{"Running Total", Int64.Type}})
in
    #"Changed Type1"
```

You want to replace paramenters of ```fnRollingSum``` based on your data. 
for instance, 
- ```#"Added Index"``` : previous step of a table 
- ```Amount```: Column you want to compute running total of
- ```[Index]```: Index column [Index clolumn will inprove performance]

### **Rolling Total in DAX**

```
Rolling Tolal = 
// Running total
// Works even if you have separate DIM Date table.
IF (
    MIN ( 'DataTable'[Date] )
        <= CALCULATE (
            MAX ( 'DataTable'[Date] ),
            ALL ( 'DataTable' )
        ),
    CALCULATE (
        SUM ( 'DataTable'[Amount] ),
        FILTER (
            ALL ( 'DataTable'[Date] ),
            'DataTable'[Date]
                <= MAX ( ( 'DataTable'[Date] ) )
        )
    ),
    BLANK ()
)
```

Download pbix file. [Rolling_Total.pbix](https://github.com/MishraSubash/MishraSubash.github.io/blob/main/support/Running%20Total%20in%20PQ%20and%20DAX.pbix?raw=true)
