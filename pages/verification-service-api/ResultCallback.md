---
layout: default
title: Identity verification webhook
parent: Verification service API
has_toc: true
nav_order: 3
---

# Verification results webhook

import useBaseUrl from '@docusaurus/useBaseUrl';


When a client completes the verification - your registered webhook endpoint will receive a HTTP POST request containing data and status of the verification.

Your API **must** return HTTP response with status code `200`. Otherwise, a client will see a failed verification message and will be redirected to a  URL of a failed verification.

If you are experiencing any issues receiving a webhook - [webhook troubleshooting section](/pages/verification-service-api/ResultCallback#webhook-troubleshooting).

{: .note }
All of our webhooks are TLS encrypted and require a valid SSL certificate of your webhook endpoint, otherwise, the webhook sending will fail.

## Verification and webhook flow

### When to expect a webhook

There are certain points of time in identity verification process when your API should expect to receive a webhook.

Your API should expect to receive a webhook up to 20 minutes when:
- A client has successfully completed a verification
- A client has consecutively failed to identify himself and has reached maximum reattempts allowed in the verification platform.  
- A client was suspected for a fraudulent activity and his session was terminated.

Your API should not expect to receive up to 20 minutes callback when (In case of these events your API will receive a callback only when a **token** (token property `tokenExpiry`) or a **token session** (token property `sessionLength`) has expired. For more on these variables refer to [generating verification token](/API/GeneratingIdentificationToken)):
- A client has consecutively failed to identify himself and has not reached maximum reattempts allowed in the verification platform.
- A client did not start a verification session.

### Default callback

Default callback is represented in a UML activity graph below:


<img alt="Token generation UML activity diagram" width="600" src='https://documentation.markid.eu/resources/Default_Callback.png' />

1. Generate a token by sending an HTTP POST request to `https://ivs.markid.eu/api/v2/token` endpoint. The request must contain basic auth headers, where the username is the API key and the password is API secret. 
  
  More information can be found [here](/pages/verification-service-api/GeneratingIdentificationToken).
  
2. Redirect client to Mark ID platform. It can be done in several ways:
    - Client redirect to verification WEB platform
    - Client redirect to verification WEB platform (iFrame)
    - iOS SDK
    - Android SDK
3. Client submits all documents (tries to verify at least one time).  
  
  3.1. If a client doesn't upload all photos at least one time, for example, face photos are missing and only the document’s photo are captured after session time expires Mark ID system will send a callback about expired notification to the provided URL with overall status: ”EXPIRED”.

4. The client completes the verification process. 
When the verification process is complete, please check [here](/pages/fraud-prevention-services/FAQ#when-verification-process-is-complete).

  4.1. If a client captures all photos at least one time, but verification was unsuccessful while more attempts are available after session time expires Mark ID system will send a callback about expired notification to the provided URL with overall status: ”DENIED”.

5. After the verification process is completed or 4.1 callback sent we will perform full document analysis by automated system and human supervision. The Analysis process can take up to 20 minutes (on average about 10 minutes).
6. After the analysis process is done, we are sending all data to the registered callback URL.
7. Receiving all data sent by Mark ID to your provided endpoint.
8. Returning HTTP status code 200.
  
  8.1. HTTP status code is not 200. Trying to resend callback (max 3 times) every 0,5 seconds.

How many webhooks to expect:
If You get a 3.1 callback with overall status EXPIRED it would be just one callback.

If a client does not complete verification and 4.1 step occurs, the system will send a DENIED callback and after automated system and human supervision analysis, we will send the final callback.

In default flow (steps 1, 2, 3, 4, 5, 6, 7) You would get only one callback with final results after human supervision analysis.


### Webhooks with auto callback

Callback with automatic callback are represented in a UML activity graph below:

<img alt="Token generation UML activity diagram" width="600" src='https://documentation.markid.eu/resources/Default_Callback_with_auto.png' />


1. Generate a token by sending an HTTP POST request to `https://ivs.markid.eu/api/v2/token` endpoint. The request must contain basic auth headers, where the username is the API key and the password is API secret. 

  More information can be found [here](/pages/verification-service-api/GeneratingIdentificationToken).

2. Redirect the client to the Mark ID platform. It can be done in several ways:
    - Client redirect to verification WEB platform
    - Client redirect to verification WEB platform (iFrame)
    - iOS SDK
    - Android SDK
3. Client submits all documents (tries to verify at least one time).

  3.1. If a client doesn't upload all photos at least one time, for example, face photos are missing and only the document’s photo are captured after session time expires Mark ID system will send a callback about expired notification to the provided URL with overall status: ”EXPIRED”.

4. The client completes the verification process. 
When the verification process is complete, please check [here](/pages/fraud-prevention-services/FAQ#when-verification-process-is-complete).

  4.1. If a client captures all photos at least one time, but verification was unsuccessful while more attempts are available after session time expires Mark ID system will send a callback about expired notification to the provided URL with overall status: ”DENIED”.

5. After the verification process is completed we will perform full document analysis by the automated system.
6. After the automatic analysis process is done, we are sending all data with auto results to the registered callback URL.
7. Receiving all data with auto results sent by Mark ID to your provided endpoint.
8. Returning HTTP status code 200.

  8.1. HTTP status code is not 200. Trying to resend callback (max 3 times) every 0,5 seconds.

9. After the automatic verification process is completed and 200 status received we will perform full document analysis by human supervision. The Analysis process can take up to 20 minutes (on average about 10 minutes).
10. After the manual analysis process is done, we are sending all data with manual results to the registered callback URL.
11. Receiving all data with manual results sent by Mark ID to your provided endpoint.
12. Returning HTTP status code 200.

  12.1. HTTP status code is not 200. Trying to resend callback (max 3 times) every 0,5 seconds.

You should expect two webhooks:
- Callback after automatic analysis
- Callback after manual review

If case 3.1 occurs You should expect to get only one callback:
Callback about an expired session


## Callback structure

Request HTTP body is in JSON format which is described in tables below:

| JSON key             | Type     | Constraints            | Explanation                                                                                                                                                                                                                     |
|----------------------|----------|------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `final`              | `Bool  ` | -                      | If `true`, this callback is likely the final callback for the verification (unless it will be repeatedly manually reviewed). If `false`, you will receive another callback for that verification, when it is reviewed manually. |
| `platform`           | `String` | - Max length 30        | Tells from which device (platform) a client completed a verification. <br/>Possible values:<br/>- PC<br/>- MOBILE<br/>- TABLET<br/>- MOBILE_APP<br/>- MOBILE_SDK<br/>- OTHER                                                    |
| `status`             | `Object` | -                      | Dictionary that contains the status of the verification. [Refer to status table](#verification-status-table).                                                                                                                   |
| `data`               | `Object` | -                      | Dictionary that contains parsed data from clients identity document. [Refer to data table](#data-table).                                                                                                                        |
| `fileUrls`           | `Object` | -                      | Dictionary that contains url links to download or view client's verification photos and videos. [Refer to file urls table](#file-urls-table)                                                                                    |
| `aml`                | `Object` | -                      | Dictionary that contains anti-money-laundering (AML) service data. Only applicable if AML is enabled for you. [Refer to AML documentation](/pages/fraud-prevention-services/aml).                                                                         |
| `lid`                | `Object` | -                      | Dictionary that contains lost-invalid-documents (LID) service data. Only applicable if LID is enabled for you. [Refer to LID documentation](/pages/fraud-prevention-services/lid).                                                                        |
| `scanRef`            | `String` | - Max length 36        | A unique string to trace back a verification on Mark ID’s side.                                                                                                                                                                  |
| `externalRef`        | `String` | - Max length 40        | A unique string for external reference to link better the client to you and the Mark ID system.                                                                                                                                  |
| `clientId`           | `String` | - Max length 100       | A unique string to trace back a client on your side.                                                                                                                                                                            |
| `startTime`          | `Int   ` | -                      | A timestamp of when a client starts the verification process.                                                                                                                                                                   |
| `finishTime`         | `Int   ` | -                      | A timestamp of when the final decision for automatic processing was made.                                                                                                                                                       |
| `clientIp`           | `String` | - Max length 39        | A string of client's system IP address.                                                                                                                                                                                         |
| `clientIpCountry`    | `String` | - Country alpha-2 code | A country code of the client's system IP address.                                                                                                                                                                               |
| `clientLocation`     | `String` | - Max length 39        | Full information available about the client's current location based on the IP address.                                                                                                                                         |
| `gdcMatch`           | `Bool`   | -                      | Indicates if provided information in [additional data](/pages/verification-service-api/AdditionalSteps#sending-additional-data) matches any entry from [global data sources](/pages/fraud-prevention-services/checkGdc) databases.                                             |
| `manualAddress`      | `String` | - Max length 255       | Client address parsed from utility bill. Only occurs if utility bill was uploaded.                                                                                                                                              |
| `manualAddressMatch` | `Bool`   | -                      | Indicates if the document's address matched with the address provided by the partner after the manual check.                                                                                                                    |

### Verification status table

| JSON key           | Type     | Constraints          | Explanation                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
|--------------------|----------|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `overall`          | `String` | - Max length 30      | An overall status of the verification. It is a combination of manual and automatic verification results. <br/>Possible values:<br/>- APPROVED<br/>- DENIED<br/>- SUSPECTED \* <br/>- EXPIRED<br/> <br/> \* Overall status of the verification. It depends on auto or manual webhook, but if face status:`autoFace`, `manualFace` is `FACE_MATCH` and document status `autoDocument`, `manualDocument` is `DOC_VALIDATED`, then the verification is approved. If other statuses exist, for example: `DOC_NOT_FOUND`, `FACE_MISMATCH`, then verification is declined. If any `fraudTags` or `mismatchTags` exist for the verification, then status can be set as `SUSPECTED`, because the identification caught some discrepancies. <strong>If verification is approved(`FACE_MATCH` and `DOC_VALIDATED`) but overall status `SUSPECTED` it should be decided on your end to allow or decline this client.</strong> <br/> [For value explanations refer to status vocabulary](/pages/fraud-prevention-services/Vocabulary#verification-status-values-vocabulary) |
| `fraudTags`        | `List  ` | -                    | A list of fraud tags (strings) indicating why verification was `SUSPECTED`. <br/>Possible values:<br/>- FACE_SUSPECTED<br/>- FACE_BLACKLISTED<br/>- DOC_FACE_BLACKLISTED<br/>- DOC_MOBILE_PHOTO<br/>- DEV_TOOLS_OPENED<br/>- DOC_PRINT_SPOOFED<br/>- FAKE_PHOTO<br/>- AML_SUSPECTION<br/>- AML_FAILED<br/>- LID_SUSPECTION<br/>- LID_FAILED<br/>- UTILITY_ADDRESS_CHECK_FAILURE <br/>- VIRTUAL_CAMERA <br/>- FACE_IN_BLACKLIST <br/>- DOC_FACE_IN_BLACKLIST <br/>- DUPLICATE_FACE <br/>- DUPLICATE_DOC_FACE <br/>[For value explanations refer to status vocabulary](/pages/fraud-prevention-services/Vocabulary#verification-status-values-vocabulary).                                                                                                                                                                                                                                                                                                                                                                                                       |
| `mismatchTags    ` | `List  ` | -                    | A list of mismatch tags (strings) indicating where partner provided document information does not match with the read information. Verification `overall` status was `SUSPECTED` if any mismatchTags exist.  <br/>Possible values: <br/>-NAME <br/>-SURNAME <br/>-DOCUMENT_NUMBER <br/>-PERSONAL_CODE <br/>-EXPIRY_DATE <br/>-DATE_OF_BIRTH <br/>-DATE_OF_ISSUE <br/>-FULL_NAME <br/>-UNDER_AGE <br/>-UNKNOWN_AGE <br/>-SEX <br/>-NATIONALITY <br/>-INVALID_ADDITIONAL_STEP <br/>-ADDITIONAL_STEP_NOT_FOUND <br/>-GDC_NOT_MATCH <br/>-DOC_INFO_MISMATCH <br/>-ADDITIONAL_STEP_INFORMATION_MISMATCH <br/>-EXPIRED_ADDITIONAL_STEP_INFORMATION <br/>[For value explanations refer to status vocabulary](/pages/fraud-prevention-services/Vocabulary#verification-status-values-vocabulary).                                                                                                                                                                                                                                                                      |
| `autoFace`         | `String` | - Max length 30      | An automatic face analysis result (decision made by an automated platform). <br/>Possible values:<br/>- FACE_MATCH<br/>- FACE_MISMATCH<br/>- NO_FACE_FOUND<br/>- TOO_MANY_FACES<br/>- FACE_TOO_BLURRY<br/>- FACE_GLARED<br/>- FACE_UNCERTAIN<br/>- FACE_NOT_ANALYSED<br/>- FACE_ERROR<br/>- AUTO_UNVERIFIABLE<br/>- FAKE_FACE<br/>[For value explanations refer to status vocabulary](/pages/fraud-prevention-services/Vocabulary#verification-status-values-vocabulary).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `manualFace`       | `String` | - Max length 30      | A Final face analysis result (decision made by a automatic system and human). Possible values: <br/>- FACE_MATCH<br/>- FACE_MISMATCH<br/>- NO_FACE_FOUND<br/>- TOO_MANY_FACES<br/>- FACE_UNCERTAIN<br/>- FAKE_FACE<br/>[For value explanations refer to status vocabulary](/pages/fraud-prevention-services/Vocabulary#verification-status-values-vocabulary).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `autoDocument`     | `String` | - Max length 30      | An automatic document analysis result (decision made by an automated platform).<br/>Possible values:<br/>- DOC_VALIDATED<br/>- DOC_INFO_MISMATCH<br/>- DOC_NOT_FOUND<br/>- DOC_NOT_FULLY_VISIBLE<br/>- DOC_NOT_SUPPORTED<br/>- DOC_FACE_NOT_FOUND<br/>- DOC_TOO_BLURRY<br/>- DOC_GLARED<br/>- DOC_FACE_GLARED<br/>- MRZ_NOT_FOUND<br/>- MRZ_OCR_READING_ERROR<br/>- BARCODE_NOT_FOUND<br/>- DOC_EXPIRED<br/>- COUNTRY_MISMATCH<br/>- DOC_TYPE_MISMATCH<br/>- DOC_SIDE_MISMATCH<br/>- DOC_DAMAGED<br/>- DOC_FAKE<br/>- DOC_ERROR<br/>- AUTO_UNVERIFIABLE<br/>- DOC_NOT_ANALYSED<br/>- DOC_NAME_ERROR<br/>- DOC_SURNAME_ERROR<br/>- DOC_EXPIRY_ERROR<br/>- DOC_DOB_ERROR<br/>- DOC_PERSONAL_NUMBER_ERROR<br/>- DOC_NUMBER_ERROR<br/>- DOC_DATE_OF_ISSUE_ERROR<br/>- DOC_SEX_ERROR<br/>- DOC_NATIONALITY_ERROR<br/>- COUNTRY_NOT_SUPPORTED<br/>- DOC_PERSONAL_CODE_INVALID<br/>- DOC_SPOOF_DETECTED<br/>- MRZ_INVALID<br/>[For value explanations refer to status vocabulary](/pages/fraud-prevention-services/Vocabulary#verification-status-values-vocabulary). |
| `manualDocument`   | `String` | <br/>- Max length 30 | A Final document analysis result (decision made by a automatic system and human). Possible values:<br/>- DOC_VALIDATED<br/>- DOC_NOT_FULLY_VISIBLE<br/>- DOC_NOT_SUPPORTED<br/>- DOC_EXPIRED<br/>- DOC_DAMAGED<br/>- DOC_FAKE<br/>- DOC_PERSONAL_CODE_INVALID<br/>- MRZ_INVALID<br/>[For value explanations refer to status vocabulary](/pages/fraud-prevention-services/Vocabulary#verification-status-values-vocabulary).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `additionalSteps`  | `String` | - Max length 30      | An additional steps result (decision made by  human). <br/>Possible values:<br/>- VALID<br/>- INVALID<br/>- NOT_FOUND<br/>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |      

### Face and document statuses after manual review table

| JSON key           | Type     | Constraints          | Explanation                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
|--------------------|----------|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `manualFace`       | `String` | - Max length 30      | A Final face analysis result (decision made by a automatic system and human). Possible values: <br/>- FACE_MATCH<br/>- FACE_MISMATCH<br/>- NO_FACE_FOUND<br/>- TOO_MANY_FACES<br/>- FACE_UNCERTAIN<br/>- FAKE_FACE<br/>[For value explanations refer to status vocabulary](/pages/fraud-prevention-services/Vocabulary#verification-status-values-vocabulary).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `manualDocument`   | `String` | <br/>- Max length 30 | A Final document analysis result (decision made by a automatic system and human). Possible values:<br/>- DOC_VALIDATED<br/>- DOC_NOT_FULLY_VISIBLE<br/>- DOC_NOT_SUPPORTED<br/>- DOC_EXPIRED<br/>- DOC_DAMAGED<br/>- DOC_FAKE<br/>- DOC_PERSONAL_CODE_INVALID<br/>- MRZ_INVALID<br/>[For value explanations refer to status vocabulary](/pages/fraud-prevention-services/Vocabulary#verification-status-values-vocabulary).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |

### Data table

Note, that any of the specified fields below can be `null`. Also, some fields in original language could have symbols
encoded in UTF-16.

| JSON key                 | Type     | Constraints                   | Explanation                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|--------------------------|----------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `docFirstName`           | `String` | - Max length 100              | Clients name parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `docLastName`            | `String` | - Max length 100              | Clients surname parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `docNumber`              | `String` | - Max length 15               | Clients document number parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `docPersonalCode`        | `String` | - Max length 15               | Clients personal code parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `docExpiry`              | `String` | - Max length 10               | Clients document expiry date parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `docDob`                 | `String` | - Max length 10               | Clients date of birth parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `docDateOfIssue`         | `String` | - Max length 10               | Clients date of issue parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `docType`                | `String` | - Max length 30               | Clients used document type to complete verification. <br/>Possible values:    <br/>- ID_CARD <br/>- PASSPORT <br/>- RESIDENCE_PERMIT <br/>- DRIVER_LICENSE <br/>- PAN_CARD <br/>- AADHAAR <br/>- OTHER <br/>- VISA <br/>- BORDER_CROSSING <br/>- ASYLUM <br/>- NATIONAL_PASSPORT <br/>- INTERNATIONAL_PASSPORT <br/>- PROVISIONAL_DRIVER_LICENSE <br/>- VOTER_CARD <br/>- OLD_ID_CARD <br/>- TRAVEL_CARD <br/>- PHOTO_CARD <br/>- MILITARY_CARD <br/>- PROOF_OF_AGE_CARD <br/>- DIPLOMATIC_ID |
| `docSex`                 | `String` | - Max length 9                | Clients sex parsed from the document. <br/>Possible values:<br/>- MALE<br/>- FEMALE<br/>- UNDEFINED                                                                                                                                                                                                                                                                                                                                                                                           |
| `docNationality`         | `String` | - Country alpha-2 code        | Clients nationality parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `docIssuingCountry`      | `String` | - Country alpha-2 code        | Clients documents issuing country parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `birthPlace`             | `String` | - Max length 60               | Clients birth place parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `authority`              | `String` | - Max length 60               | The authority of the document parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `address`                | `String` | - Max length 80               | Clients address parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `mothersMaidenName`      | `String` | - Max length 80               | Clients mothers maiden name parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| `driverLicenseCategory`  | `String` | - Max length 30               | Clients driving license categories (classes) parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `manuallyDataChanged`    | `Bool`   | -                             | Indicates whether a manual reviewer has changed any parsed data from the document. Applicable only when a human reviews a verification. In automated verification response this value is `false`.                                                                                                                                                                                                                                                                                             |
| `fullName`               | `String` | - Max length 100              | Clients full name parsed from the document.                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 
| `selectedCountry`        | `String` | - Any country in alpha-2 code | Country which was selected in verification process.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `orgFirstName`           | `String` | - Max length 60               | Client name parsed from the document in original language.                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `orgLastName`            | `String` | - Max length 60               | Client surname parsed from the document in original language.                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `orgNationality`         | `String` | - Max length 30               | Client nationality parsed from the document in original language.                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `orgBirthPlace`          | `String` | - Max length 60               | Client birth place parsed from the document in original language.                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `orgAuthority`           | `String` | - Max length 60               | Client document authority categories parsed from the document in original language.                                                                                                                                                                                                                                                                                                                                                                                                           |
| `orgAddress`             | `String` | - Max length 60               | Client address parsed from the document in original language.                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `selectedCountry`        | `String` | - Country alpha-2 code        | Client's selected country in the verification process.                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `ageEstimate`            | `String` | - Max length 10               | Client age detected from selfie when liveness is on. <br/>Possible values:<br/>- UNDER_13<br/>- OVER_13<br/>- OVER_18<br/>- OVER_22<br/>- OVER_25<br/>- OVER_30                                                                                                                                                                                                                                                                                                                               |
| `clientIpProxyRiskLevel` | `String` | - Max length 11               | Client's IP address proxy risk level. If this feature isn't enabled or this is not the final callback with overall status `APPROVED` - this field will be `null`. <br/>Possible values:<br/>- VERY_LOW<br/>- LOW<br/>- MEDIUM<br/>- HIGH<br/>- VERY_HIGH<br/>- NOT_CHECKED                                                                                                                                                                                                                    |
| `duplicateFaces`         | `List`   | -                             | Indicates scanRefs of other clients which selfie faces were matched with this one's verification. If list is empty, a value is `null`.                                                                                                                                                                                                                                                                                                                                                        |
| `duplicateDocFaces`      | `List`   | -                             | Indicates scanRefs of other clients which document faces were matched with this one's verification. If list is empty, a value is `null`.                                                                                                                                                                                                                                                                                                                                                      |
|`additionalData`        |`JSON object`|Must be used with [additionalSteps](/pages/verification-service-api/AdditionalSteps)|Additional data provided alongside any [additionalSteps](/pages/verification-service-api/AdditionalSteps), for example - Social Security Number in `UTILITY_BILL`. Must be used with [additionalSteps](/pages/verification-service-api/AdditionalSteps)|


### File URLs table

| JSON key      | Type           | Can be null | Constraints      | Explanation                                                                                    |
|---------------|----------------|-------------|------------------|------------------------------------------------------------------------------------------------|
| `FRONT`       | `String (URL)` | Yes         | - Max length 500 | A URL to download front document side photo with which a client has completed a verification. |
| `BACK`        | `String (URL)` | Yes         | - Max length 500 | A URL to download back document side photo with which a client has completed a verification.  |
| `FACE`        | `String (URL)` | Yes         | - Max length 500 | A URL to download face photo with which a client has completed a verification.                |
| `FRONT_VIDEO` | `String (URL)` | Yes         | - Max length 500 | A URL to download the video of a client taking the front photo.                                |
| `BACK_VIDEO`  | `String (URL)` | Yes         | - Max length 500 | A URL to download the video of a client taking the back photo.                                 |
| `FACE_VIDEO`  | `String (URL)` | Yes         | - Max length 500 | A URL to download the video of a client taking the face photo.                                 |

## Examples

This is an example JSON body in the callback HTTP request.

```json
{
  "clientId": "123",
  "scanRef": "scan-ref",
  "externalRef": "external-ref",
  "platform": "MOBILE_APP",
  "startTime": 1554726960, 
  "finishTime": 1554727002,
  "clientIp": 192.0.2.0,
  "clientIpCountry": "LT",
  "clientLocation": "Kaunas, Lithuania",
  "final": false,
  "status": {
    "overall": "APPROVED",
    "suspicionReasons": [],
    "mismatchTags": [],
    "fraudTags": [],
    "autoDocument": "DOC_VALIDATED",
    "autoFace": "FACE_MATCH",
    "manualDocument": "DOC_VALIDATED",
    "manualFace": "FACE_MATCH",
    "additionalSteps": null
  },
  "data": {
    "docFirstName": "FIRST-NAME-EXAMPLE",
    "docLastName": "LAST-NAME-EXAMPLE",
    "docNumber": "XXXXXXXXX",
    "docPersonalCode": "XXXXXXXXX",
    "docExpiry": "YYYY-MM-DD",
    "docDob": "YYYY-MM-DD",
    "docType": "ID_CARD",
    "docSex": "UNDEFINED",
    "docNationality": "LT",
    "docIssuingCountry": "LT",
    "manuallyDataChanged": false,
    "fullName": "FULL-NAME-EXAMPLE",
    "selectedCountry": "LT",
    "orgFirstName": "FIRST-NAME-EXAMPLE",
    "orgLastName":  "LAST-NAME-EXAMPLE",
    "orgNationality": "LIETUVOS",
    "orgBirthPlace": "ŠILUVA",
    "orgAuthority": null,
    "orgAddress": null,
    "selectedCountry": "LT",
    "ageEstimate": "UNDER_13",
    "clientIpProxyRiskLevel": "LOW",
    "duplicateDocFaces": null,
    "duplicateFaces": null,
    "additionalData": {
    "UTILITY_BILL": {
      "ssn": {
        "value": "ssn number",
        "status": "MATCH"
      }
    }
  },
  "manualAddress": null,
  "manualAddressMatch": false,
  },
  "fileUrls": {
    "FRONT": "https://s3.eu-west-1.amazonaws.com/production.users.storage/users_storage/users/<HASH>/FRONT.png?AWSAccessKeyId=<KEY>&Signature=<SIG>&Expires=<STAMP>",
    "BACK": "https://s3.eu-west-1.amazonaws.com/production.users.storage/users_storage/users/<HASH>/BACK.png?AWSAccessKeyId=<KEY>&Signature=<SIG>&Expires=<STAMP>",
    "FACE": "https://s3.eu-west-1.amazonaws.com/production.users.storage/users_storage/users/<HASH>/FACE.png?AWSAccessKeyId=<KEY>&Signature=<SIG>&Expires=<STAMP>"
  },
  "AML": [
  {
      "status": {
        "serviceSuspected": false,
        "checkSuccessful": true,
        "serviceFound": true,
        "serviceUsed": true,
        "overallStatus": "NOT_SUSPECTED"
      },
      "data": [],
      "serviceName": "PilotApiAmlV2",
      "serviceGroupType": "AML",
      "uid": "OHT8GR5ESRF5XROWE5ZGCC123",
      "errorMessage": null
    }
  ],
  "LID": [
  {
      "status": {
        "serviceSuspected": false,
        "checkSuccessful": true,
        "serviceFound": true,
        "serviceUsed": true,
        "overallStatus": "NOT_SUSPECTED"
      },
      "data": [],
      "serviceName": "IrdInvalidPapers",
      "serviceGroupType": "LID",
      "uid": "OHT8GR5ESRF5XROWE5ZGCC123",
      "errorMessage": null
    }
  ]
}
```

## Webhook troubleshooting

Not receiving a callback? These are the first steps you should take in order to resolve an issue:
- Ensure that you have provided a valid callback endpoint (it does not contain typos and is a fully specified URL with HTTP schema, port and domain name).
- Ensure that the provided endpoint can be reached from internet.
- Ensure that your SSL is set up correctly. Our system can only send webhooks to URLs with valid SSL certificates.
- Ensure that you are **actually** not getting a callback and your framework is not accidentally returning some other HTTP response e.g. 422 or 500.

