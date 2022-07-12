---
layout: default
title: AML Monitoring
parent: AML services
has_toc: true
nav_order: 5
---

# AML  Monitoring


## Create AML check for users and companies

You can create AML monitoring for users and companies. They will be searched periodically until it reaches the expiration date, which is one year after entry creation. When the information about that user or company is matched with entries from AML databases, a webhook and/or email will be sent to the partner.

### Sending request
To add a user to AML monitoring, send a *HTTP POST* request to: `https://ivs.markid.eu/api/v2/add-aml-user` 

The request must contain **basic auth** headers where *username* is **API key** and *password* is **API secret**.

To add a user to AML monitoring, the request must contain JSON with **scanRef**  of client's verification that needs to be monitored or manually typed data, where **name** and **surname**, **type** set as **'PERSON'**  are required fields and *nationality*,  *dateOfBirth*, *monitoringId* are optional.

To add a company to AML monitoring, the request must contain JSON with **name**, **country** and **type** set as **'COMPANY'** *monitoringId* is optional.

:::note
If you'll pass a *scanRef* in your request the type will automatically be counted as 'PERSON' regardless of the value provided in the request.
:::

|JSON key        |Type    |Explanation|
|----------------|--------|-----------|
|`scanRef`      |`String`|A unique string identifying a client verification on Mark ID’s side.|
|`monitoringId` |`String`|A unique string identifying a monitored user or company.|
|`type`         |`String`|Values: <br/>-`COMPANY` Specifies the type as a company.<br/>-`PERSON` Specifies the type as a person.|
|`name`         |`String`|Name of the monitored user or company.|
|`surname`      |`String`|Surname of the monitored user.|
|`nationality`  |`String`|Nationality of the monitored user.|
|`dateOfBirth`  |`String`|Monitored user's date of birth.|
|`country`      |`String`|Mandatory parameter for the `COMPANY` checks in alpha2/alpha3 format.|
#### Example request with scanRef:

```json
{
    "scanRef": "scanRef"
}
```

#### Example request with specified data:

```json
{
    "name": "name",
    "surname": "surname",
    "dateOfBirth": "1990-01-01",
    "nationality": "LTU",
    "type": "PERSON"
}
```
#### Example request to add a company:

```json
{
    "name": "Company's name",
    "type": "COMPANY"
}
```

#### Example responses:
A successful API call returns a JSON response with *monitoringId*.

##### An example response with all fields provided:
```json
{
   "monitoringId": "123456789"
}
```
### Get monitoring webhook
To receive webhook data, send a *HTTP POST* request to: `https://ivs.markid.eu/api/v2/get-monitoring-callback` With monitoring ID in the request body.

#### Monitoring user table

|JSON key        |Type    |Explanation|
|----------------|--------|-----------|
|`monitoringId` |`String`|A unique string identifying a monitored user or company.|
|`name`         |`String`|Name of the monitored user or company.|
|`surname`      |`String`|Surname of the monitored user.|
|`nationality`  |`String`|Nationality of the monitored user.|
|`dob`  |`String`|Date of birth monitored user.|
|`scanRefList`      |`List`|A unique string identifying a client verification on Mark ID’s side.<br/>If you have multiple verifications with the exact same data, and create monitoring users with both of them, only one user will be created and the scanRefList will have both scanRef's, as not to waste monitoring on the same data twice `scanRefList` will be a list of strings|
|`alert_status`|`String`|Current status of the monitored user.<br/>Possible values:<br/>`ALERT`,<br/> `ACCEPTED`,<br/> `DECLINED`,<br/> `PENDING`


#### Results table

