# Find commonly logged strings

## Description
This script allows the user to find the top 50 most common substrings in the last month of logs.

## Use Case
This was initially used to reduce the costs of logging on Employment Tribunals but it turned into quite a useful script for finding particularly weird behaviour elsewhere. 
When first run on CCD, it spotted a single user account that retrieved its user info 5.4 million times in five days.

To amend it for your own team, simply replace 'et-prod' with your own service's app insights instance.

## Prerequisites
None

## KQL Script
```kusto
app("et-prod").traces
| where timestamp >= startofmonth(now())-30d and timestamp < startofmonth(now())
| parse kind=regex message with * " " Substring: string " " *
| where strlen(Substring) >= 10
| summarize count() by Substring
| top 50 by count_ desc
```

## Author
Danny Furnivall (@Danny on Slack)
