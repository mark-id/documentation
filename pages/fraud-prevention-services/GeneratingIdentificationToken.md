---
layout: default
title: Create verification session
parent: Fraud prevention services
has_toc: true
nav_exclude: true
---

import useBaseUrl from '@docusaurus/useBaseUrl';

# Create verification session 

If you have ***API key*** and ***API secret*** you can create a verification token.

:::note
***API key*** and ***API secret*** can be retrieved by contacting *iDenfy's tech support* or *iDenfy's sales team*:
- sales@idenfy.com
- info@idenfy.com
- via Dashboard
:::

### Graphical representation of token generation (UML activity)

<img alt="Token generation UML activity diagram" width="450" src='https://documentation.markid.eu/resources/TokenGenerationUmlActivityDiagram.jpg' />
<img alt="Token generation UML activity diagram" width="450" src={useBaseUrl('img/TokenGenerationUmlActivityDiagram.jpg')} />

### Sending request
Send a *HTTP POST* request to: `https://ivs.idenfy.com/api/v2/token` 

The request must contain *basic auth* headers where *username* is *API key* and *password* is *API secret*.


#### We recommend:
Provide only clientId value while generating a token. 
Do not provide whole client information. Often clients make mistakes and cannot pass the verification successfully.
Use client information (name, surname and etc.) from our system's [webhook](/pages/fraud-prevention-services/ResultCallback). This would prevent errors and improve user experience.

:::caution

If provided information is incorrect or different than information found in the document, you would get verification `overall` status `SUSPECTED` with `mismatchTags`: `DOC_INFO_MISMATCH`

:::

The request must contain JSON with optional and mandatory parameters:

