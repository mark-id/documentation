---
layout: default
title: Phone Number Validation
parent: Fraud prevention services
has_toc: true
nav_order: 3
---

# Phone number validation

This service endpoint calculates phone number's risk score and returns additional information about that phone number

### Sending request

Send a *HTTP Post* request to: `https://ivs.markid.eu/fraud/validate-phone`

The request must contain *basic auth* headers where *username* is *API key* and *password* is *API secret*.

{: .note }
> ***API key*** and ***API secret*** can be retrieved by contacting *Mark ID tech support* or *Mark ID sales team*:
> - sales@markid.lt
> - via Dashboard

<br/>

The request must contain JSON with parameters:

|Key               |Required|Type    |Constraints         |Explanation                |
|------------------|--------|--------|--------------------|---------------------------|
|`phone_number`    |Yes     |`String`|- [E.164](https://en.wikipedia.org/wiki/E.164) format|Phone number with country code to check|
|`current_location`|No      |`String`|- Country alpha-2 code|Current IP address location|


### Receiving response

|Key|Type|Constraints|Explanation|
|---|---|---|---|
|`risk_score`|`Float`|- Min 0<br/>- Max 99|Calculated score how risky is `phone_number`|
|`country_code`|`String`|- Country alpha-2 code|Two character country code for `phone_number`|
|`current_network`|`String`|-|The full name of the carrier that `phone_number` is associated with|
|`current_route`|`String`|- Values:<br/>-`mobile`<br/>-`landline`<br/>-`landline_premium`<br/>-`landline_tollfree`<br/>-`virtual`<br/>-`unknown`<br/>-`pager`|The type of network that `phone_number` is associated with|
|`original_network`|`String`|-|The full name of the carrier that `phone_number` is associated with|
|`availability`|`String`|- Values:<br/>-`unknown`<br/>-`reachable`<br/>-`undeliverable`<br/>-`absent`<br/>-`bad_number`<br/>-`blacklisted`|Can you call the `phone_number`. This is applicable to mobile numbers only|
|`validity`|`String`|- Values:<br/>-`unknown`<br/>-`valid`<br/>-`not_valid`<br/>-`inferred`<br/>-`inferred_not_valid`|`inferred_not_valid` means that the number could not be determined as valid or invalid via an external system and the best guess is that the number is invalid. This is applicable to mobile numbers only|
|`roaming`|`String`|- Values:<br/>-`unknown`<br/>-`roaming`<br/>-`not_roaming`|Is `phone_number` outside its home carrier network|
|`roaming_country`|`String`|- Country alpha-2 code|If `phone_number` is roaming, this is the code of the country `phone_number` is roaming in|
|`roaming_network_name`|`String`|-|If `phone_number` is roaming, this is the name of the carrier network `phone_number` is roaming in|
|`request_id`|`String`|- Max length 40|The unique identifier for your request|

### Examples

#### Example request

```json
{
    "phone_number": "+12025550163",
    "current_location": "US"
}
```

#### Example response

```json
{
    "risk_score": 10.0,
    "country_code": "US",
    "current_network": "United States Premium",
    "current_route": "unknown",
    "original_network": "United States Premium",
    "availability": "unknown",
    "validity": "not_valid",
    "roaming": "unknown",
    "roaming_country": null,
    "roaming_network_name": null,
    "request_id": "fcecf6e6-1ad7-4d5e-98a8-cc9b2d5575eb"
}
```

{: .note }
If the provided phone number in the request is not in [E.164](https://en.wikipedia.org/wiki/E.164) format or it isn't a *mobile* phone number, our servers may return an empty string.  `""`
