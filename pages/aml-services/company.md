---
layout: default
title: Company check
parent: AML services
has_toc: true
nav_order: 4
---

# Company check
This service checks whether a company has an AML risk, if it has any identified hits and other information on the company.

{: .note-title }
> Pricing
> 
> Please contact Mark ID sales team sales@markid.lt for a detailed price-sheet.


## Callback data
The service check returns company's data in JSON format.
Overall response can be seen in the table below:

### Overall response table

|JSON key          |Type        |Explanation                                                |
|------------------|------------|-----------------------------------------------------------|
|`status`          |`Object`    |The overall status of the service check.                   | 
|`data`            |`List`      |Data returned from services.                               |
|`serviceName`     |`String`    |The name of the service that was used to check a company.   |
|`serviceGroupType`|`String`    |The type of the service that was used. It is always `COMPANY`. |
|`uid`             |`String`    |A unique identifier for a request. NOTE that it is unique only in one request scope. It is not unique globally. This parameter is only useful when doing multi-company checks.|
|`errorMessage`    |`String`    |In case of an error in the checking process a human readable error message is given.|

### Status table

|JSON key            |Type        |Explanation                                                             |
|--------------------|------------|------------------------------------------------------------------------|
|`serviceSuspected`  |`Bool`      |Indicates whether a service found a record about the company under check.| 
|`checkSuccessful`   |`Bool`      |Indicated whether an error occurred while doing a check.                |
|`serviceFound`      |`Bool`      |Indicates whether a service was found for a given country.              |
|`serviceUsed`       |`Bool`      |Indicated whether a service was used.                                   |
|`overallStatus`     |`String`    |A mapping string indicating overall status of the check.<br/>Possible values:<br/> DATA_ERROR<br/>  Supplied data for fraud check is malformed therefore a check can not be performed<br/> SUSPECTED<br/>Fraud API results were found for given person therefore check is suspected<br/> NOT_SUSPECTED<br/> Fraud API results were not found therefore the check is not suspected<br/> INTERNAL_ERROR<br/> Internal error on our side<br/> SERVICE_NOT_FOUND <br/> Could not find a suitable fraud API for supplied data<br/> CHECK_FAILED<br/> The called API did not return a successful response or response was malformed |

## Example response

```json

{
    "status": {
        "serviceSuspected": true,
        "serviceUsed": true,
        "serviceFound": true,
        "checkSuccessful": true,
        "overallStatus": "SUSPECTED"
    },
    "serviceName": "CompanyCheck",
    "serviceGroupType": "COMPANY",
    "uid": "EU065N3B85FSHRGYXHL9XAVOJ",
    "errorMessage": null,
    "data": [
        {
            "result": {
                "Name": "My Company",
                "Residence": "LT",
                "CompanyHasHits": "true",
                "ScoreAml": "0",
                "RiskAml": "L",
                "RiskElements": [
                    {
                        "RiskName": "RiskAml",
                        "Explanation": "The AML risk for this company is LOW"
                    },
                    {
                        "RiskName": "CompanyHasHits",
                        "Explanation": "The company's name is similar to names on sanctions' lists. Please check if the hits apply to the company."
                    }
                ],
                "HitsOnLists": [
                    {
                        "Name": "My Company",
                        "Forename": "UNKNOWN",
                        "Reason": "reason",
                        "Nationality": "Unknown",
                        "BirthDate": "UNKNOWN",
                        "HitType": "Sanction",
                        "ListNumber": "12345",
                        "ListName": "OFAC",
                        "Score": "100",
                        "LastUpdateDate": "2018-08-23T00:00:00",
                        "IsPerson": "false",
                        "IsActive": "true"
                    }
                ],
                "CompaniesFound": []
            },
            "companyName": "My Company",
            "country": "LT"
        }
    ]
}
```
