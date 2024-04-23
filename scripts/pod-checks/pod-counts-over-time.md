# [Script Name]
Pod Counts Over Time

## Description
Count the number of pods over a given time period and potentially namespace

## Use Case
This was used to demonstrate the impacts of horizontal pod autoscaling finetuning. Likely to be useful in the case of anything relating to numbers of pods (e.g. excessive restarts etc)

## Prerequisites
None

## KQL Script
```kusto
union 
workspace("hmcts-prod").KubePodInventory,
workspace("hmcts-nonprod").KubePodInventory,
KubePodInventory
| where TimeGenerated > ago(1d)
| where Namespace in ("your","namespaces", "here")
| summarize PodCount = dcount(PodUid) by Namespace, bin(TimeGenerated, 30m)
| order by TimeGenerated, Namespace

```

## Author
Danny Furnivall (@Danny on slack)
