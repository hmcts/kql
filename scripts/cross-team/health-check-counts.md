# Healthcheck Counts

## Description
These scripts allow a user to look across multiple teams' app insights instances and get a sum of each team's healthcheck counts.

## Use Case
This was used as part of a cross-HMCTS cluster performance analysis. It found multiple instances of redundant or excessive chained liveness/readiness checks, which may have impacted overall cluster performance. 

Script 1 aggregates the count by service, whereas Script 2 aggregates at both service and pod level. Script 2 allowed us to look specifically at which pods were being hit most (in this case, this generally indicated a problem)

It's unlikely that this particular use case will appear again, but there may be some reusable syntax for other analyses, particularly cross-team ones.

## Prerequisites
Access to production app insights is required to run the script as-is but if environments are altered to lower ones, it should be usable by all.

## KQL Script 1 - overall counts
![image](https://github.com/hmcts/kql/assets/18507008/2b8f1465-12c1-41d8-9a70-c16888b97369)

```kusto
union 
    (app('probate-prod').requests | extend appName = "probate"),
    (app('et-prod').requests | extend appName = "et"),
    (app('sscs-prod').requests | extend appName = "sscs"),
    (app('ia-prod').requests | extend appName = "ia"),
    (app('fpl-case-service-appinsights-prod').requests | extend appName = "fpl"),
    (app('finrem-prod').requests | extend appName = "finrem"),
    (app('cmc-prod').requests | extend appName = "cmc"),
    (app('civil-citizen-ui-prod').requests | extend appName = "civil-citizen-ui"),
    (app('civil-service-prod').requests | extend appName = "civil-service"),
    (app('sptribs-appinsights-prod').requests | extend appName = "sptribs"),
    (app('nfdiv-appinsights-prod').requests | extend appName = "nfdiv-appinsights"),
    (app('div-prod').requests | extend appName = "div"),
    (app('xui-webapp-appinsights-prod').requests | extend appName = "xui-webapp"),
    (app('xui-ao-webapp-appinsights-prod').requests | extend appName = "xui-ao"),
    (app('xui-mo-webapp-appinsights-prod').requests | extend appName = "xui-mo"),
    (app('em-prod').requests | extend appName = "em"),
    (app('rd-prod').requests | extend appName = "rd"),
    (app('ccd-prod').requests | extend appName = "ccd"),
    (app('reform-scan-prod').requests | extend appName = "bulkscan"),
    (app('rpe-pdf-service-appinsights-prod').requests | extend appName = "pdfservice"),
    (app('draft-store-service-appinsights-prod').requests | extend appName = "draftstore"),
    (app('wa-prod').requests | extend appName = "work-allocation"),
    (app('ethos-prod').requests | extend appName = "ethos/ecm"),
    (app('prl-appinsights-prod').requests | extend appName = "prl"),
    (app('fact-appinsights-prod').requests | extend appName = "fact")
| where timestamp > ago(1h)
| where name contains "GET /health"
| summarize HealthCheckCount = sum(iif(name contains "GET /health", 1, 0)) by appName
| project appName, HealthCheckCount
| order by appName
```


## KQL Script 2 - Counts by pod instance
![Pasted image 20240105211226](https://github.com/hmcts/kql/assets/18507008/09aa9cc5-5395-45c4-95fb-f2c7f9a6e946)

```kusto
union 
    (app('probate-prod').requests | extend appName = "probate"),
    (app('et-prod').requests | extend appName = "et"),
    (app('sscs-prod').requests | extend appName = "sscs"),
    (app('ia-prod').requests | extend appName = "ia"),
    (app('fpl-case-service-appinsights-prod').requests | extend appName = "fpl"),
    (app('finrem-prod').requests | extend appName = "finrem"),
    (app('cmc-prod').requests | extend appName = "cmc"),
    (app('civil-citizen-ui-prod').requests | extend appName = "civil-citizen-ui"),
    (app('civil-service-prod').requests | extend appName = "civil-service"),
    (app('sptribs-appinsights-prod').requests | extend appName = "sptribs"),
    (app('nfdiv-appinsights-prod').requests | extend appName = "nfdiv-appinsights"),
    (app('div-prod').requests | extend appName = "div"),
    (app('xui-webapp-appinsights-prod').requests | extend appName = "xui-webapp"),
    (app('xui-ao-webapp-appinsights-prod').requests | extend appName = "xui-ao"),
    (app('xui-mo-webapp-appinsights-prod').requests | extend appName = "xui-mo"),
    (app('em-prod').requests | extend appName = "em"),
    (app('rd-prod').requests | extend appName = "rd"),
    (app('ccd-prod').requests | extend appName = "ccd"),
    (app('reform-scan-prod').requests | extend appName = "bulkscan"),
    (app('rpe-pdf-service-appinsights-prod').requests | extend appName = "pdfservice"),
    (app('draft-store-service-appinsights-prod').requests | extend appName = "draftstore"),
    (app('wa-prod').requests | extend appName = "work-allocation"),
    (app('ethos-prod').requests | extend appName = "ethos/ecm"),
    (app('prl-appinsights-prod').requests | extend appName = "prl"),
    (app('fact-appinsights-prod').requests | extend appName = "fact")
| where timestamp > ago(1h)
| where name contains "GET /health"
| summarize HealthCheckCount = sum(iif(name contains "GET /health", 1, 0)) by appName, cloud_RoleInstance
| project appName, HealthCheckCount, cloud_RoleInstance
| order by appName, cloud_RoleInstance
```

## Author
Danny Furnivall (@Danny on Slack)