|Key|Required|Explanation|Type|Constraints|Default value|
|---|---|---|---|---|---|
|`clientId`|Yes|A unique string identifying a client.|String|<br/>- Not null<br/>- Max length 100|-|
|`firstName`|No|A name(s) of a client to be identified.|String|<br/>- Min length 1<br/>- Max length 100 <br/>- Not digits <br/>- Not characters: ```~!@#$%^*()_+={}[]\|:;",<>/?```|-|
|`lastName`|No|A surname(s) of a client to be identified.|String|<br/>- Min length 1<br/>- Max length 100 <br/>- Not digits <br/>- Not characters: ```~!@#$%^*()_+={}[]\|:;",<>/?```|-|
|`successUrl`|No|A url where a client will be redirected after a successful verification.|String|<br/>- Min length 5<br/> - Max length 2048<br/> - Cannot be used with [iframe implementation](/pages/verification-service-api/ClientRedirectToWebUiIframe)|`https://ui.idenfy.com/result?status=success`|
|`errorUrl`|No|A url where a client will be redirected after a failed verification.|String|<br/>- Min length 5<br/> - Max length 2048<br/> - Cannot be used with [iframe implementation](/pages/verification-service-api/ClientRedirectToWebUiIframe)|`https://ui.idenfy.com/result?status=fail`|
|`unverifiedUrl`|No|A url where a client will be redirected after a not analyzed verification. E.g. user immediately cancels process.|String|<br/>- Min length 5<br/> - Max length 2048<br/> - Cannot be used with [iframe implementation](/pages/verification-service-api/ClientRedirectToWebUiIframe)|`https://ui.idenfy.com/result?status=unverified`|
|`locale`|No|A country code in alpha-2 format. Determines what default language a client will see in verification UI.|String|Values:<br/>-`lt`(Lithuanian)<br/>-`en`(English)<br/>-`ru`(Russian)<br/>-`pl`(Polish)<br/>-`lv`(Latvian)<br/>-`ro`(Romanian)<br/>-`it`(Italian)<br/>-`de`(German)<br/>-`fr`(French)<br/>-`sv`(Swedish)<br/>-`es`(Spanish)<br/>-`hu`(Hungarian)<br/>-`ja`(Japanese)<br/>-`bg`(Bulgarian)<br/>-`et`(Estonian)<br/>-`cs`(Czech)|`en`|
|`showInstructions`|No|Indicates whether instructions should be shown.|Bool|-|`true`|
|`expiryTime`|No|Length of time in seconds after which a newly generated token will become invalid.|Integer|<br/>- More than 0<br/>- Not exceeding 2592000 (30 days)|`86400`|
|`sessionLength`|No|Length of time in seconds where a client is given to identify himself in indentification UI.|Integer|<br/>- More than 60<br/>- Less than 3600|`1800`|
|`country`|No|A default document country in alpha-2 code for a client. A client will not be able to select a different country.|String|<br/>- Any country in alpha-2 code|`null`|
|`documents`|No|Supported verification documents for the client.|List\[String\]| Possible values:<br/>`ID_CARD`<br/>`PASSPORT`<br/>`RESIDENCE_PERMIT`<br/>`DRIVER_LICENSE`<br/>`AADHAAR`<br/>`PAN_CARD`<br/>`VISA`<br/>`BORDER_CROSSING`<br/>`ASYLUM`<br/>`NATIONAL_PASSPORT`<br/>`INTERNATIONAL_PASSPORT`<br/>`VOTER_CARD`<br/>`OLD_ID_CARD`<br/>`TRAVEL_CARD`<br/>`PHOTO_CARD`<br/>`MILITARY_CARD`<br/>`PROOF_OF_AGE_CARD`<br/>`DIPLOMATIC_ID`|Values:<br/>`ID_CARD`<br/>`PASSPORT`|
|`dateOfBirth`|No|Date of birth of a client.|String|- Format: YYYY-MM-DD|`null`|
|`dateOfExpiry`|No|Date of expiry of a client document.|String|- Format: YYYY-MM-DD|`null`|
|`dateOfIssue`|No|Date of issue of a client document.|String|- Format: YYYY-MM-DD|`null`|
|`nationality`|No|Nationality of a client.|String|- Any country in alpha-2 code|`null`|
|`personalNumber`|No|Personal/national number of a client.|String|- Min length 1|`null`|
|`documentNumber`|No|Number of a client document.|String|- Min length 1|`null`|
|`sex`|No|Gender of a client.|String|- Values:-`M`-`F`|`null`|
|`generateDigitString`|No|Specify whether to generate an 8-digit string identifying the token that can be used in our mobile application.|Boolean|<br/>-If `true`, contract must allow to generate digit string  <br/>-If `true`, `expiryTime` must not exceed maximum expiry time of digit string|`false`|
|`address`|No|Client address provided by partner.|String|- Max length 255|`null`|
|`tokenType`|No|Determines, what sort of processing the client should go through.|String|Values:<br/>-`DOCUMENT`<br/>-`IDENTIFICATION`<br/>-`VIDEO_CALL`<br/> -`VIDEO_CALL_PHOTOS`<br/>-`VIDEO_CALL_IDENTIFICATION`|`IDENTIFICATION`|
|`videoCallQuestions`|No|Questions the partner should ask the client in a video call.|List[String]|-|[]|
|`externalRef`|No|An unique string for external reference to link the client to you and the iDenfy system better.|String|- Length <= 40|`null`|
|`utilityBill`|No|Require your users to upload a utility bill as an additional step in the verification.|Bool|Can't be used together with `additionalSteps`|`false`|
|`additionalSteps`|No|Require your users to upload additional photos in their verification process. Please refer to [**this**](/pages/verification-service-api/AdditionalSteps) manual page for detailed information about additional steps.|JSON object|Valid JSON. Can't be used together with `utilityBill`|`null`|
|`additionalData`|No|Additional data provided alongside any [additionalSteps](/pages/verification-service-api/AdditionalSteps), for example - Social Security Number in `UTILITY_BILL`|JSON object|Must be used with [additionalSteps](/pages/verification-service-api/AdditionalSteps)|`null`|
|`callbackUrl`|No|A webhook endpoint for the generated session.|String|Must be HTTPS URL, requires permissions enabled(contact tech support via dashboard if the request is refused).|-|

:::note
If you're generating a 8 digit mobile code, please keep in mind that for security purposes, `expiryTime` cannot be longer than `generateDigitString` default value(must be the value as default `expiryTime`).
If you need to increase the maximum default value of the mobile code expiration, please contact sales@idenfy.com or our technical support team via Dashboard. 
:::
### Receiving response
The response JSON contains exact same fields as JSON during token generation. It also returns default values for fields
that were optional and not specified during token generation. Additionally, the response also provides these fields below:

|Key|Explanation|Constraints|Example value|
|---|---|---|---|
|`message`|A message for a developer about the status of generated token.|- Max length 100|`"Token created successfully"`
|`authToken`|A unique string for verification process (will be passed as a url parameter when redirecting a client to verification platform).|- Length <= 40|`"3FA5TFPA2ZE3LMPGGS1EGOJNJE"`
|`scanRef`|A unique string identifying a client verification on iDenfy’s side.|- Length <= 40|`"d2714c8a-ec05-11e8-834f-067891e3383a"`
|`clientId`|A unique string identifying a client on your company's side. (The same value when requesting to generate a token)|<br/>- Not null<br/>- Max length 100|`"5F7E4FR14"`
|`digitString`|A unique string identifying the token that can be used by the client in our mobile application. Will be null if `generateDigitString` is not `true`|-Length = 8|`"89567412"`

