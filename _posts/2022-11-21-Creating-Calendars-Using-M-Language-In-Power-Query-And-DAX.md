---
layout: post
title: Working with Calendar in Power BI
#subtitle:
---

A calendar table is a fundamental and essential component of a Power BI data model. Its main purpose is to provide a comprehensive and consistent date range, enabling efficient time-based analysis and reporting within Power BI. By having a calendar table, users can easily visualize and understand trends, patterns, and performance over time. It allows for seamless date-based filtering, grouping, and aggregations, making it simpler to generate insightful visuals and measures. Additionally, the calendar table simplifies the creation of custom date hierarchies, such as year, quarter, month, and day, ensuring users can easily drill down or roll up data as needed. Overall, a calendar table forms the backbone of time-intelligent data analysis, offering a crucial foundation for time-based reporting and enhancing the effectiveness of Power BI dashboards and reports.

There are several different methods of creating a calendar in Power BI. In this article, we are exploring specifically two ways to create a calendar table in the Power BI data model using DAX and M-Language.
 

### **Create calendar using DAX**
```
DIM_Calendar = 
	    ADDCOLUMNS(
	        CALENDAR(DATE (2021, 1, 1), DATE (2022, 12, 31)),
	        "Day Name",FORMAT([Date],"DDDD"),
	        "Day of Week",(WEEKDAY([Date],1)),
	        "Day of Month",DAY([Date]),
	        "Week", DATE(YEAR([Date]),MONTH([Date]),DAY([Date]))-(WEEKDAY([Date],1)-1),
	        "Week Name", "Week of " & DATE(YEAR([Date]),MONTH([Date]),DAY([Date]))-(WEEKDAY([Date],1)-1),
	        "Week of Year", WEEKNUM([Date],1),
	        "Month", DATE(YEAR([Date]),MONTH([Date]),1),
	        "Month Name", FORMAT([Date],"MMMM"),
	        "Month of Year", MONTH([Date]),
	        "Month_Year", MONTH([Date]) &"_" & YEAR([Date]),
	        "Month Year Name", FORMAT([Date],"MMM") & " " & YEAR([Date]),
	        "Month Year Name Sort", (100*YEAR([Date])+MONTH([Date])),
	        "Quarter", DATE(YEAR([Date]),SWITCH(ROUNDUP(DIVIDE(MONTH([Date]),3,1),0),1,1,2,4,3,7,4,10),1),
	        "Quarter Name", "Q" & ROUNDUP(MONTH([Date])/3,0),
	        "Quarter Year Name", "Q" & ROUNDUP(MONTH([Date])/3,0) & " " & YEAR([Date]),
	        "Year", YEAR([Date])
	    )
```
### **Create Calendar in M-Language in Power Query**
Change Start Date as needed on the `Source` step. By default, it returns start date from Jan 1st 2019. 
Download or Open file in browser: [Calendar.txt](https://raw.githubusercontent.com/MishraSubash/MishraSubash.github.io/main/support/Calendar%20in%20M-Languge%20for%20PQ.txt)