|JSON key        |Type    |Explanation|
|----------------|--------|-----------|
|`serviceSuspected`  |`Bool`      |Indicates whether a service found a record about the person under check.| 
|`checkSuccessful`   |`Bool`      |Indicated whether an error occurred while doing a check.                |
|`serviceFound`      |`Bool`      |Indicates whether a service was found for a given country.              |
|`serviceUsed`       |`Bool`      |Indicated whether a service was used.                                   |
|`overallStatus`     |`String`    |A mapping string indicating overall status of the check.                |
|`status`          |`Object`    |The overall status of the service check.                   | 
|`data`            |`List`      |Data returned from services.                               |
|`serviceName`     |`String`    |The name of the service that was used to check a person.   |
|`serviceGroupType`|`String`    |The type of the service that was used. It is always `AML`. |
|`uid`             |`String`    |A unique identifier for a request. NOTE that it is unique only in one request scope. It is not unique globally. This parameter is only useful when doing multi-person checks.|
|`errorMessage`    |`String`    |In case of an error in the checking process a human readable error message is given.|
|`name`              |`String`    |The name of the person in the service's database.          | 
|`surname`           |`String`    |The surname of the person in the service's database.       |
|`nationality`       |`String`    |The nationality of the person in the service's database.   |
|`dob`               |`String`    |The date of birth of the person in the service's database. |
|`suspicion`         |`String`    |Tells the kind of the suspicion.<br/>Possible values:<br/>`PEPS`<br/>`SANCTION`<br/>`INTERPOL`<br/>`OTHER`|
|`reason`            |`String`    |Tells the reason of the kind of the suspicion.             |
|`score`             |`Integer`   |Confidence coefficient that the information about the person is true.|
|`lastUpdate`        |`String`    |Date of when the record was last updated.                  |
|`isPerson`          |`Bool`      |Indicates whether the record is about a person.            |
|`isActive`          |`Bool`      |Indicated whether a record is active.                      |
|`checkDate`         |`String`    |Date and time of when AML name service was checked.        |
|`whitelisted`       |`Bool`      |Boolean indicator if the entry is whitelisted.             |
##### Example request:
```json
{
   "monitoringId": "123456789"
}
```
##### Example response:

```json
{
    "monitoringId": "123456789",
    "name": "Carl",
    "surname": "Smith",
    "nationality": "",
    "dob": null,
    "isActive": true,
    "expiration": "2022-02-02",
    "scanRefList": "[]",
    "alert_status": "DECLINED",
    "results": [
        {
            "status": {
                "serviceSuspected": true,
                "serviceUsed": true,
                "serviceFound": true,
                "checkSuccessful": true,
                "overallStatus": "SUSPECTED"
            },
            "serviceName": "PilotApiAmlV2NameCheck",
            "serviceGroupType": "AML",
            "uid": "RHO3XRYH32W4C4OAWEWHGH1TS",
            "errorMessage": null,
            "data": [
                {
                    "reason": "",
                    "listNumber": "000000",
                    "suspicion": "PEPS",
                    "isActive": true,
                    "checkDate": "2021-02-02 20:02:02",
                    "isPerson": true,
                    "score": 100,
                    "nationality": "",
                    "surname": "SURNAME",
                    "dob": "1956",
                    "lastUpdate": "2018-10-24",
                    "name": "NAME",
                    "listName": "PEPS",
                    "whitelisted": false
                }
            ]
        }
    ]
}  
```
:::note
`companyId`, `beneficiaryId`, `comments`, `pepsStatus`,`status_set_by`, `status_set_at`, `adverseMediaStatus`, and `sanctionsStatus` also appear in the results if the user has a company assigned to him.
:::

### Viewing entries in AML monitoring
To view a list of entries, send a *HTTP GET* request to: `https://ivs.markid.eu/api/v2/get-aml-users`

##### Example response:

