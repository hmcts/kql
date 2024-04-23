# [Script Name]
Memory Checking over Time

## Description
This script allows us to investigate memory (or other pod resources) over a given time frame.

## Use Case
This was used to demonstrate a memory leak within a particular service

## Prerequisites
You'll need to be in a specific loganalytics workspace that includes kubernetes pods (e.g. hmcts-prod or hmcts-nonprod)

## KQL Script
```kusto
union
workspace("hmcts-prod").Perf,
workspace("hmcts-nonprod").Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryWorkingSetBytes"
| where InstanceName contains "<your-pod-here>"
| summarize AvgMemoryUsageMB = avg(CounterValue) by InstanceName, bin(TimeGenerated, 1h)
| order by TimeGenerated asc
```

## Author
Danny Furnivall (@Danny on slack)
