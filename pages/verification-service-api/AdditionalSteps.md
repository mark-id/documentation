---
layout: default
title: Additional steps
parent: Verification service API
has_toc: true
nav_order: 7
---

import useBaseUrl from '@docusaurus/useBaseUrl';

## Additional steps
Using this functionality will let you add additional steps for your users to complete in the verification process. These steps could include utility bill upload or an extra photo of your client. The text can be fully customizable.

{: .note }
To make this functionality available, please contact sales@markid.lt if your contract does not include additional steps. For technical questions please contact our support team using your Dashboard account.

### Utility bill
You can add a requirement for your users to upload a utility bill in their verification process. Mark ID can extract and compare the adresses if you provide one in token generation.

{: .note }
Please inform your users that regulations do not allow utility bills older than 3 months to be accepted.

### Sending a request for utility bill
To [generate a token](GeneratingIdentificationToken) with utility bill step, simply add `"utilityBill": true` to your request like the example below:

```json
{
    "clientId":"2",
    "utilityBill": true
}
```
The response should be similar to this:

```json
{
    "message": "Token created successfully",
    "authToken": "cBqwefLQK6jEA20CJnq12r01cge00mlvPrjTGM4",
    "scanRef": "6a31253e-e10a-11eb-cc95-02cb49d118ed",
    "clientId": "2",
    "personScanRef": "2",
    "firstName": null,
    "lastName": null,
    "successUrl": null,
    "errorUrl": null,
    "unverifiedUrl": null,
    "callbackUrl": null,
    "locale": null,
    "country": null,
    "expiryTime": 3600,
    "sessionLength": 600,
    "documents": [
        "ID_CARD",
        "PASSPORT",
        "DRIVER_LICENSE",
        "RESIDENCE_PERMIT"
    ],
    "dateOfBirth": null,
    "dateOfExpiry": null,
    "dateOfIssue": null,
    "nationality": null,
    "personalNumber": null,
    "documentNumber": null,
    "sex": null,
    "address": null,
    "showInstructions": true,
    "tokenType": "IDENTIFICATION",
    "videoCallQuestions": [],
    "utilityBill": true,
    "additionalSteps": {
        "ALL": {
            "ALL": {
                "UTILITY_BILL": {
                    "texts": {
                        "lt": {
                            "name": "Adreso įrodymo dokumento fotografavimas",
                            "description": "Prašome įkelti adreso įrodymo dokumento nuotrauką."
                        },
                        "en": {
                            "name": "Proof of address document capturing",
                            "description": "Please upload the proof of address document photo."
                        },
                        "pl": {
                            "name": "Dowód przechwycenia dokumentu adresowego",
                            "description": "Prześlij dokument potwierdzający adres zdjęcia."
                        },
                        "ru": {
                            "name": "Подтверждение захвата адресного документа",
                            "description": "Пожалуйста, загрузите документ, подтверждающий адрес документа."
                        },
                        "lv": {
                            "name": "Adresu apliecinošs dokuments",
                            "description": "Lūdzu, augšupielādējiet adreses apliecinājuma dokumenta fotoattēlu."
                        },
                        "ro": {
                            "name": "Dovada capturii documentului de adresă",
                            "description": "Vă rugăm să încărcați dovada de fotografie a documentului adresei."
                        },
                        "it": {
                            "name": "Prova di acquisizione del documento di indirizzo",
                            "description": "Carica la foto del documento di prova dell'indirizzo."
                        },
                        "fr": {
                            "name": "Capture d'un document de preuve d'adresse",
                            "description": "Veuillez télécharger la photo du justificatif de domicile."
                        },
                        "es": {
                            "name": "Captura del documento de prueba de domicilio",
                            "description": "Por favor, suba la foto del documento de prueba de domicilio."
                        },
                        "de": {
                            "name": "Erfassung von Dokumenten zum Nachweis der Adresse",
                            "description": "Upload bitte das Foto des Adressnachweisdokuments."
                        },
                        "hu": {
                            "name": "Lakcímet igazoló dokumentum lefotózása",
                            "description": "Kérjük töltse fel a lakcímet igazoló dokumentum fényképét."
                        },
                        "jp": {
                            "name": "「住所文書の証明を取り込む」",
                            "description": "「住所文書写真の証明をアップロードしてください」"
                        }
                    },
                    "settings": {
                        "canUpload": true,
                        "canCapture": false,
                        "canUploadPDF": true
                    }
                }
            }
        }
    },
    "externalRef": null,
    "digitString": null
}
```
:::note
The utility bill JSON text, settings, document types and locales can be changed using "additionalSteps" key. The "utilityBill" and "additionalSteps" keys can't be used both in the same request.
:::

