---
layout: default
title: AML API
parent: AML services
has_toc: true
nav_order: 1
---

# Fraud checking API 
Fraud checking API is an additional service used to further identify a person and stop fraudulent activities.

### Available services:

- (*AML*) Peps & Sanctions name check
- (*AML*) Adverse media articles check
- (*COMPANY*) Check information about a company, as well as any hits identified on the company.
- (*LID*) Lost and Invalid documents registry check (elligible for **Lithuanian** documents only)

### Calling the API:

:::important
Fraud check endpoint was changed from `https://fraud.markid.eu/V1/check` to `https://ivs.markid.eu/fraud/check`
:::
Send a *HTTP Post* request to: `https://ivs.markid.eu/fraud/check`

The request must contain **basic auth** headers where *username* is **API key** and *password* is **API secret**.

:::note
***API key*** and ***API secret*** can be retrieved by contacting *Mark ID tech support* or *Mark ID sales team*:
- sales@markid.eu
- info@markid.eu
- via Dashboard
:::

The request must contain JSON with parameters:

|Key       |Required|Type          |Explanation|
|----------|--------|--------------|-----------|
|`mode`    |Yes     |`String`      |Specifies how fraud check will be performed. <br/>If `IDENTIFICATION` is given, you will have to provide a scanRef in the data field. <br/>If `DATA` is given, you will have to provide all necessary data about client for required services.|
|`services`|Yes     |`List[String]`|Types of services to apply. <br/>`AML` - Anti money laundering service, <br/>`COMPANY` - Company check, <br/> `LID` - Lost and invalid documents registry(**Lithuanian** documents only).|
|`data`    |Yes     |`List[Dict]`  |Supplied data for fraud check services. The list should consist of one element only.|

#### Specifying AML services

By default if `AML` service is specified in `services` - a name check against global databases for PEPs and Sanctions is performed.
> To have specific `AML` services applied in `data` entry specify names of the services:

|AML service name   |Type    |Default|Constraints|
|-------------------|--------|-------|-----------|
|`nameCheck`        |`Bool`  |`true` |-          |
|`negativeNews`     |`Bool`  |`false`|-          |

#### Filtering AML results

AML nameCheck results can be filtered in multiple ways, by supplying the filters in `data` entry

|Key              |Type         |Constraints |Explanation|
|-----------------|-------------|------------|-----------|
|`probability`    |`Int`        |1-100       |The higher the number, the more similar the found name has to be to the provided one. Default is 95.|
|`dateOfBirth`    |`String`     |`YYYY-MM-DD`|If the result has a birth date and it clashes with the provided one, the result is not returned.|
|`nationality`    |`String`     |length = 3  |If the result has a nationality and it clashed with the provided one, the result is not returned.|

#### Specifying LID services

If `LID` service is specified in `services` and `mode` is specified as `DATA` - a `country` parameter should be also provided in `data` entry. This is because a `LID` service is associated to a single country.

|LID data key   |Type    |Default|Constraints         |
|---------------|--------|-------|--------------------|
|`country`      |`String`|-      |Country alpha-2 code|

 ### Receiving response
|Key       |Type         |Explanation|
|----------|-------------|-----------|
|`AML`     |`List[Dict]` |Fraud check results from AML services. If `AML` service was not specified in the request this key is not present in the response. For a complete response documentation please refer to [AML documentation](/fraud/aml).|
|`LID`     |`List[Dict]` |Fraud check results from LID services. If `LID` service was not specified in the request this key is not present in the response. For a complete response documentation please refer to [LID documentation](/fraud/lid).|
|`COMPANY` |`List[Dict]` |Fraud check results from COMPANY services. If `COMPANY` service was not specified in the request this key is not present in the response. For a complete response documentation please refer to [COMPANY documentation](/fraud/company).|

## Examples

### Example AML check

#### Example request

```json
{
  "mode": "DATA",
  "services": [
    "AML"
  ],
  "data": [
    {
    	"name": "Alexander",
        "surname": "Lukashenko"
    }
  ]
}
```

