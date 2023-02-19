---
layout: post
title: VLOOKUPs Equivalent In DAX
---

## **RELATED and different types of LOOKUPVALUE () functions in DAX**

The LOOKUPVALUE and RELATED functions in DAX (Data Analysis Expressions) are used to retrieve values from a table based on a specified condition.

The RELATED function, on the other hand, retrieves a related value from another table that is connected to the current table through a relationship. The syntax for the RELATED function is: RELATED(<related_column_name>). It is used to return the value of a column from a related table based on an established relationship between the two tables. The function is especially useful when working with data from multiple related tables and you need to retrieve a value from one of those tables.

The LOOKUPVALUE function allows you to look up a single value in a table based on one or more conditions. The function returns the value in the specified column that corresponds to the first matching condition. The syntax for LOOKUPVALUE is: LOOKUPVALUE(<result_column_name>, <search_column_name>, <search_value>, [<search_column_name2>, <search_value2>, ...]).

In summary, both LOOKUPVALUE and RELATED functions are useful for retrieving values from a table based on specific conditions, with the difference being that LOOKUPVALUE looks up values based on one or more conditions, while RELATED retrieves values from a related table based on an established relationship
We are going to explore some use cases of RELATED() and LOOKUPVALUE() function in this article. 
Please refer to this PBIX file for hands-on example.
We use the following model as an example.

<img src="/img/Vlookup_FirstTable.jpg" width="600px" style="border: 1px solid #ee6e73;"/>


- Calculated Column in the FactData table. 
                   ```
                    CurrencyIndex = RELATED(CurrDim[ID])
```

Imagine there is no active relationship or relationship cannot be defined between tables but need to look up value from another tables. LOOKUPVALUE() function comes into play. 

- Regular LOOKUPVALUE() function which is also equivalent to VLOOKUP() in Excel.
```
Regular LOOKUPVALUE() 
Country =
LOOKUPVALUE (
    CurrDim[CountryName],
    CurrDim[CurrencyID], FactData[Currency]
)
```
- LOOKUPVALUE with SUM() function 
```
TotalAmount = 
VAR Output_value =
    CALCULATE (
        SUM ( FactData[Amount] ),
        FILTER (
            'FactData',
            FactData[Currency] = CurrDim[CurrencyID]
        )
    )
RETURN
    IF (
// Replace blank cell with 0
        ISBLANK ( Output_value ),
        0,
        Output_value
    )
```

- Using LOOKUPVALUE () to pull first value from another table.
```
FirstValue =
CALCULATE (
    FIRSTNONBLANK (
        FactData[Amount],
        1
    ),
    FILTER (
        ALL ( FactData ),
        FactData[Currency] = CurrDim[CurrencyID]
    )
)
```

Downlaod pbix file here [LOOKUPVALUE.pbix](https://github.com/MishraSubash/MishraSubash.github.io/blob/main/support/LOOKUPVALUE.pbix?raw=true)
