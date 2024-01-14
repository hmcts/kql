# Find Odd Behaviour from Foreign Countries

## Description
This script was used to identify some odd-looking behaviour from countries which are unlikely to be genuine users.

## Use Case
This allowed us to isolate some genuinely dodgy requests to the Employment Tribunals service (as well as some innocuous but dodgy-looking ones!).
Nepal and Indonesia are just examples, but this could be used for any purpose where country of request matters.

## Prerequisites
None

## KQL Script
```kusto
union traces, exceptions, requests
| where timestamp > ago(90d) and client_CountryOrRegion == "Nepal" or client_CountryOrRegion == "Indonesia"
```

## Author
Danny Furnivall (@Danny on Slack)