### Custom additional steps
You can add a requirement for your users to upload an additional photo. For example, a selfie of the user while holding their document next to them or a photo where the client holds a piece of paper with handwritten text that you specify. The text of the required additional step is fully customizable.

### Sending request for additional steps
To [generate a token](GeneratingIdentificationToken) with a custom additional step, add `additionalSteps` with a valid JSON to your request and send a HTTP POST request to: https://ivs.markid.eu/api/v2/token

The request must contain basic auth headers where username is API key and password is API secret.

```json
{
    "clientId":"2",
    "additionalSteps": {
        "ALL": { 
            "ALL": {
                "ADDITIONAL_PHOTO": {
                    "texts": {
                        "en": {
                            "name": "Please upload a selfie",
                            "description": "Please upload your selfie with your passport next to you."
                        }
                    },
                    "settings": {
                        "canUpload": true,
                        "canCapture": false,
                        "canUploadPDF": false
                    }
                }
            }
        }
    }
}
```

### Sending additional data

You can also include **SSN**(Social Security Number) check with `additionalData` provided alongside any `additionalStep` for example - `UTILITY_BILL`.

**SSN** extraction or comparison works only if you have one of these proof of address types enabled in your environment - `EXTRACT` or `COMPARE`.

Please contact Mark ID sales team to discuss the possibilities of including these features in your environments.

#### Example request:
To [generate a token](GeneratingIdentificationToken) with a custom additional step and `additionalData`, add `additionalSteps` with a valid JSON to your request and send a HTTP POST request to: `https://ivs.markid.eu/api/v2/token`

The request must contain basic auth headers where username is API key and password is API secret.

```json
{
   "clientId":"123",
   "additionalData":{
      "UTILITY_BILL":{
         "address":"test-12",
         "ssn":"123123"
      }
   },
   "additionalSteps":{
      "ALL":{
         "ALL":{
            "UTILITY_BILL":{
               "type":"EXTRACT",
               "fields":[
                  "ssn", "address"
               ],
               "texts": {
                  "en":{
                     "name":"Proof of address document capturing",
                     "description":"Please upload the proof of address document photo."
                  }
               },
               "settings":{
                  "canUpload":true,
                  "canCapture":false,
                  "canUploadPDF":true
               }
            }
         }
      }
   }
}
```
#### Example response:

After a successful request, you will receive a status 201(created) and a JSON body similar to below example:

```json
{
    "message": "Token created successfully",
    "authToken": "57K5hu3HSTOLCg4UFNpdwZqsC8tDG0PLnvgcY4sC",
    "scanRef": "10bb0ade-4784-11ec-a167-02476f3f1509",
    "clientId": "123",
    "personScanRef": "123",
    "firstName": null,
    "lastName": null,
    "successUrl": null,
    "errorUrl": null,
    "unverifiedUrl": null,
    "callbackUrl": null,
    "locale": "en",
    "country": null,
    "expiryTime": 3600,
    "sessionLength": 600,
    "documents": [
        "ID_CARD",
        "PASSPORT",
        "DRIVER_LICENSE",
        "RESIDENCE_PERMIT"
    ],
    "dateOfBirth": null,
    "dateOfExpiry": null,
    "dateOfIssue": null,
    "nationality": null,
    "personalNumber": null,
    "documentNumber": null,
    "sex": null,
    "address": null,
    "showInstructions": true,
    "tokenType": "IDENTIFICATION",
    "videoCallQuestions": [],
    "utilityBill": null,
    "additionalSteps": {
        "ALL": {
            "ALL": {
                "UTILITY_BILL": {
                    "type": "EXTRACT",
                    "fields": [
                        "ssn",
                        "address"
                    ],
                    "texts": {
                        "en": {
                            "name": "Proof of address document capturing",
                            "description": "Please upload the proof of address document photo."
                        }
                    },
                    "settings": {
                        "canUpload": true,
                        "canCapture": false,
                        "canUploadPDF": true
                    }
                }
            }
        }
    },
    "additionalData": {
        "UTILITY_BILL": {
            "address":"test-12",
            "ssn": "123123"
        }
    },
    "externalRef": null,
    "digitString": null
}
```