```json
{
    "users": [
        {
            "created": "2021-07-27 04:02:02",
            "monitoring_id": "12345678",
            "type": "PERSON",
            "name": "NAME SURNAME",
            "date_of_birth": "1956",
            "nationality": "LITHUANIA",
            "last_check_date": "2021-07-30",
            "is_active": true,
            "expiration_time": "2021-07-20",
            "alert_status": "ACCEPTED",
            "is_suspected": false,
            "whitelisted": false
        },
    ]
}
```
:::note
If the alert_status has "DECLINED" value, that user is not monitored anymore and the data won't be updated further.
:::
### Deleting an entry
To delete an entry from monitoring list, send a *HTTP POST* request to: `https://ivs.markid.eu/api/v2/delete-monitoring-user/` with monitoring ID in the request body.

##### Example request:

```json
{  
   "monitoringId": "12345678"
}
```
A successful API call returns an HTTP response with status 200.


## Add AML checks to the whitelist for a monitored user or company

You can add specific AML checks to the whitelist for a monitored user. AML checks in the whitelist won't be counted as suspected upon detection.

### Sending request
Send a *HTTP POST* request to: `https://ivs.markid.eu/api/v2/add-whitelist` 

The request must contain **basic auth** headers where *username* is **API key** and *password* is **API secret**.

The request must contain JSON with **whitelist** and one of three identifiers: **monitoringId**, **scanRef** or **name** and **surname** (*nationality* and *dateOfBirth* can also be added.) 

|Key            |Type    |Explanation|
| --------------| ------ | --------- | 
|`whitelist`    |`List`  |List of whitelisted sanction and suspicions. <br/>Possible values: <br/>- SANCTION <br/>- INTERPOL <br/>- PEPS <br/>- OTHER <br/>- WORLDBANK_DEBARRED <br/>- EU_EEAS_SANCTIONS <br/>- EU_FSF <br/>- KG_FIU_NATIONAL_LEGAL <br/>- COE_ASSEMBLY <br/>- KG_FIU_NATIONAL_PERSON <br/>- EVERYPOLITICIAN <br/>- GB_HMT_SANCTIONS <br/>- EU_MEPS <br/>- US_CIA_WORLD_LEADERS <br/>- FR_FREEZING_OF_ASSETS <br/>- UA_SDFM_BLACKLIST <br/>- AU_DFAT_SANCTIONS <br/>- UN_SC_SANCTIONS <br/>- INTERPOL_RED_NOTICES <br/>- INTERPOL_YELLOW_NOTICES <br/>- US_BIS_DENIED <br/>- CA_DFATD_SEMA_SANCTIONS <br/>- EU_COR_MEMBERS <br/>- CH_SECO_SANCTIONS <br/>- GB_COH_DISQUALIFIED |
|`type`         |`String`|Values: <br/>-`COMPANY` Specifies the type as a company.<br/>-`PERSON` Specifies the type as a person.|
|`monitoringId` |`String`|A unique string identifying a monitored user.|
|`scanRef`      |`String`|A unique string identifying a client verification on Mark ID’s side.|
|`name`         |`String`|Name of the monitored user.|
|`surname`      |`String`|Surname of the monitored user.|
|`nationality`  |`String`|Nationality of the monitored user.|
|`dateOfBirth`  |`String`|Monitored user's date of birth.|

#### Example request with monitoringId:

```json
{
    "monitoringId": "OVXVXHLL43",
    "whitelist": {"PEPS": ["ListNumber"], "gb_hmt_sanctions": ["entity_id"]}
}
```

#### Example request with scanRef:

```json
{
    "scanRef": "OVXVXHLL43",
    "whitelist": {"PEPS": ["ListNumber"], "gb_hmt_sanctions": ["entity_id"]}
}
```

#### Example request with additional data:

```json
{
    "name": "name", 
    "surname": "surname", 
    "nationality": "LT", 
    "dateOfBirth": "1999-12-31",
    "whitelist":  {"PEPS": ["ListNumber"], "gb_hmt_sanctions": ["entity_id"]}
}
```

#### Example requests with a company:

```json
{
    "name": "Company's name",
    "whitelist": {"gb_hmt_sanctions": ["entity_id"]}
}
```
```json
{
    "monitoringId": "monitoringId",
    "whitelist": {"PEPS": ["ListNumber"]}
}
```

