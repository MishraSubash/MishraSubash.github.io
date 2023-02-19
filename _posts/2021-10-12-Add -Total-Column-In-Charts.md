## **Adding a new "Total" column in Power BI Charts**

Adding a total column in column or bar chart in Power BI without downloading custom visuals is quite tricking and intimidating. This seems like a pretty easy task to do but in reality, that is not the case. In this article, I will walk you though with an example to add a total column in column/bar chart.
Let’s take an simple example, assume we have the following one column data.
 
<img src="/img/total_datatable.jpg" width="200px" style="border: 1px solid #ee6e73;" />


### Step 1: Create a New Table using DAX to add a new row “Total” (name as you want)
```
GroupName = 
UNION (
    VALUES ( 'DataTable'[Group] ),
    ROW (
        "Group", "Total"
    )
)
```

### Step 2: Create following two measures.
*Replace COUNTX by SUMX (of course need to update SUMX arguments) if you want to sum column values*
```
MeasureOne = 
COUNTX (
    'DataTable',
    'DataTable'[Group]
)
```

AND 

```
Count of Each Group = 
VAR Total =
    CALCULATE (
        [MeasureOne],
        ALL ( 'DataTable' )
    )
RETURN
    SWITCH (
        SELECTEDVALUE ( 'GroupName'[Group] ),
        "Total", Total,
        [MeasureOne]
    )
```

### Step 3: Establish one to many relationships between tables as shown:
<img src="/img/Total_Relationship.jpg" width="200px" style="border: 1px solid #ee6e73;" />

### Step 5: Drag and drop table or chart visuals. You’re all set!
<img src="/img/Total_graph.jpg" width="200px" style="border: 1px solid #ee6e73;" />


Clink this link to download .pbix file. [Adding_Total_Bar.pbix](https://github.com/MishraSubash/MishraSubash.github.io/blob/main/support/Add_Total_Bar.pbix?raw=true)


