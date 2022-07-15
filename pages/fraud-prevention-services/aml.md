---
layout: default
title: AML, PEP & Sanctions, Adverse media
parent: Fraud prevention services
has_toc: true
nav_exclude: true
---

# AML, PEP & Sanctions, Adverse media

This service checks whether given person is in PEP or sanctions list, checks adverse media.

:::note  Pricing
Please contact Mark ID's sales team sales@Mark ID.com for a detailed price-sheet.
:::

## Callback data
The service check returns data associated with the person under check in JSON format.
Overall response can be seen in the table below:

### Overall response table

|JSON key          |Type        |Explanation                                                |
|------------------|------------|-----------------------------------------------------------|
|`status`          |`Object`    |The overall status of the service check.                   | 
|`data`            |`List`      |Data returned from services.                               |
|`serviceName`     |`String`    |The name of the service that was used to check a person.   |
|`serviceGroupType`|`String`    |The type of the service that was used. It is always `AML`. |
|`uid`             |`String`    |A unique identifier for a request. NOTE that it is unique only in one request scope. It is not unique globally. This parameter is only useful when doing multi-person checks.|
|`errorMessage`    |`String`    |In case of an error in the checking process a human readable error message is given.|

### Status table

|JSON key            |Type        |Explanation                                                             |
|--------------------|------------|------------------------------------------------------------------------|
|`serviceSuspected`  |`Bool`      |Indicates whether a service found a record about the person under check.| 
|`checkSuccessful`   |`Bool`      |Indicated whether an error occurred while doing a check.                |
|`serviceFound`      |`Bool`      |Indicates whether a service was found for a given country.              |
|`serviceUsed`       |`Bool`      |Indicated whether a service was used.                                   |
|`overallStatus`     |`String`    |A mapping string indicating overall status of the check.                |

### Data table for AML name check service
A service response in `data` field returns a list of objects which are detailed in the table below:

|JSON key            |Type        |Explanation                                                |
|--------------------|------------|-----------------------------------------------------------|
|`name`              |`String`    |The name of the person in the service's database.          | 
|`surname`           |`String`    |The surname of the person in the service's database.       |
|`nationality`       |`String`    |The nationality of the person in the service's database.   |
|`dob`               |`String`    |The date of birth of the person in the service's database. |
|`suspicion`         |`String`    |Tells the kind of the suspicion.<br/>Possible values:<br/>PEPS<br/>SANCTION<br/>INTERPOL<br/>OTHER|
|`reason`            |`String`    |Tells the reason of the kind of the suspicion.             |
|`score`             |`Integer`   |Confidence coefficient that the information about the person is true.|
|`lastUpdate`        |`String`    |Date of when the record was last updated.                  |
|`isPerson`          |`Bool`      |Indicates whether the record is about a person.            |
|`isActive`          |`Bool`      |Indicated whether a record is active.                      |
|`checkDate`         |`String`    |Date and time of when AML name service was checked.        |

### Data table for Adverse media service
A service response in `data` field returns a list of objects which are detailed in the table below:

|JSON key            |Type        |Explanation                                                |
|--------------------|------------|-----------------------------------------------------------|
|`title`             |`String`    |The title of the news article.                             | 
|`url`               |`String`    |Url pointing to the news article.                          |
|`text`              |`String`    |An short extract of the news article text.                 |
|`type`              |`String`    |The type of the news article.                              |
|`checkDate`         |`String`    |Date and time of when AML news service was checked.        |

## Example responses 
### Example response for AML name check service

```json
{
    "status": {
        "serviceSuspected": true,
        "checkSuccessful": true,
        "serviceFound": true,
        "serviceUsed": true,
        "overallStatus": "SUSPECTED"
    },
    "data": [
        {
            "name": "NAME",
            "surname": "SURNAME",
            "reason": "LITHUANIA: GOVERNMENT - PRESIDENT",
            "nationality": "LITHUANIA",
            "dob": "1956",
            "suspicion": "PEPS",
            "score": 100,
            "lastUpdate": "2018-10-24",
            "isPerson": true,
            "isActive": true,
            "checkDate": "2020-02-02 20:02:02"
        },
        {
            "name": "NAME",
            "surname": "SURNAME2",
            "reason": "LITHUANIA: GOVERNMENT - PRESIDENT",
            "nationality": "LITHUANIA",
            "dob": "1956",
            "suspicion": "PEPS",
            "score": 100,
            "lastUpdate": "2018-10-24",
            "isPerson": true,
            "isActive": true,
            "checkDate": "2020-02-02 20:02:02"
        }
    ],
    "serviceName": "PilotApiAmlV2",
    "serviceGroupType": "AML",
    "uid": "OHT8GR5ESRF5XROWE5ZGCC49A",
    "errorMessage": null
}
```

### Example response for AML adverse media service

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