:::important
Only one entry is allowed per request. If you'll provide multiple entries in `data`, the response will be 400(Bad request) - "message": "Incorrect request: data length must be 1".

Incorrect request:

```json
"data": [
    {
        "name":"Alexander",
        "surname": "Lukashenko",
        "nameCheck": true
    },
    {
        "name":"Vladimir",
        "surname": "Putin",
        "nameCheck": true
    }
```
:::

#### Example response

```json
{
    "AML": [
        {
            "status": {
                "serviceSuspected": true,
                "serviceUsed": true,
                "serviceFound": true,
                "checkSuccessful": true,
                "overallStatus": "SUSPECTED"
            },
            "serviceName": "PilotApiAmlV2NameCheck",
            "uid": "KXTCTZR3RXQK7MQIOWLNTHO0E",
            "errorMessage": null,
            "data": [
                {
                    "reason": "BELARUS PROGRAM",
                    "listNumber": "9760",
                    "suspicion": "SANCTION",
                    "isActive": true,
                    "checkDate": "2022-03-07 14:34:27",
                    "isPerson": true,
                    "score": 100,
                    "nationality": "UNKNOWN",
                    "surname": "LUKASHENKO",
                    "dob": "30 AUG 1954",
                    "lastUpdate": "2018-08-23",
                    "name": "ALEXANDER HRYHORYAVICH",
                    "listName": "OFAC"
                },
                {
                    "reason": "BELARUS PROGRAM",
                    "listNumber": "9760",
                    "suspicion": "SANCTION",
                    "isActive": true,
                    "checkDate": "2022-03-07 14:34:27",
                    "isPerson": true,
                    "score": 100,
                    "nationality": "UNKNOWN",
                    "surname": "LUKASHENKO",
                    "dob": "30 AUG 1954",
                    "lastUpdate": "2018-08-23",
                    "name": "ALEXANDER GRIGORIEVICH",
                    "listName": "OFAC"
                },
                {
                    "reason": "BELARUS: NATIONAL OLYMPIC COMMITTEE - PRESIDENT (EX)",
                    "listNumber": "2280096",
                    "suspicion": "PEPS",
                    "isActive": false,
                    "checkDate": "2022-03-07 14:34:27",
                    "isPerson": true,
                    "score": 97,
                    "nationality": "BELARUS",
                    "surname": "LUKASHENKO",
                    "dob": "UNKNOWN",
                    "lastUpdate": "2021-11-02",
                    "name": "ALEXANDRE",
                    "listName": "PEPS"
                }
            ],
            "serviceGroupType": "AML"
        }
    ]
}
```

#### Example request with adverse media check

```json
{
  "mode": "DATA",
  "services": [
    "AML"
  ],
  "data": [
    {
    	"name": "Alexander",
        "surname": "Lukashenko",
        "negativeNews": true,
        "nameCheck": true
    }
  ]
}
```

#### Example response

