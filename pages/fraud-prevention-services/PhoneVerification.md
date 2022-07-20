---
layout: default
title: Phone Number Verification
parent: Fraud prevention services
has_toc: true
nav_order: 4
---

# Phone number verification

Phone number verification is a two-step process consisting of sending verification code via SMS message to the user and verifying that the code which user entered is valid

## 1. Sending SMS message with verification code

Call endpoint which will send an SMS message containing verification code to the user

### Sending request

Send a *HTTP Post* request to: `https://ivs.markid.eu/fraud/send-sms`

The request must contain *basic auth* headers where *username* is *api key* and *password* is *api secret*.

:::note
***API key*** and ***API secret*** can be retrieved by contacting *Mark ID support* or *Mark ID sales team*:
- sales@markid.lt
- support@markid.lt
- info@markid.lt
:::

<br/>

The request must contain JSON with parameters:

|Key           |Required|Type    |Constraints    |Default |Explanation|
|--------------|--------|--------|---------------|--------|-----------|
|`phone_number`|Yes     |`String`|[E.164](https://en.wikipedia.org/wiki/E.164) format|-|Phone number with country code, to which will be sent the verification code via SMS message|
|`sender_name` |No      |`String`|- Max length 11|`Mark ID`|Name or number which will be displayed as sender number/name|


### Receiving response

|Key         |Type    |Explanation                                 |
|------------|--------|--------------------------------------------|
|`request_id`|`String`|You will need this for the verification step|


### Examples

#### Example request

```json
{
    "phone_number": "+12025550163",
    "sender_name": "The Sender"
}
```

#### Example response

```json
{
    "request_id": "3d50e1584e0946e789e0185c009aaadf"
}
```

#### Example sent SMS message

```
The Sender code: 1234. Valid for 5 minutes. 
```


## 2. Verifying user-entered code

After the verification code was sent, the user can enter the code he got, and we can verify if it is valid

### Sending request

Send a *HTTP Post* request to: `https://ivs.markid.eu/fraud/verify-sms`

The request must contain *basic auth* headers where *username* is *api key* and *password* is *api secret*.

:::note
***API key*** and ***API secret*** can be retrieved by contacting *Mark ID support* or *Mark ID sales team*:
- sales@markid.lt
- support@markid.lt
- info@markid.lt
:::

<br/>

The request must contain JSON with parameters:

|Key            |Required|Type    |Constraints|Explanation|
|---------------|--------|--------|-----------|-----------|
|`request_id`   |Yes     |`String`|- Min Length 32<br/>- Max Length 40|`request_id` which was returned from SMS message sending endpoint|
|`received_code`|Yes     |`String`|- Length 4 |Numerical verification code which was entered by user|


### Receiving response

|Key          |Type  |Explanation|
|-------------|------|-----------|
|`is_verified`|`Bool`|If `true`, the `received_code` was valid, if `false`, the `received_code` was invalid, already validated, or the wrong `received_code` was provided too many times|


### Examples

#### Example request

```json
{
    "request_id": "3d50e1584e0946e789e0185c009aaadf",
    "received_code": "1234"
}
```

#### Example response

```json
{
    "is_verified": true
}
```
