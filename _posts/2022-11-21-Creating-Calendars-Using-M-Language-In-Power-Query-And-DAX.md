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
	        "Week",DATE(YEAR([Date]),MONTH([Date]),DAY([Date]))-(WEEKDAY([Date],1)-1),
	        "Week Name", "Week of " & DATE(YEAR([Date]),MONTH([Date]),DAY([Date]))-(WEEKDAY([Date],1)-1),
	        "Week of Year", WEEKNUM([Date],1),
	        "Month", DATE(YEAR([Date]),MONTH([Date]),1),
	        "Month Name", FORMAT([Date],"MMMM"),
	        "Month of Year", MONTH([Date]),
	        "Month_Year", MONTH([Date]) &"_" & YEAR([Date]),
	        "Month Year Name", FORMAT([Date],"MMM") & " " & YEAR([Date]),
	        "Month Year Name Sort",(100*YEAR([Date])+MONTH([Date])),
	        "Quarter",DATE(YEAR([Date]),SWITCH(ROUNDUP(DIVIDE(MONTH([Date]),3,1),0),1,1,2,4,3,7,4,10),1),
	        "Quarter Name", "Q" & ROUNDUP(MONTH([Date])/3,0),
	        "Quarter Year Name", "Q" & ROUNDUP(MONTH([Date])/3,0) & " " & YEAR([Date]),
	        "Year",YEAR([Date])
	    )
```
### **Create Calendar in M-Language in Power Query**
```let
    Source = List.Dates,
    InvokeDates = Source(#date(2019, 01, 01), Duration.Days(DateTime.Date(DateTime.FixedLocalNow())- #date(2019, 01, 01))+1, #duration(1, 0, 0, 0)),
    #"List to table" = Table.FromList(InvokeDates, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    Date = Table.RenameColumns(#"List to table",{{"Column1", "Date"}}),
    Day = Table.AddColumn(Date, "DayNo", each Text.PadStart(Number.ToText(Date.Day([Date])),2,"0")),
    ShortWeek = Table.AddColumn(Day, "Day Name", each Date.ToText([Date],"ddd","en-US")),
    #"Month No" = Table.AddColumn(ShortWeek, "Month No", each Date.Month([Date])),
    MonthName = Table.AddColumn(#"Month No", "Month Name", each Date.ToText([Date],"MMM","en-us")),
    Quarter = Table.AddColumn(MonthName, "Quarter No", each Date.QuarterOfYear([Date])),
    ExtractYear = Table.AddColumn(Quarter, "Year", each Date.Year([Date])),
    MonthNumber_Year = Table.AddColumn(ExtractYear, "Month_Year", each Text.Combine({Date.ToText([Date], "%M"), "_", Date.ToText([Date], "yyyy")}), type text),
    #"Year Quarter Added" = Table.AddColumn(MonthNumber_Year, "Year-Quarter", each Number.ToText([Year]) & "Q"& Number.ToText([Quarter No],"00")),
    #"Change type to text" = Table.TransformColumnTypes(#"Year Quarter Added",{{"Year", Int64.Type}, {"Date", type date}, {"Month No", Int64.Type}, {"Day Name", type text}, {"Month Name", type text}, {"Quarter No", Int64.Type}, {"Year-Quarter", type text},   {"DayNo", type text} }),
    #"Sort DayName" = Table.AddColumn(#"Change type to text", "DayName", each Date.DayOfWeek([Date],1)),
    YearMonth = Table.AddColumn(#"Sort DayName", "YearMonth", each Number.ToText([Year])&Text.PadStart(Number.ToText([Month No]),2,"0")),
    YearQuarter = Table.AddColumn(YearMonth, "YearQuarter", each Number.ToText([Year])&Text.PadStart(Number.ToText([Quarter No]),2,"0")),
    #"Changed Type" = Table.TransformColumnTypes(YearQuarter,{{"DayName", Int64.Type}, {"YearMonth", type text}, {"YearQuarter", type text}})
in
    #"Changed Type"
```
Download .pbix file [Calendar.pbix](https://github.com/MishraSubash/MishraSubash.github.io/blob/main/support/Calendar.pbix?raw=true)