```json
{
    "AML": [
        {
            "status": {
                "serviceSuspected": true,
                "serviceUsed": true,
                "serviceFound": true,
                "checkSuccessful": true,
                "overallStatus": "SUSPECTED"
            },
            "serviceName": "PilotApiAmlV2NameCheck",
            "uid": "W08VHTFGBLR5VYUWKXSHRF5ZT",
            "errorMessage": null,
            "data": [
                {
                    "name": "ALEXANDER HRYHORYAVICH",
                    "surname": "LUKASHENKO",
                    "reason": "BELARUS PROGRAM",
                    "nationality": "UNKNOWN",
                    "dob": "30 AUG 1954",
                    "suspicion": "SANCTION",
                    "listNumber": "9760",
                    "listName": "OFAC",
                    "score": 100,
                    "lastUpdate": "2018-08-23",
                    "isPerson": true,
                    "isActive": true,
                    "checkDate": "2022-03-07 14:21:50"
                },
                {
                    "name": "ALEXANDER GRIGORIEVICH",
                    "surname": "LUKASHENKO",
                    "reason": "BELARUS PROGRAM",
                    "nationality": "UNKNOWN",
                    "dob": "30 AUG 1954",
                    "suspicion": "SANCTION",
                    "listNumber": "9760",
                    "listName": "OFAC",
                    "score": 100,
                    "lastUpdate": "2018-08-23",
                    "isPerson": true,
                    "isActive": true,
                    "checkDate": "2022-03-07 14:21:50"
                },
            ],
            "serviceGroupType": "AML"
        },
        {
            "status": {
                "serviceSuspected": false,
                "serviceUsed": true,
                "serviceFound": true,
                "checkSuccessful": true,
                "overallStatus": "NOT_SUSPECTED"
            },
            "serviceName": "PilotApiAmlV2NegativeNews",
            "uid": "W08VHTFGBLR5VYUWKXSHRF5ZT",
            "errorMessage": null,
            "data": [
                {
                    "title": "ALEXANDER LUKASHENKO | CNN",
                    "url": "https://www.cnn.com/2021/07/02/europe/alexander-lukashenko/index.html",
                    "text": "August 16, 2020 - Lukashenko gives a speech to an estimated crowd of less than 10,000 supporters, according to CNN's team in Minsk, Belarus. He claims Belarus is being threatened by foreign ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                },
                {
                    "title": "BELARUS: WHO IS AUTHORITARIAN PRESIDENT ALEXANDER LUKASHENKO?",
                    "url": "https://www.usatoday.com/story/news/politics/2022/03/02/belarus-alexander-lukashenko/9330908002/",
                    "text": "WASHINGTON -- When the Belarusian government said on Monday over half the country's voters supported a constitutional amendment allowing authoritarian President Alexander Lukashenko to stay in ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                },
                {
                    "title": "ALEXANDER LUKASHENKO: BELARUSIAN STRONGMAN TRIES TO TURN ...",
                    "url": "https://www.cnn.com/2021/10/02/europe/belarus-lukashenko-interview/index.html",
                    "text": "Belarusian strongman Alexander Lukashenko tries to turn the tables in combative interview. EXCLUSIVE by Zahra Ullah and Matthew Chance, CNN. Updated 12:00 AM ET, Sat October 2, 2021. Minsk ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                },
                {
                    "title": "WHO IS BELARUS PRESIDENT ALEXANDER LUKASHENKO, 'EUROPE'S ...",
                    "url": "https://www.foxnews.com/world/who-is-europes-last-dictator-alexander-lukashenko",
                    "text": "Some 200,000 people swarmed the streets in Belarus's capital Minsk on Sunday - demanding that President Alexander Lukashenko, often dubbed \"Europe's Last Dictator\" - step down after 26 years ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                },
                {
                    "title": "WHO IS ALEXANDER LUKASHENKO? BELARUSIAN DICTATOR'S ...",
                    "url": "https://www.businessinsider.com/who-is-alexander-lukashenko-closer-look-at-the-belarusian-dictator-2021-5",
                    "text": "In 1993, he was named head of the Belarusian parliament's anti-corruption commission. In 1994, Lukashenko was elected president of Belarus with 80.3% of the vote, running on the pledge to \"take ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                },
                {
                    "title": "THE ACCIDENTAL DICTATORSHIP OF ALEXANDER LUKASHENKO ...",
                    "url": "https://www.aei.org/articles/the-accidental-dictatorship-of-alexander-lukashenko/",
                    "text": "Belarusian President Alexander Lukashenko has confirmed his reputation as the last dictator in Europe. His election to the presidency in 1994 remains the first and last democratic election in the ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                },
                {
                    "title": "OPINION | HOW ALEXANDER LUKASHENKO ELECTED ... - NBC NEWS",
                    "url": "https://www.nbcnews.com/think/opinion/will-belarus-protests-topple-europe-s-last-dictator-alexander-lukashenko-ncna1237472",
                    "text": "Aug. 20, 2020, 12:26 PM PDT. By Alesia Rudnik, research fellow at the Center for New Ideas. After 26 years of surviving in an autocracy, Belarusians have become politicized. In the days since ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                },
                {
                    "title": "EUROPEAN UNION SLAPS SANCTIONS ON BELARUSIAN PRESIDENT ...",
                    "url": "https://www.nbcnews.com/news/world/european-union-slaps-sanctions-belarusian-president-alexander-lukashenko-n1246821",
                    "text": "European Union slaps sanctions on Belarusian President Alexander Lukashenko Lukashenko \"is responsible for the violent repression by the State apparatus carried out before and after the 2020 ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                },
                {
                    "title": "EUROPE'S 'LAST DICTATOR': WHO IS BELARUS'S ALEXANDER ...",
                    "url": "https://www.aljazeera.com/news/2021/11/25/belarus-leaders-trajectory-from-communist-farmer-to-paranoid",
                    "text": "With a grey comb-over hairstyle, chevron-shaped moustache and a strong rural accent, Alexander Lukashenko, a former collective farm manager-turned-minor Communist official, was the only legislator ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                },
                {
                    "title": "WHO IS ALEXANDER LUKASHENKO'S SON AND POSSIBLE SUCCESSOR ...",
                    "url": "https://meaww.com/who-is-alexander-lukashenkos-son-and-possible-successor-nikolais-mother-may-have-been-prezs-doc",
                    "text": "Who is Alexander Lukashenko's son and possible successor? Alexander is the father of two grown-up sons, Viktor Lukashenko, 46, and Dmitry Lukashenko, 42, and a teenage son named Nikolai Lukashenko. Viktor's and Dmitry's mothers are the same, it has been said that Nikolai's mother is different.",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                },
                {
                    "title": "ALEXANDER LUKASHENKO | FACTS, BIOGRAPHY, & PRESIDENCY OF ...",
                    "url": "https://www.britannica.com/biography/Alexander-Lukashenko",
                    "text": "Alexander Lukashenko, Lukashenko also spelled Lukashenka, (born August 30, 1954, Kopys, Vitebsk oblast, Belorussia, U.S.S.R. [now in Belarus]), Belarusian politician who espoused communist principles and who became president of the country in 1994.. Lukashenko graduated from the Mogilyov Teaching Institute and the Belarusian Agricultural Academy. In the mid-1970s he was an instructor in ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                },
                {
                    "title": "HOW BELARUS BECAME PUTIN'S LACKEY | THE NEW REPUBLIC",
                    "url": "https://newrepublic.com/article/165533/belarus-ukraine-putin-alexander-lukashenko",
                    "text": "How Belarus Became Putin's Lackey The Kremlin propped up Aleksandr Lukashenko after Belarusians tried to oust him from office. He's now dragged his nation into the Ukraine invasion.",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                },
                {
                    "title": "ALEXANDER LUKASHENKO, INTERNATIONAL TERRORIST - POLITICO",
                    "url": "https://www.politico.eu/article/alexander-lukashenko-terrorist-belarus-ryanair-plane-roman-protasevich/",
                    "text": "Alexander Lukashenko, international terrorist ... He jailed opposition hopefuls and critics, used junta-style methods against peaceful mass protests and banned civil society organizations and independent media outlets. To date, more than 35,000 Belarusians have been arrested, thousands have been abused and tortured in police custody, and ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-03-07 14:21:51"
                }
            ],
            "serviceGroupType": "AML"
        }
    ]
}
```

