---
layout: post
title: Reference a specific step from another query in Power BI
# subtitle: 
---

Power Query Editor is a powerful tool that allows users to extract, transform, and load data. One of its key features is the ability to reference each step as a table in Power Queries, which can simplify complex data processing workflows. In this article, we will walk through how to reference an intermediate step from one query to another in Power Query Editor.

Imagine you have a table with numerous transformations in Power Query, but you want to reference a step that sits within a query to another query.


<img src="/img/Intermediate_ReferenceStep.jpg" width="550px" style="border: 1px solid #ee6e73;"/>

Let’s say we want to reference “RefStep” table data in another query in the same Power BI report. One solution would be splitting up the main query into two and reference the split to another query. But that technique is not ideal in some instances, and it would degrade the query performance.

A better solution is to add a metadata step next to the “FinalStep” and call that meta step from another query.
 

<img src="/img/Intermediate_DataTable.jpg" width="550px" style="border: 1px solid #ee6e73;"/>


The “meta” expression stores the metadata of reference step (“RefStep”) which you can call from any query.  
Assume we have another query from where we would like to fetch data from “RefStep” step from main query, all we need to do is to reference the “StepToFetch” variable.
 

<img src="/img/intermediate_fetchStep.jpg" width="550px" style="border: 1px solid #ee6e73;"/>

### Append a step from another query 

Assume you want to append intermediate step (“RefStep” from DataTable table) from one query to another query. Here’s how you easily append any intermediate step with another query or as a new query.

<img src="/img/Intermediate_finalStep.jpg" width="550px" style="border: 1px solid #ee6e73;"/>

Download PBIX file [Reference_A_Step.pbix](https://github.com/MishraSubash/MishraSubash.github.io/blob/main/support/ReferenceStep_To_AnotherQuery.pbix?raw=true)