### Removing entries from whitelist
To remove an entry from whitelist, send a *HTTP POST* request to: `https://ivs.markid.eu/api/v2/clear-user-whitelist/` with monitoring ID in the request body.

##### Example request:
```json
{  
   "monitoringId": "12345678"
}
```

#### Example responses:
A successful API call returns an HTTP response with status 200.

## Generate AML monitoring report

You can retrieve a report in PDF format about the monitoring entry.

The report contains the following information about the entry:

* Monitoring ID

* Personal information, such as: name, surname, full name, nationality, date of birth.

* Type (person or company)

* Entry date

* Last check date

* Expiration date

* Suspicion type and reason of suspicion

* Checked service name

* Indicators (probability score, active or not).

### Sending request
Send a *HTTP POST* request to: `https://ivs.markid.eu/api/v2/generate-pdf-aml`

The request must contain *basic auth* headers where *username* is *API key* and *password* is *API secret*.
The request must contain JSON with parameter:

|     Key    | Required |              Explanation              |   Type   | 
| -----------| -------- | ------------------------------------- | -------- |
| `monitoringId`  | Yes      | A unique string identifying AML monitoring entry. | `String` | 


#### Response 
After a successful API call, you will receive a PDF file in **Base64** format and a response with a positive **200** status.

Only one entry is accepted per request.


### Examples
#### Example request

```json
{
    "monitoringId": "{{monitoringId}}"
}	
```

<!--
## Search form AML sanctions

You can search form AML sanctions.

### Sending request
Send a *HTTP Post* request to: `https://ivs.markid.eu/api/v2/check-aml-sanctions` 

The request must contain *basic auth* headers where *username* is *api key* and *password* is *api secret*.

The request must contain JSON with parameters:

|Key       |Required|Type          |Explanation|
|----------|--------|--------------|-----------|
|`mode`    |Yes     |`String`      |Specifies how fraud check will be performed. <br/>If `IDENTIFICATION` is given, you will have to provide a scanRef in the data field. <br/>If `DATA` is given, you will have to provide all necessary data about client for required services.|
|`services`|Yes     |`List[String]`|Types of services to apply. <br/>`PERSON`- people check<br/> `COMPANY` - companies check.|
|`data`    |Yes     |`List[Dict]`  |Supplied data for fraud check services. In each dictionary could be given `scanRef` with IDENTIFICATION mode, and with DATA mode search data: <br/>- `firstName` (required) <br/>- `lastName` (required) <br/>- `nationality` (optional) <br/>- `dateOfBirth` (optional)|


#### Example request

```json
{
  "services": ["PERSON"],
  "mode": "DATA",
  "data": [
    {
    	"firstName": "Aliaksandr" ,
        "lastName": "Lukashenka"     
    }
  ]
}
```

### Response table

|JSON key          |Type        |Explanation                                                |
|------------------|------------|-----------------------------------------------------------|
|`status`          |`Object`    |The overall status of the service check.                   | 
|`data`            |`List`      |Data returned from services.                               |
|`serviceName`     |`String`    |The name of the sanction that was used to check.   |
|`serviceGroupType`|`String`    |The type of the service that was used. It is always `AML OPEN SANCTIONS`. |
|`uid`             |`String`    |A unique identifier for a request. NOTE that it is unique only in one request scope. It is not unique globally. This parameter is only useful when doing multi-person checks.|
|`errorMessage`    |`String`    |In case of an error in the checking process a human readable error message is given.|

#### Status table

|JSON key            |Type        |Explanation                                                             |
|--------------------|------------|------------------------------------------------------------------------|
|`serviceSuspected`  |`Bool`      |Indicates whether a service found a record about the person under check.| 
|`checkSuccessful`   |`Bool`      |Indicated whether an error occurred while doing a check.                |
|`serviceFound`      |`Bool`      |Indicates whether a service was found.              |
|`serviceUsed`       |`Bool`      |Indicated whether a service was used.                                   |
|`overallStatus`     |`String`    |A mapping string indicating overall status of the check.                |