### Examples
#### Example requests

You can choose not to send any data regarding your client that needs to be identified. The only mandatory parameter is **clientId**.
```json
{  
   "clientId":"100000"
}
```
As our only mandatory parameter is **clientId**, however we recommend that you would append at least client’s name and surname if possible. It increases the success rate of verifications.
```json
{  
   "clientId":"100000",
   "firstName":"John Tom",
   "lastName":"Smith"
}
```
Specify all of the parameters for full control.
```json
{  
   "clientId":"100000",
   "firstName":"John Tom",
   "lastName":"Smith",
   "successUrl":"https://www.my-company.com/idenfy/success",
   "errorUrl":"https://www.my-company.com/idenfy/fail",
   "unverifiedUrl":"https://www.my-company.com/idenfy/unverified",
   "callbackUrl" : "https://mywebsite.com/idenfy/webhookendpoint",
   "locale":"en",
   "showInstructions":true,
   "expiryTime":600,
   "sessionLength":600,
   "country":"lt",
   "documents":["PASSPORT"],
   "dateOfBirth": "1990-12-20",
   "dateOfExpiry": "1990-12-20",
   "dateOfIssue": "1990-12-20",
   "nationality": "lt",
   "personalNumber": "123456789",
   "documentNumber": "123456",
   "sex": "M",
   "address": "Address",
   "tokenType": "IDENTIFICATION",
   "videoCallQuestions": ["Question 1", "Question 2"],
   "externalRef": "reference",
   "utilityBill": false,
   "additionalSteps": null,
   "additionalData": null
   
}
```

#### Example responses
If supplied data in JSON and ***API key*** with ***API secret*** are valid, you should receive a successful response providing you ***scanRef*** which is the main identifier of a verification in *iDenfy* platform.

##### An example response with all fields provided:
```json
{
   "message": "Token created successfully",
   "authToken": "pgYQX0z2T8mtcpNj9I20uWVCLKNuG0vgr12f0wAC",
   "scanRef": "ec6a7108-8c26-11e9-9758-309c231b1bac",
   "clientId": "100000",
   "firstName": "JOHN TOM",
   "lastName": "SMITH",
   "successUrl": "https://www.my-company.com/idenfy/success",
   "errorUrl": "https://www.my-company.com/idenfy/fail",
   "unverifiedUrl":"https://www.my-company.com/idenfy/unverified",
   "callbackUrl" : "https://mywebsite.com/idenfy/webhookendpoint",
   "locale": "en",
   "showInstructions":true,
   "country": "lt",
   "expiryTime": 600,
   "sessionLength": 600,
   "documents": [
     "PASSPORT"
   ],
   "allowedDocuments": {
        "ALL": [
            "ID_CARD",
            "PASSPORT",
            "DRIVER_LICENSE",
            "RESIDENCE_PERMIT"
        ]
    },
   "dateOfBirth": "1990-12-20",
   "dateOfExpiry": "1990-12-20",
   "dateOfIssue": "1990-12-20",
   "nationality": "lt",
   "personalNumber": "123456789",
   "documentNumber": "123456",
   "sex": "M",   
   "digitString": "4823657",
   "address": "Address",
   "tokenType": "IDENTIFICATION",
   "videoCallQuestions": ["Question 1", "Question 2"],
   "externalRef": "reference",
   "utilityBill": false,
   "additionalSteps": null,
   "additionalData": null,
   "callbackUrl" : "https://mywebsite.com/idenfy/webhookendpoint"
}
```

##### An example response with no fields provided:
```json
{
  "message": "Token created successfully",
  "authToken": "LEWCd6xHTW2auHrst9MEqZN8tUgKsUQ8hSMNYoLI",
  "scanRef": "9b11acb2-8c27-11e9-9758-309c231b1bac",
  "clientId": "100000",
  "personScanRef": "100000",
  "firstName": null,
  "lastName": null,
  "successUrl": null,
  "errorUrl": null,
  "locale": "en",
  "showInstructions":true,
  "country": null,
  "expiryTime": 3600,
  "sessionLength": 300,
  "documents": [
    "ID_CARD",
    "PASSPORT",
    "RESIDENCE_PERMIT",
    "DRIVER_LICENSE"
  ],
  "allowedDocuments": {
        "ALL": [
            "ID_CARD",
            "PASSPORT",
            "RESIDENCE_PERMIT",
            "DRIVER_LICENSE"
        ]
    },
  "dateOfBirth": null,
  "dateOfExpiry": null,
  "dateOfIssue": null,
  "nationality": null,
  "personalNumber": null,
  "documentNumber": null,
  "sex": null,
  "digitString": "4823657",
  "address": null,
  "tokenType": "IDENTIFICATION",
  "videoCallQuestions": [],
  "externalRef": null,
  "utilityBill": false,
  "additionalSteps": null,
  "additionalData": null

}
```

