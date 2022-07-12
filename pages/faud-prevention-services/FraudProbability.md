---
layout: default
title: Fraud Probability
parent: Fraud prevention services
has_toc: true
nav_order: 2
---

# Fraud probability estimation

This service endpoint estimates fraud probability by analyzing various information about user

### Sending request

Send a *HTTP Post* request to: `https://ivs.markid.eu/fraud/estimate-fraud-probability`

The request must contain *basic auth* headers where *username* is *api key* and *password* is *api secret*.

:::note
***API key*** and ***API secret*** can be retrieved by contacting *Mark ID support* or *Mark ID sales team*:
- sales@markid.eu
- support@markid.eu
- info@markid.eu
:::

<br/>

The request must contain JSON with parameters:

|Key|Required|Type|Constraints|Explanation|
|---|---|---|---|---|
|`user_agent`|No|`String`|- Max length 512|The HTTP `User-Agent` header of the browser|
|`accept_language`|No|`String`|-|The HTTP `Accept-Language` header of the device|
|`ip_address`|No|`String`|- `ipv4` or `ipv6` format|User's IP address|
|`first_name`|No|`String`|- Max length 255|User's first name|
|`last_name`|No|`String`|- Max length 255|User's last name|
|`email_address`|No|`String`|- Max length 255|User's email address|
|`email_domain`|No|`String`|- Max length 255|The domain of the `email_address`|
|`street_address`|No|`String`|- Max length 255|The first line of the user's living address|
|`city`|No|`String`|- Max length 255|User's city|
|`country`|No|`String`|- Country alpha-2 code|User's country|
|`postal_code`|No|`String`|- Max length 255|The postal/zip code of user's living address|
|`phone_number`|No|`String`|- Max length 255|User's phone number without country code (e.g. without +370, only 640...)|
|`phone_country_code`|No|`String`|- Max length 4|User's phone country code (e.g. +370)|
|`credit_card_number_6`|No|`String`|- Length 6|The first 6 digits of the user's payment card|
|`credit_card_last_4_digits`|No|`String`|- Length 4|The last 4 digits of the user's payment card|

:::important
Despite that none of these parameters are required, but the request must contain at least one of them
:::

### Receiving response

|Key|Type|Constraints|
|---|---|---|
|`fraud_level`|`String`|- Values:<br/>-`VERY_LOW`<br/>-`LOW`<br/>-`MEDIUM`<br/>-`HIGH`<br/>-`VERY_HIGH`<br/>-`NOT_CHECKED`|


### Examples

#### Example request

```json
{
    "user_agent": "Mozilla/5.0 (Android 7.0; Mobile; rv:54.0) Gecko/54.0 Firefox/54.0",
    "accept_language": "en-US,en;q=0.8",
    "ip_address": "64.236.213.12",
    "first_name": "John",
    "last_name": "Smith",
    "email_address": "john@gmail.com",
    "email_domain": "gmail.com",
    "street_address": "123 Address Rd.",
    "city": "Boston",
    "country": "US",
    "postal_code": "34455",
    "phone_number": "3439007998",
    "phone_country_code": "1",
    "credit_card_number_6": "410608",
    "credit_card_last_4_digits": "9930"
}
```

#### Example response

```json
{
    "fraud_level": "LOW"
}
```