### Example LID check

#### Example request

```json
{
  "mode": "DATA",
  "services": [
    "LID"
  ],
  "data": [
    {
    	"type": "PASSPORT",
        "number": "74587539",
        "country": "LT"
    }
  ]
}
```

#### Example response

```json
{
  "LID": [
    {
      "status": {
        "serviceSuspected": true,
        "serviceUsed": true,
        "serviceFound": true,
        "checkSuccessful": true,
        "overallStatus": "SUSPECTED"
      },
      "serviceName": "IrdInvalidPapers",
      "serviceGroupType": "LID",
      "uid": "KBSZDZPBJ3FS6NOT4L33VZ0KS",
      "errorMessage": null,
      "data": [
        {
          "documentNumber": "74587539",
          "documentType": null,
          "valid": false,
          "expiryDate": "2018-03-21",
          "checkDate": "2020-02-02 20:02:02"
        }
      ]
    }
  ]
}
```

### Example COMPANY check

#### Example request

```json
{
  "mode": "DATA",
  "services": [
    "COMPANY"
  ],
  "data": [
    {
    	"companyName": "MyCompany",
        "country": "LT"
    }
  ]
}
```

#### Example response

```json
{
    "COMPANY": [
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
            "uid": "KNR6IJ7G3EYNXSVVQNN6ZV3VV",
            "errorMessage": null,
            "data": [
                {
                    "result": {
                        "Name": "MyCompany",
                        "Residence": "LT",
                        "CompanyHasHits": "true",
                        "ScoreAml": "2",
                        "RiskAml": "M",
                        "RiskElements": [
                            {
                                "RiskName": "ScoreAml",
                                "Explanation": "The company is located in a medium risk country"
                            },
                            {
                                "RiskName": "RiskAml",
                                "Explanation": "The AML risk for this company is MEDIUM"
                            },
                            {
                                "RiskName": "CompanyHasHits",
                                "Explanation": "The company's name is similar to names on sanctions' lists. Please check if the hits apply to the company."
                            }
                        ],
                        "HitsOnLists": [
                            {
                                "Name": "MY AVIATION COMPANY LIMITED",
                                "Forename": "UNKNOWN",
                                "Reason": "IFSR Program",
                                "Nationality": "Unknown",
                                "BirthDate": "UNKNOWN",
                                "HitType": "Sanction",
                                "ListNumber": "24877",
                                "ListName": "OFAC",
                                "Score": "100",
                                "LastUpdateDate": "2018-09-18T00:00:00",
                                "IsPerson": "false",
                                "IsActive": "true"
                            }
                        ],
                        "CompaniesFound": []
                    },
                    "companyName": "MyCompany",
                    "country": "LT"
                }
            ]
        }
    ]
}
```

