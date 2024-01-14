# Find commonly logged strings

## Description
This script allows the user to find the top 50 most common substrings in the last month of logs.

## Use Case
This was initially used to reduce the costs of logging on Employment Tribunals but it turned into quite a useful script for finding particularly weird behaviour elsewhere (in this case, CCD). 
When first run, it spotted a single user account that retrieved its user info 5.4 million times in five days on CCD.
To amend it for your own team, simply replace 'ccd-prod' with your own service's app insights instance.

## Prerequisites
None

## KQL Script
```kusto
union traces, app("ccd-prod").traces
| where timestamp >= startofmonth(now())-30d and timestamp < startofmonth(now())
| parse kind=regex message with * " " Substring: string " " *
| where strlen(Substring) >= 10
| summarize count() by Substring
| top 50 by count_ desc
```

## Author
Danny Furnivall (@Danny on Slack)