:::note
Using the generated token above, your user will now be able to upload their proof of address document and our manual review team will be able to extract or compare the ***SSN*** and/or ***Address***. Please refer to the value explanations below and expected webhook callback response information that can be found [here](/pages/verification-service-api/ResultCallback).
:::

### Additional step reupload

If your user uploaded an invalid additional step, for example, a utility bill that is older than 3 months, there is a possibility to reupload the file without the need of undergoing the verification process again.

This can be done by sending an HTTP request to this endpoint `https://ivs.markid.eu/api/v2/upload-additional-step`

The request must contain basic auth headers where username is API key and password is API secret.

#### Example request:

```json
{
    "scanRef": "3f2d0e6a-0a37-11ec-a45b-025ad99a18e7",
    "image": {{Base64 image}},
    "step": "UTILITY_BILL",
    "additionalData": {}
}
```
`additionalData` is not required. However, you can pass values like {"address": "some address"} that our manual review team will use for comparison. Any data from the previous verification will be overriden after the new request.

{: .note }
Requests sent to the `https://ivs.markid.eu/api/v2/upload-additional-step` endpoint will only work if the provided `scanRef` had custom additional steps provided in token generation.

### Value explanation
* The first `ALL` value refers to country selection in alpha2 format. If you include specific countries in this value, the additional steps will only be added to these countries. `ALL` is used to include all countries.

* The second `ALL` value refers to document type used for that additional step. Supported values: `ID_CARD`, `PASSPORT`, `RESIDENCE_PERMIT`, `DRIVER_LICENSE`, `PAN_CARD`, `AADHAAR`, `OTHER`. `ALL` is used to include all supported documents in your environment.

* `ADDITIONAL_PHOTO` this is a variable that can be changed according to the step you're going to use.

* `type` There can be 3 different types and only 1 type can be enabled in your environment, depending on your contract.

* `UPLOAD` this type allows upload of the file only.

* `EXTRACT` this type allows our manual review team to type in the information from the utility bill document, such as address, SSN, etc. That information will be visible in the verification's pop-up or webhook callback.

* `COMPARE` this type allows manual review team to compare the data that our partners sent in the token generation with the data visible in the client's uploaded file. After the comparison, the possible values are: `MATCH`, `NOT_MATCH`, `NOT_FOUND`, `NO_DATA`. These statuses will be included in the webhook callback 

* `fields` name of the field in the `additionalData`

* `texts` support multiple locales that you can use for your `name` and `description`. Supported values are - `lt`, `en`, `ru`, `pl`, `lv`, `ro`, `it`, `et`, `fr`, `sv`, `es`, `hu`, `ja`, `bg`
* Both `name` and `description` can be changed by your own text for each language.

* The settings `canUpload`,`canCapture`,`canUploadPDF` only accepts boolean true or false.
* `canUpload` setting will allow/disallow user to upload the photo from their device.
* `canCapture` setting will allow/disallow user to capture the photo in their verification window.
* `canUploadPDF` setting will allow/disallow user to upload the file in PDF format.

After generating a token using the example above, your user will see additional step window with the text we indicated.


<p align="center">
  <img src='https://documentation.markid.eu/resources/ADDITIONALSTEP.png' alt="Additional Steps" width="450"/>
</p>

{: .note }
Our tech support team can add these additional steps to your environment's settings so these steps will have to be done by all users that go through verification process without the need to include them in the request each time. Please contact our support team via Dashboard and we'll gladly assist.