### Adverse media check on companies

```json
{
  "mode": "DATA",
  "services": [
    "COMPANY"
  ],
  "data": [
    {
        "companyName": "Naftan",
        "country": "BY",
        "negativeNews": true
    }
  ]
}
```

#### Example response:

```json
{
    "AML": [
        {
            "status": {
                "serviceSuspected": false,
                "serviceUsed": true,
                "serviceFound": true,
                "checkSuccessful": true,
                "overallStatus": "NOT_SUSPECTED"
            },
            "serviceName": "PilotApiAmlV2NegativeNews",
            "uid": "FW0BJRTBLGX82TJKDZ5DNCSH3",
            "errorMessage": null,
            "data": [
                {
                    "title": "NAFTAN OIL REFINERY CHANGES NAME AND ... - EURORADIO.FM",
                    "url": "https://euroradio.fm/en/naftan-oil-refinery-changes-name-and-ownership-structure-due-us-sanctions",
                    "text": "It decided to change the name, ownership structure and expand the number of owners in the spring of 2021. Since May 17, the word \"NAFTAN\" has disappeared from the name of the enterprise. Now it is called EddieTech LLC. Moreover, the company has a new owner. Previously, the plant was owned 50%-50% by Naftan and Lukoil.",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "SETTLEMENT AGREEMENT - UNITED STATES SECRETARY OF THE TREASURY",
                    "url": "https://home.treasury.gov/system/files/126/20181220_zoltek_settlement.pdf",
                    "text": "to be blocked persons. Therefore, because Belneftekhim owned 50 percent or greater of Naftan at the time of its designation, Naftan itself became an OFAC-blocked person on November 13, 2007 pursuant to SS 548.411. On December 1, 2008, Naftan (which, as noted above, was a",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "ACTIVIST FROM NAVAPOLATSK: EMPLOYEES OF NAFTAN ARE BEING ...",
                    "url": "https://charter97.org/en/news/2021/4/29/420424/",
                    "text": "Activist from Navapolatsk: Employees of Naftan Are Being Prepared For the Fact That There Will Soon Be Dismissals and Salary Cuts 9. 29.04.2021, 17:25 ; 15,414; The situation at the enterprise does not inspire optimism.",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "OFAC REVOKES KEY GENERAL LICENSE UNDER BELARUS SANCTIONS ...",
                    "url": "https://www.clearytradewatch.com/2021/04/ofac-revokes-key-general-license-under-belarus-sanctions-program/",
                    "text": "On April 19, 2021, in response to reported human rights violations by the regime of President Alexander Lukashenko, the U.S. Department of the Treasury, Office of Foreign Assets Control (\"OFAC\") issued General License 2H (\"GL 2H\") under the U.S. sanctions program targeting Belarus. GL 2H revokes and replaces General License 2G (\"GL 2G\"), [2] which authorized U.S. persons to engage ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "U.S. COMPANIES MUST WIND DOWN TRANSACTIONS WITH SANCTIONED ...",
                    "url": "https://www.ustrademonitor.com/2021/05/u-s-companies-must-wind-down-transactions-with-sanctioned-belarusian-state-owned-enterprises-by-june-3rd/",
                    "text": "The Office of Foreign Assets Control (\"OFAC\") recently tightened sanctions on Belarus by revoking and replacing General License 2G with General License 2H. General License 2H now requires U.S. persons to wind down transactions involving the following Belarusian Specially Designated Nationals and their subsidiaries by June 3, 2021: ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "28 - NAFTAN.BY",
                    "url": "http://www.naftan.by/docs/prop/Complete_information_Naftan_29_04_2021.doc",
                    "text": "bids for long-term sales of Oil Bitumen of OJSC Naftan origin in assortment planned for April 29, 2021. On April 29, 2021 . CJSC Belarusian Oil Company (hereinafter - CJSC BNK, Tender Organizer) is holding an open tender of commercial bids for long-term sale of Oil Bitumen of OJSC Naftan origin in assortment as follows:",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "DONALD TRUMP, NAFTA, AND THE USMCA - THE BALANCE",
                    "url": "https://www.thebalance.com/donald-trump-nafta-4111368",
                    "text": "The USMCA was signed on Nov. 30, 2018, by U.S., Mexican, and Canadian leaders at that year's G-20 meeting. 6 . It was then sent to each country's legislature to be ratified. 7 After signing the agreement, Trump threatened to terminate NAFTA if Congress didn't approve the USMCA. 8. House Democrats approved the deal in December 2019.",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "VICE-CHAIRPERSON OF BITU AT NAFTAN JAILED - BELNP.ORG",
                    "url": "https://belnp.org/en/news/open/vice-chairperson-bitu-naftan-jailed",
                    "text": "Vice-chairperson of BITU at Naftan jailed. Today, 3 September, Vadzim Mikhailau, Vice-chairperson of the primary trade union organization of the Belarusian Independent Trade Union at JSC Naftan, was detained during a lunch break. The trade union is sure that the detention is connected with his trade union activity.",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "OFAC EXTENDS WIND-DOWN AUTHORIZATION INVOLVING BELARUSIAN ...",
                    "url": "https://www.mondaq.com/unitedstates/export-controls-trade-investment-sanctions/1059988/ofac-extends-wind-down-authorization-involving-belarusian-entities",
                    "text": "OFAC extended through June 3, 2021 the authorization of the wind down of transactions involving certain Belarusian entities, otherwise prohibited by the Belarus Sanctions Regulations.. In General License (\"GL\") No. 2H, OFAC authorized certain transactions with the following nine state-owned entities: Belarusian Oil Trade House, Belneftekhim, Belneftekhim USA, Inc., Belshina OAO, Grodno Azot ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "U.S. COMPANIES MUST WIND DOWN TRANSACTIONS WITH SANCTIONED ...",
                    "url": "https://www.mondaq.com/unitedstates/export-controls-trade-investment-sanctions/1066678/us-companies-must-wind-down-transactions-with-sanctioned-belarusian-state-owned-enterprises-by-june-3rd",
                    "text": "The Office of Foreign Assets Control (\"OFAC\") recently tightened sanctions on Belarus by revoking and replacing General License 2G with General License 2H. General License 2H now requires U.S. persons to wind down transactions involving the following Belarusian Specially Designated Nationals and their subsidiaries by June 3, 2021: ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "OIL SUPPLIES TO BELARUSIAN REFINERY NAFTAN WILL BE ... - TASS",
                    "url": "https://tass.com/economy/1060065",
                    "text": "MINSK, May 25 / TASS/. The deliveries of conditional Russian oil to the Belarusian refinery Naftan via pipeline transport should be fully restored before July 28, said Deputy Prime Minister of ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "OFAC REACTIVATES SANCTIONS AGAINST BELARUSSIAN COMPANIES ...",
                    "url": "https://www.steptoeinternationalcomplianceblog.com/2021/04/ofac-reactivates-sanctions-against-belarussian-companies-and-provides-45-day-wind-down-period/",
                    "text": "On April 19, 2021, OFAC effectively reactivated longstanding sanctions against nine Belarussian companies and their subsidiaries, revoking a general license that had authorized transactions involving those entities since 2015. These sanctions may impact a significant number of Belarussian companies, as several of the listed entities are large conglomerates.",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "NAFTAN, OOO COMPANY PROFILE - DNB.COM",
                    "url": "https://www.dnb.com/business-directory/company-profiles.naftan_ooo.5fc5b8d683c02668e8976b23028bb650.html",
                    "text": "Company Description: NAFTAN, OOO is located in Zhukovski, Moskovskaya Obl., Russian Federation and is part of the Petroleum and Petroleum Products Merchant Wholesalers Industry. NAFTAN, OOO has 80 employees at this location and generates $27.94 million in sales (USD). (Employees figure is estimated).",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "NEW SANCTIONS AGAINST BELARUS - UNITED STATES DEPARTMENT ...",
                    "url": "https://2009-2017.state.gov/r/pa/prs/ps/2011/08/170405.htm",
                    "text": "Washington, DC. August 11, 2011. Today, the United States imposed additional, new economic sanctions against four major Belarusian state-owned enterprises: the Belshina tire factory; Grodno Azot, which manufactures fertilizer; Grodno Khimvolokno, a fiber manufacturer; and Naftan, a major oil refinery. These four entities have been determined to ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "DESPITE U.S. JOB LOSSES, NAFTA BENEFITS TEXTILE ... - NEWSWISE",
                    "url": "https://www.newswise.com/articles/despite-us-job-losses-nafta-benefits-textile-apparel-industries",
                    "text": "Despite U.S. Job Losses, NAFTA Benefits Textile, Apparel Industries. Contacts: Eric Whittington or Patricia Divine. (336) 758-5030 or (800) 722-1622 eric.whittington@mba.wfu.edu. WINSTON-SALEM, N ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                },
                {
                    "title": "GOOGLE BLOCKED YOUTUBE-CHANNELS OF THE ORGANIZATIONS ...",
                    "url": "https://motolko.help/en-news/google-blocked-youtube-channels-of-the-organizations-related-to-lukashenko/",
                    "text": "Google blocked accounts of the Investigative Committee of the Republic of Belarus, Dana Holdings developer, JSC Belaruskali, OJSC Naftan and other organizations that came under US sanctions. This includes Youtube-channels, one of which was actively used by the Investigative Committee. \"Your Google account has been disabled in accordance with our regulations regarding export restrictions and ...",
                    "type": "NEGATIVE",
                    "checkDate": "2022-02-02 10:53:09"
                }
            ],
            "serviceGroupType": "AML"
        }
    ],
    "COMPANY": [
        {
            "status": {
                "serviceSuspected": true,
                "serviceUsed": true,
                "serviceFound": true,
                "checkSuccessful": true,
                "overallStatus": "SUSPECTED"
            },
            "serviceName": "CompanyCheck",
            "uid": "FW0BJRTBLGX82TJKDZ5DNCSH3",
            "errorMessage": null,
            "data": [
                {
                    "Name": "Naftan",
                    "Residence": "LT",
                    "CompanyHasHits": "true",
                    "ScoreAml": "2",
                    "RiskAml": "M",
                    "RiskElements": [
                        {
                            "RiskName": "ScoreAml",
                            "Explanation": "The company is located in a medium risk country"
                        },
                        {
                            "RiskName": "RiskAml",
                            "Explanation": "The AML risk for this company is MEDIUM"
                        },
                        {
                            "RiskName": "CompanyHasHits",
                            "Explanation": "The company's name is similar to names on sanctions' lists. Please check if the hits apply to the company."
                        }
                    ],
                    "HitsOnLists": [
                        {
                            "Name": "NAFTAN OAO",
                            "Forename": "UNKNOWN",
                            "Reason": "BELARUS Program",
                            "Nationality": "Unknown",
                            "BirthDate": "UNKNOWN",
                            "HitType": "Sanction",
                            "ListNumber": "12860",
                            "ListName": "OFAC",
                            "Score": "100",
                            "LastUpdateDate": "2018-08-23T00:00:00",
                            "IsPerson": false,
                            "IsActive": true
                        },
                        {
                            "Name": "NAFTAN OJSC",
                            "Forename": "UNKNOWN",
                            "Reason": "BELARUS Program",
                            "Nationality": "Unknown",
                            "BirthDate": "UNKNOWN",
                            "HitType": "Sanction",
                            "ListNumber": "12860",
                            "ListName": "OFAC",
                            "Score": "100",
                            "LastUpdateDate": "2018-08-23T00:00:00",
                            "IsPerson": false,
                            "IsActive": true
                        },
                        {
                            "Name": "NAFTAN",
                            "Forename": "UNKNOWN",
                            "Reason": "BELARUS Program",
                            "Nationality": "Unknown",
                            "BirthDate": "UNKNOWN",
                            "HitType": "Sanction",
                            "ListNumber": "12860",
                            "ListName": "OFAC",
                            "Score": "100",
                            "LastUpdateDate": "2018-08-23T00:00:00",
                            "IsPerson": false,
                            "IsActive": true
                        },
                        {
                            "Name": "NAFTAN PROIZVODSTVENNOYE OBYEDINENYE",
                            "Forename": "UNKNOWN",
                            "Reason": "BELARUS Program",
                            "Nationality": "Unknown",
                            "BirthDate": "UNKNOWN",
                            "HitType": "Sanction",
                            "ListNumber": "12860",
                            "ListName": "OFAC",
                            "Score": "100",
                            "LastUpdateDate": "2018-08-23T00:00:00",
                            "IsPerson": false,
                            "IsActive": true
                        },
                        {
                            "Name": "NAFTAN PRODUCTION ASSOCIATION",
                            "Forename": "UNKNOWN",
                            "Reason": "BELARUS Program",
                            "Nationality": "Unknown",
                            "BirthDate": "UNKNOWN",
                            "HitType": "Sanction",
                            "ListNumber": "12860",
                            "ListName": "OFAC",
                            "Score": "100",
                            "LastUpdateDate": "2018-08-23T00:00:00",
                            "IsPerson": false,
                            "IsActive": true
                        }
                    ],
                    "CompaniesFound": []
                }
            ],
            "serviceGroupType": "COMPANY"
        }
    ]
}
```