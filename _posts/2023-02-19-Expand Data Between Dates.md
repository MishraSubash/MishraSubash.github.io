---
layout: post
title: How to expand data rows between dates by each day in Power Query?
#subtitle: 
---

Expanding data rows between dates by each day in Power Query is a useful technique for analyzing time-based data. In this article, we are exploring couple different techniques to expand data rows by each day in Power Query.

**Condition1:** 
Imagine, we have data table without date column, but you want to expand data between custom data ranges. 


<img src="/img/Table_Fig_1.jpg" width="550px" style="border: 1px solid #ee6e73;"/>

Step 1: Create two date functions in which you want to expand data between. (Function is not required if you want to embed dates in M-code). For this example, I am going to create following two date functions: 
a.	First Day of Previous Month: 
```
(FirstDayOfPreviousMonth as date) =>
let
    #"First Day of previous Month" = Date.StartOfMonth(Date.AddMonths(DateTime.Date(DateTime.LocalNow()), -1))
in
    #"First Day of previous Month"
```
b.	Last Day of Previous Month:

```
(LastDayOfPreviousMonth as date) =>
let
    #"Last Day of Previous Month" = Date.EndOfMonth(Date.AddMonths(DateTime.Date(DateTime.LocalNow()), -1))
in
   #"Last Day of Previous Month"
```
Save Function a as FirstDayofPreviousMonth and function b as LastDayOfPreviousMonth
Step 2: Add new column as Date and paste following code in formula box 
```	
 List.Transform({
    Number.From(
        FirstDayofPreviousMonth(#date(2023,2,2))).. 
    Number.From(
        LastDayOfPreviousMonth(#date(2023,2,2)))}, 
    each  Date.From(_)
              )
```
IMPORTANT: Date inside the `#date()` function is just a format parameter. Regardless of what value you supply, function will generates dates based on original function.


<img src="/img/Custom_Col_Fig_2.jpg" width="1200px" style="border: 1px solid #ee6e73;"/>

Step3: Expand lists values as “Expand to New Rows”

<img src="/img/Expand_as_rows_between_dates_gig_3.jpg" width="350px" style="border: 1px solid #ee6e73;"/>

**Condition 2:** 
Assume, a following data table. Make sure both dates columns are in date format

<img src="/img/DataTable_Fig_4.jpg" width="350px" style="border: 1px solid #ee6e73;"/>

Next, Add a new custom column and give an appropriate name. I am going to name it as “Date” and paste following code in formula bar. 
```{ Number.From([StartDate])..Number.From([EndDate])}```

<img src="/img/Custom_Col_fig_5.jpg" width="350px" style="border: 1px solid #ee6e73;"/>

Above step will add a Date column with lists value. Expand column selecting “Expand to New Rows”
<img src="/img/Final_Output_Fig_6.jpg" width="350px" style="border: 1px solid #ee6e73;"/>

This will expand original into rows for each date between StartDate and EndDate. Make sure you format date value to Date data type. 

Bonus: 
Asumme you have a data that has maturity date and need to expand date a day before maturity date. You will need to subtract no of days from the EndDate (Maturity Date) as shown in the below code. 
```{ Number.From([StartDate])..Number.From([EndDate])-1}```

Download PBIX file [ExpandDataBetweenDates.pbix](https://github.com/MishraSubash/MishraSubash.github.io/blob/main/support/ExpandDataBetweenDates.pbix?raw=true)
