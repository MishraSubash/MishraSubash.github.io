---
layout: post
title: Reference a specific step from another query in Power BI
# subtitle: 
---

The Power Query Editor is a robust tool that enables users to extract, transform, and load data. One of its standout features is the ability to reference each step as a table in Power Queries, streamlining complex data processing workflows. 

This article will guide you through referencing an intermediate step from one query to another in the Power Query Editor. Suppose you have a table with numerous transformations in Power Query, but you need to reference a step within a query to another query.


<img src="/img/Intermediate_ReferenceStep.jpg" width="550px" style="border: 1px solid #ee6e73;"/>

Suppose you want to reference the data in the “RefStep” table from another query in the same Power BI report. One solution is to split up the main query into two and reference the split to another query. However, this technique is not ideal in some instances and can degrade query performance. A better solution is to add a metadata step next to the “FinalStep” and call that meta step from another query.
 

<img src="/img/Intermediate_DataTable.jpg" width="1000px" style="border: 1px solid #ee6e73;"/>

The `meta` expression stores the metadata of the reference step (“RefStep”), which can be called from any query. Assume we have another query from which we would like to fetch data from the “RefStep” step from the main query. All we need to do is reference the “StepToFetch” variable we created using the `meta` function.
 

<img src="/img/inter.jpg" width="800px" style="border: 1px solid #ee6e73;"/>

### Append a step from another query 

Assume you want to append intermediate step (“RefStep” from DataTable table) from one query to another query. Here’s how you easily append any intermediate step with another query or as a new query.

<img src="/img/final.jpg" width="800px" style="border: 1px solid #ee6e73;"/>

Download PBIX file [Reference_A_Step.pbix](https://github.com/MishraSubash/MishraSubash.github.io/blob/main/support/ReferenceStep_To_AnotherQuery.pbix?raw=true)