#### Example response

```json
[
    {
        "status": {
            "serviceSuspected": true,
            "serviceUsed": true,
            "serviceFound": true,
            "checkSuccessful": true,
            "overallStatus": "SUSPECTED"
        },
        "serviceName": "gb_hmt_sanctions",
        "serviceGroupType": "AML OPEN SANCTIONS",
        "uid": "HEJUTK1SG9HV2E0XU8YJ8N6FW",
        "errorMessage": null,
        "data": [
            {
                "authority": "HM Treasury Financial sanctions targets",
                "country": "Belarus",
                "birthPlace": [
                    "Kopys, Vitebsk/Viciebsk district"
                ],
                "program": "Global Human Rights",
                "birthDate": "1954-08-30",
                "nationality": null,
                "name": "Aliaksandr Grigorievich LUKASHENKA",
                "firstName": "Aliaksandr",
                "lastName": "LUKASHENKA",
                "address": "Independence Palace Prospekte Pobeditelei Minsk Belarus",
                "passportNumber": "",
                "nationalId": "",
                "modifiedAt": "2020-12-31",
                "birthCountry": [
                    "former USSR (currently Belarus",
                ],
                "startDate": "2020-09-29",
                "position": "President of Belarus",
                "type": "Person",
                "group": "13918",
                "notes": "(UK Sanctions List Ref):GHR0050 and BEL0045 Listed under the Global Human Rights and Belarus sanctions regimes. (UK Statement of Reasons):Alexander Lukashenko is the President of Belarus. He has held near absolute authority in his 26 years in power and maintains close control over the security apparatus of the State. This authority has remained in place since the 9 August Presidential election and he is ultimately responsible for the violence against protestors and journalists that has followed the disputed election result. Lukashenko has not called out violence and has expressed support for security services. Alexander Lukashenko has therefore been responsible for serious violations of human rights in the weeks following 9 August and has been involved in promoting such violations. (Gender):Male",
                "title": ""
            }
        ]
    },
    {
        "status": {
            "serviceSuspected": true,
            "serviceUsed": true,
            "serviceFound": true,
            "checkSuccessful": true,
            "overallStatus": "SUSPECTED"
        },
        "serviceName": "eu_eeas_sanctions",
        "serviceGroupType": "AML OPEN SANCTIONS",
        "uid": "R6T5MJ1X02A2WC03SBAC6O11Y",
        "errorMessage": null,
        "data": [
            {
                "sourceUrl": "https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=uriserv%3AOJ.L_.2021.068.01.0029.01.ENG&toc=OJ%3AL%3A2021%3A068%3ATOC",
                "authority": "European External Action Service",
                "name": "Viktar Aliaksandravich LUKASHENKA",
                "country": "",
                "modifiedAt": "2021-02-27",
                "createdAt": "2021-02-27",
                "program1": "https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=uriserv%3AOJ.L_.2021.068.01.0029.01.ENG&toc=OJ%3AL%3A2021%3A068%3ATOC",
                "program2": "2021/339 (OJ L68)",
                "reason": "(Date of UN designation: 2020-11-06)",
                "startDate": "2021-02-27",
                "address": "",
                "url": "https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=uriserv%3AOJ.L_.2021.068.01.0029.01.ENG&toc=OJ%3AL%3A2021%3A068%3ATOC",
                "title": "",
                "type": "Person",
                "firstName": "",
                "lastName": "",
                "birthDate": "",
                "nationality": "",
                "gender": "male",
                "middleName": "",
                "position": "National Security Advisor to the President, Member of the Security Council",
                "birthCity": "",
                "birthPlace": ""
            }
        ]
    }
]
```
-->
