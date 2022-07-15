---
layout: default
title: Global data sources check
parent: Fraud prevention services
has_toc: true
nav_order: 5
---

### Utility of global data sources check during verification process

This service is used to verify person’s identity data against the databases from more than 200 government and commercial sources.

If you already have this functionality on your environment, there is a possibility to get automatic check. It compares provided information and entries from databases of global data sources. In your [webhook callback](/pages/fraud-prevention-services/ResultCallback), you will receive a field - `gdcMatch` that can have values - `true`, `false` or `null`. 


### Separate API request data and examples

The request is sent to `https://ivs.markid.eu/api/v2/check-gdc-api` and it must contain *basic auth* headers where *username* is *API key* and *password* is *API secret*.

| Mandatory parameters                                                                                                             |
|----------------------------------------------------------------------------------------------------------------------------------|
| `name` - First name, `surname` - Last name **and/or** `ssnNumber` - National ID/Number, social security number, `dateOfBirth` - Date of Birth (MM/DD/YYYY) |

```json
{
  "name": "John",
  "surname": "Smith",
  "dateOfBirth":"1958-10-12",
  "ssnNumber": "1234567890"
}
```

#### Example response
The service also returns a reliability score of the checked person:

|Score|Description|Explanation|
|---|---|---|
10|(Good/Correct/Pass)|Messages and match types indicate that this is a passing match combination based on the defined rule set.|
20 | (Probable/Possible) | Messages and match types are sufficient to consider this as a possible match/pass.|
30 | (Did not Pass/Doubtful) | There may or may not have been matched elements but it is doubtful this is sufficient information.|

You will receive HTTP response 200 and JSON body with the following data:

```json
{
  "completename": "John Smith",
  "formofaddress": "",
  "qualificationpreceding": "",
  "givenfullname": "John",
  "givennameinitials": "",
  "qualification_int_first": "",
  "surname_prefix_first": "",
  "surname_first": "Smith",
  "indicator": "",
  "qualification_int_second": "",
  "surname_prefix_second": "",
  "qualification_suceeding": "",
  "name_qualified": "",
  "function": "",
  "gender": "",
  "nationality": "",
  "nationalid": "1234567890",
  "organization_name": "",
  "dob": "10/12/1958",
  "businessid": "",
  "contact_type": "",
  "countryCode": "US",
  "passport": "",
  "codes": {
    "reliability": "30",
    "adaptation": "0",
    "detailCode": "WS-6641463.2022.2.28.17.12.39.893",
    "detail_list": "",
    "options": "identityverify;messageverbose",
    "messages": [
      {
        "code": "1MT--PRP1-FIRSTINITIAL",
        "value": "Full match was made on First Initial"
      },
      {
        "code": "1MT--PRP1-NATIONALID",
        "value": "Full match was made on National ID"
      },
      {
        "code": "3MT--PRP1-COMPLETENAME",
        "value": "Partial match made on element Complete Name"
      },
      {
        "code": "3AU-425-COMPLETENAME",
        "value": "Partial match made on element Complete Name"
      },
      {
        "code": "2AU-400-LASTNAME",
        "value": "No match was made on Last Name/Surname"
      },
      {
        "code": "2AU-350-FIRSTNAME",
        "value": "No match was made on First Name/Given Name"
      },
      {
        "code": "1AU-375-FIRSTINITIAL",
        "value": "Full match was made on First Initial"
      },
      {
        "code": "2AU-625-YEAROFBIRTH",
        "value": "No match was made on Year of Birth"
      },
      {
        "code": "2AU-600-MONTHOFBIRTH",
        "value": "No match was made on Month of Birth"
      },
      {
        "code": "2AU-575-DAYOFBIRTH",
        "value": "No match was made on Day of Birth"
      },
      {
        "code": "2AU-550-DATEOFBIRTH",
        "value": "No match was made on Date of Birth"
      },
      {
        "code": "1AU-525-NATIONALID",
        "value": "Full match was made on National ID"
      },
      {
        "code": "2AU-200-ADDRESS",
        "value": "No match was made on Address Elements provided in Address Lines"
      },
      {
        "code": "Codes",
        "value": "1AU-375-FIRSTINITIAL;1AU-525-NATIONALID;1MT--PRP1-FIRSTINITIAL;1MT--PRP1-NATIONALID;2AU-200-ADDRESS;2AU-350-FIRSTNAME;2AU-400-LASTNAME;2AU-550-DATEOFBIRTH;2AU-575-DAYOFBIRTH;2AU-600-MONTHOFBIRTH;2AU-625-YEAROFBIRTH;3AU-425-COMPLETENAME;3MT--PRP1-COMPLETENAME;"
      }
    ],
    "detailList": ""
  },
  "contactType": "",
  "nameQualified": "",
  "organizationName": "",
  "qualificationIntFirst": "",
  "qualificationIntSecond": "",
  "qualificationSuceeding": "",
  "surnameFirst": "Smith",
  "surnamePrefixFirst": "",
  "surnamePrefixSecond": ""
}
```

:::note
Please contact sales@markid.eu to discuss the possibilities of including this service to your workflow.

Technical inquiries should be forwarded to our technical support team via Dashboard. 
:::
