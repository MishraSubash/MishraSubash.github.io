---
layout: post
title: Working with Calendar in Power BI
#subtitle:
---

Creating a calendar in Power BI is an important step when working with time intelligence functions and cross join the fact table with the calendar to forward fill values to gain daily granularity. A calendar table is essentially a table that contains a complete set of dates for a given time period and is typically used as the basis for time-related calculations in Power BI.

You can use the time intelligence functions in Power BI, such as TOTALYTD or SAMEPERIODLASTYEAR, to perform calculations based on the dates in the calendar table.

There are several different methods of creating a calendar in Power BI. In this article, we are exploring specifically two ways to create a calendar using DAX and M-Language in Power Query.
 

### **Create calendar using DAX**
```DIM_Calendar = 
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
Download this txt file for Calendar in M-Langugae for PQ: [Calendar.txt](https://raw.githubusercontent.com/MishraSubash/MishraSubash.github.io/main/support/Calendar%20in%20M-Languge%20for%20PQ.txt)


