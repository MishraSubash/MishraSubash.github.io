---
layout: post
title: Pivot Table with filters and If conditions using DAX
#subtitle: 
---

Assuming we have a data of bank balances by bank and account type, our goal is to aggregate the balance by BankGroup for each AccountType based on a specific criterion. The criterion involves summarizing the raw data by bank group and account type, and then adding up only the positive balances for each bank group.

<img src="/img/Pivot_RawBankBalance.jpg" width="550px" style="border: 1px solid #ee6e73;" /></center></a>

In order to do that, first we need to pivot data by bankgroup and accountype. Then we only sum positive balance which means replace negative balance by 0 then sum it up. The data would look like this: 

<img src="/img/Pivot_FirstPivot.jpg" width="1200px" style="border: 1px solid #ee6e73;" /></center></a>

Again, re-run one more pivot table to aggregate bank balance by bank group. The final output would be 

<img src="/img/Pivot_FinalOutPut.jpg" width="350px" style="border: 1px solid #ee6e73;" /></center></a>

If you’re following so far, we have understood the problem statement as well as solution workflows. Now, we are going to write a single DAX function to perform pivot table operation as mentioned above. 

### **Step 1: Group raw data by Account Type.**
```
VAR CheckingData =
    FILTER (
        'DataTable',
        'DataTable'[AccountType] = "Checking"
    )
VAR SavingData =
    FILTER (
        'DataTable',
        'DataTable'[AccountType] = "Saving"
    )
```

### **Step 2: Perform Pivot operation for each group and treat negative balance using IF() operation and save it as new VAR.**

```
VAR PositiveCheckingBalance =
    CALCULATETABLE (
        SUMMARIZE (
            'DataTable',
            'DataTable'[BankGroup],
            "Bank Balance",
                IF (
                    SUM ( 'DataTable'[Balance] ) <= 0,
                    0,
                    SUM ( 'DataTable'[Balance] )
                )
        ),
        KEEPFILTERS ( CheckingData )
    ) 

// Sum positive balance for Saving AccountType  
VAR PositiveSavingBalance =
    CALCULATETABLE (
        SUMMARIZE (
            'DataTable',
            'DataTable'[BankGroup],
            "Bank Balance",
                IF (
                    SUM ( 'DataTable'[Balance] ) <= 0,
                    0,
                    SUM ( 'DataTable'[Balance] )
                )
        ),
        KEEPFILTERS ( SavingData )
    )

```

### **Step 3: Append PositiveCheckingBalance and PositiveSavingBalance save appended table as new VAR.**
```
VAR CombineTable =
    // SELECTCOLUMN() function keeps column in specific order
    UNION (
        SELECTCOLUMNS (
            PositiveCheckingBalance,
            "Bank Group", 'DataTable'[BankGroup],
            "Bank Balance", [Bank Balance]
        ),
        SELECTCOLUMNS (
            PositiveSavingBalance,
            "Bank Group", [BankGroup],
            "Bank Balance", [Bank Balance]
        )
    )
```

### **Step 4: Step 4: Finally, Summarize the unioned table by BankGroup and RETURN final output.**
```
RETURN
    SUMMARIZE (
        CombineTable,
        [Bank Group],
        "Bank Balance",
            SUMX (
                FILTER (
                    CombineTable,
                    [Bank Group]
                        = EARLIER ( [Bank Group] )
                ),
                [Bank Balance]
            )
    )
```
The above code will return following output which identical to Pivot Table in Excel. 

<img src="/img/Pivot_DaxFinalOutput.jpg" width="350px" style="border: 1px solid #ee6e73;" /></center></a>

## Here is a complete code in a single DAX: 

```
Non-Negative Bank Balance By Account Type = 
//Separate data by Account Type 
VAR CheckingData =
    FILTER (
        'DataTable',
        'DataTable'[AccountType] = "Checking"
    )
VAR SavingData =
    FILTER (
        'DataTable',
        'DataTable'[AccountType] = "Saving"
    )

// Pivoting Data 
// SUM Bank Balance if balance is positive for each account type. 
// Sum positive balance for Checking AccountType 
VAR PositiveCheckingBalance =
    CALCULATETABLE (
        SUMMARIZE (
            'DataTable',
            'DataTable'[BankGroup],
            "Bank Balance",
                IF (
                    SUM ( 'DataTable'[Balance] ) <= 0,
                    0,
                    SUM ( 'DataTable'[Balance] )
                )
        ),
        KEEPFILTERS ( CheckingData )
    ) 
// Sum positive balance for Saving AccountType  
VAR PositiveSavingBalance =
    CALCULATETABLE (
        SUMMARIZE (
            'DataTable',
            'DataTable'[BankGroup],
            "Bank Balance",
                IF (
                    SUM ( 'DataTable'[Balance] ) <= 0,
                    0,
                    SUM ( 'DataTable'[Balance] )
                )
        ),
        KEEPFILTERS ( SavingData )
    ) 
// Append Calcualted Table and save it as new VAR because 
// we will have to aggregate balance by Bank Group later 
VAR CombineTable =
    // SELECTCOLUMN() function keeps column in specific order
    UNION (
        SELECTCOLUMNS (
            PositiveCheckingBalance,
            "Bank Group", 'DataTable'[BankGroup],
            "Bank Balance", [Bank Balance]
        ),
        SELECTCOLUMNS (
            PositiveSavingBalance,
            "Bank Group", [BankGroup],
            "Bank Balance", [Bank Balance]
        )
    ) 
// Pivot data to aggregate balance by BankGroup
RETURN
    SUMMARIZE (
        CombineTable,
        [Bank Group],
        "Bank Balance",
            SUMX (
                FILTER (
                    CombineTable,
                    [Bank Group]
                        = EARLIER ( [Bank Group] )
                ),
                [Bank Balance]
            )
    )
```
Pivot in Excel [Data_In-Excel.xlsx](https://github.com/MishraSubash/MishraSubash.github.io/blob/main/support/RawData.xlsx?raw=true)
Download PBIX file [Pivot_In_DAX.pbix](https://github.com/MishraSubash/MishraSubash.github.io/blob/main/support/PivotWithDAX.pbix?raw=true)
