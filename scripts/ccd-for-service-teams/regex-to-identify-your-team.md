# Identifying your own service in CCD logs

## Description
CCD's logs are so enormous it is notoriously difficult to find data relating to your own service. 
This script uses multiple regexes to find case ids and operation ids for a sequence wherein a 502 is returned at some point in a series of CCD events for a specific team (in this case Civil).

## Use Case
This script can be modified to capture any undesirable behaviour within a chain of events in CCD. Simply alter the regexes and other conditions to find your specific scenario.

## Prerequisites
This is pointed at the prod CCD instance but you could do the same with any environment.

## KQL Script
```kusto
let Query1 = requests
    | where name contains "/event-triggers/CREATE_SDO"
    | extend caseId = tostring(extract("/cases/(\\d+)/event-triggers/", 1, name))
    | project caseId, operation_Id;
let Query2 = requests
    | where resultCode == 502
        and name endswith "events" 
        and name startswith "POST /data/cases/"
    | extend caseId = tostring(extract("/cases/(\\d+)/events", 1, name)) 
    | project caseId, operation_Id;
Query1
| join kind=inner Query2 on operation_Id
| project caseId, operation_Id
```

## Author
Danny Furnivall (@Danny on slack)