### Sending a request with custom settings

You can generate a token with customizable settings, such as liveness face detection, instructions, attempt count, etc. simply by adding the available parameters to your token generation request body.


The default values of the settings depend on the environment's configuration. You can freely contact our support team using your Dashboard account to inquire about current and available settings, some of them have to be included in your contract before it can be enabled.

Our sales team at sales@idenfy.com will be happy to answer contract related questions and possibilities of including additional services to your environments.

Please find the full available list of settings below:

|Setting|Type|Explanation|
|---|---|---|
|`checkLiveness`|Bool|Indicates whether to add liveness face detection to the generated token or not.|
|`reviewSuccessful`|Bool|Indicates if iDenfy's manual review team should review successful verifications after the automatic processing.|
|`reviewFailed`|Bool|Indicates if iDenfy's manual review team should review failed verifications after the automatic processing.|
|`driverLicenseBack`|Bool|An option to allow uploading back side of the driver's license in the verification session.|
|`autoAmlMonitoring`|Bool|An option to automatically add the verified person to AML monitoring.|
|`fraudServices`|List|Specify a fraud service to check for the verification. <br/> Possible values:<br/>`AML_NAMES_CHECK`<br/> `LID`<br/> `AML_NEWS`
|`checkIpProxy`|Bool|An option to check if an IP proxy was used for verification.|
|`maxAttemptCount`|Integer|Indicates how many times the user can retry the verification after the automatic processing fails. For example, with this number set as 0, the client must succeed on the first try, with this number set as 3, the client will be able to retry 3 more times after failing on the first attempt.|
|`checkFaceBlacklist`|Bool|Check if this verification's selfie is added to your environment's face blacklist. If there will be any matches, the verification will be suspected|
|`checkDocFaceBlacklist`|Bool|Check if this verification's document photo is added to your environment's face blacklist.|
|`checkDuplicateFaces`|Bool|Check if there is already a verification of the same person's face in your environment.|
|`checkDuplicateDocFaces`|Bool|Check if there is already a verification of the same person's document face in your environment.|
|`nfcRequired`|Bool|Option to require NFC as a method for verification.|
|`faceMatchingThresholdModifier`|Float/Integer|This setting adjusts the sensitivity for the face matching algorithm. Please keep in mind that the default value is set at 1, which is tested and most optimal.|
|`levenshteinTextMatchRatio`|Float/Integer|This setting adjusts the Levenshtein matching ratio that is used for string comparison and searches for inconsitencies between them. The technology behind this method can be found on the web, for example - [here](https://en.wikipedia.org/wiki/Levenshtein_distance). The default setting is already in tested and optimal value - 0.8|
|`levenshteinMrzMatchRatio`|Float/Integer|This setting adjusts the Levenshtein matching ratio for document's MRZ. The technology principle is the same as `levenshteinTextMatchRatio` but used separately for MRZ. The default setting is already in tested and optimal value - 0.7|
|`checkUrjanet`|Bool|This option is turned on for utility address check.|
|`detectionTypes`|List[String]|Specifies the priority for detection in selfie processing for mobile users.<br/> Possible values: <br/>`FACE`<br/>`EYES`

:::important
None of the above parameters are mandatory, and they have to be turned on in your environment's configuration before you can pass a specific option parameter in your token generation request.

If the option is turned on in the configuration, they will be active for all of the generated tokens, unless you specify othwerise with the above parameters.

As a result, these options will give you more freedom in customizing our services and applying them in more different scenarios.

Please keep in mind that some of these parameters are already in optimal and tested settings. Use at your own risk.
:::



:::note

In case of a malformed JSON body or API key/secret mismatch you will receive a standard iDenfy API error response. 

For more on iDenfy API responses visit iDenfy error messages [iDenfy error messages](/pages/fraud-prevention-services/StandardErrorMessages).

:::
