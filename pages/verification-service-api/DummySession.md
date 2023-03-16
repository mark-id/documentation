---
layout: default
title: Dummy session for developers
parent: Verification service API
has_toc: true
nav_order: 5
---

# Dummy session for developers

You can generate a token with dummy auto status and add dummy manual status for verification in the development environment.

{: .note }
Dummy session for developers is used to test the API with various statuses and receive a webhook without the need to go through the process in the UI.
If you want to test the flow in the UI, please use the redirection methods - [Redirection to WEB UI](/pages/verification-service-api/ClientRedirectToWebUi) or [Redirection using iframe](/pages/verification-service-api/ClientRedirectToWebUiIframe) without the `dummyStatus` parameter.

## Dummy auto result

You can generate a token with dummy auto status and receive a webhook with auto results.

### Sending request
Send a *HTTP POST* request to: `https://ivs.markid.eu/api/v2/token`

The request must contain *basic auth* headers where *username* is *API key* and *password* is *API secret*.

{: .note }
You should have a development environment, otherwise neither a simple token, nor token with dummy auto status will be created.

The request must contain the same parameters as [token generation](/pages/verification-service-api/GeneratingIdentificationToken##sending-request) and *dummyStatus*, which defines dummy session's auto result.

|JSON key        |Type    |Constraints      |Explanation|
|----------------|--------|-----------------|-----------|
|`dummyStatus`   |`String`|- Max length 30  |Auto status of the verification. <br/>Possible values:<br/>- APPROVED <br/>- DENIED<br/>- SUSPECTED<br/>- ACTIVE<br/>- EXPIRED<br/>[For value explanations refer to status vocabulary](/pages/fraud-prevention-services/Vocabulary#verification-status-values-vocabulary).      

#### Example request 

```json
{
    "dummyStatus":"APPROVED",
    "clientId":"100000",
    "firstName":"John Tom",
    "lastName":"Smith",
    "successUrl":"https://www.my-company.com/markid/success",
    "errorUrl":"https://www.my-company.com/markid/fail",
    "locale":"en",
    "showInstructions":true,
    "expiryTime":600,
    "sessionLength":600,
    "country":"lt",
    "documents":["PASSPORT", "OTHER"],
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
    "externalRef": "reference"
}
```

#### Example responses
Successful API call returns a JSON response with *scanRef*, that can be used to add dummy manual status.

##### An example response with all fields provided:
```json
{
   "message": "Dummy token and verification created successfully",
   "authToken": "pgYQX0z2T8mtcpNj9I20uWVCLKNuG0vgr12f0wAC",
   "scanRef": "ec6a7108-8c26-11e9-9758-309c231b1bac",
   "clientId": "100000",
   "firstName": "JOHN TOM",
   "lastName": "SMITH",
   "successUrl": "https://www.my-company.com/markid/success",
   "errorUrl": "https://www.my-company.com/markid/fail",
   "locale": "en",
   "showInstructions":true,
   "country": "lt",
   "expiryTime": 600,
   "sessionLength": 600,
   "documents": ["PASSPORT",  "OTHER"],
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
   "externalRef": "reference"
}
```

## Dummy manual approve

You can add a dummy manual status for the verification in development environment and receive the same webhook as it would after the manual review.

### Sending request
Send a *HTTP POST* request to: `https://ivs.markid.eu/api/v2/add-dummy-status` 

The request must contain *basic auth* headers where *username* is *API key* and *password* is *API secret*.

{: .note }
Client's verification should be already created, otherwise, generate a new token with *dummyStatus*

The request must contain JSON with these parameters:

|Key                      |Type    |Constraints |Explanation|
| ------------------------| ------ | ---------- | --------- | 
|`scanRef`                |`List`  |-           |A unique string identifying a client verification.|
|`manualFaceMatchResult`  |`String`|- Max length 30 |Dummy manual face status. <br/>Possible values:<br/>- FACE_MATCH<br/>- FACE_MISMATCH<br/>- NO_FACE_FOUND<br/>- TOO_MANY_FACES<br/>- FACE_TOO_BLURRY<br/>- FACE_UNCERTAIN<br/>- FACE_NOT_ANALYSED<br/>- FACE_ERROR<br/>- AUTO_UNVERIFIABLE<br/>- FAKE_FACE<br/>[For value explanations refer to status vocabulary](/pages/fraud-prevention-services/Vocabulary#verification-status-values-vocabulary).|
|`manualDocumentValidity` |`String`|- Max length 30  |Dummy manual document status. <br/>Possible values:<br/>- DOC_VALIDATED<br/>- DOC_INFO_MISMATCH<br/>- DOC_NOT_FOUND<br/>- DOC_NOT_FULLY_VISIBLE<br/>- DOC_NOT_SUPPORTED<br/>- DOC_FACE_NOT_FOUND<br/>- DOC_TOO_BLURRY<br/>- DOC_FACE_GLARED<br/>- MRZ_NOT_FOUND<br/>- MRZ_OCR_READING_ERROR<br/>- BARCODE_NOT_FOUND<br/>- DOC_EXPIRED<br/>- COUNTRY_MISMATCH<br/>- DOC_TYPE_MISMATCH<br/>- DOC_DAMAGED<br/>- DOC_FAKE<br/>- DOC_ERROR<br/>- AUTO_UNVERIFIABLE<br/>- DOC_NOT_ANALYSED<br/>- DOC_NAME_ERROR<br/>- DOC_SURNAME_ERROR<br/>- DOC_EXPIRY_ERROR<br/>- DOC_DOB_ERROR<br/>- DOC_PERSONAL_NUMBER_ERROR<br/>- DOC_NUMBER_ERROR<br/>- DOC_DATE_OF_ISSUE_ERROR<br/>- DOC_SEX_ERROR<br/>- DOC_NATIONALITY_ERROR<br/>[For value explanations refer to status vocabulary](/pages/fraud-prevention-services/Vocabulary#verification-status-values-vocabulary)|

For approved verification you need to give *FACE_MATCH* and *DOC_VALIDATED* statuses.

#### Example request 

```json
{
    "scanRef": "unique_scan_ref",
    "manualDocumentValidity": "DOC_ERROR",
    "manualFaceMatchResult": "FACE_MATCH"
}
```

#### Example responses
A successful API call returns a HTTP response with 200 status.
