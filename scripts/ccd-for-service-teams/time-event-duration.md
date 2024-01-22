# Get event timings from CCD

## Description
This script allows you to get the length of time between two events occurring on a case based on eventId.

## Use Case
This script can be used to find the duration of particular events on CCD (e.g. how long it takes to move through a form).
Query 1 is the first event (in this case, the writeFinalDecision event for SSCS), and Query 2 is the second one (in this case, issueFinalDecision).
If you want to work out the average length of time that users take to complete a given flow, this is a way of inferring this directly from CCD.

## Prerequisites
Prod access to app insights if using there.

## KQL Script
```kusto
let Query1 = requests
    | where name endswith "writeFinalDecision"
    | where timestamp between(datetime(2024-01-16) .. datetime(2024-01-17))
    | extend caseId = tostring(extract("/cases/(\\d+)/event-triggers/", 1, name))
    | summarize MaxTimestamp = max(timestamp) by caseId
    | project caseId, timestamp1 = MaxTimestamp;
let Query2 = requests
    | where name endswith "issueFinalDecision"
    | where timestamp between(datetime(2024-01-16) .. datetime(2024-01-17))
    | extend caseId = tostring(extract("/cases/(\\d+)/event-triggers/", 1, name))
    | summarize MinTimestamp = min(timestamp) by caseId
    | project caseId, timestamp2 = MinTimestamp;
Query1
    | join kind=inner Query2 on caseId
    | extend TimeDifference = timestamp2 - timestamp1
    | where timestamp1 < timestamp2

```
**important**: This query can also be used to find the average of all cases, by adding something along the lines of the following to the final query:
```kusto
    | summarize AvgTimeDifference = avg(TimeDifference / 1m)
```

## Author
Danny Furnivall (@Danny on slack)
