---
layout: default
title: Proxy Check
parent: Fraud prevention services
has_toc: true
nav_order: 1
---

# IP address proxy check

This service checks the IP address and returns its risk level

### Callback

This check can be enabled to be checked before sending a [result webhook](/pages/fraud-prevention-services/ResultCallback) and this check results would be in the `data` table's `clientIpProxyRiskLevel` field of the webhook.


### Sending request

Send a *HTTP Post* request to: `https://ivs.markid.eu/fraud/proxy-check`

The request must contain *basic auth* headers where *username* is *api key* and *password* is *api secret*.

:::note
***API key*** and ***API secret*** can be retrieved by contacting *Mark ID support* or *Mark ID sales team*:
- sales@markid.lt
- support@markid.lt
- info@markid.lt
:::

<br/>

The request must contain JSON with parameters:

|Key         |Required|Type    |Constraints            |Explanation                 |
|------------|--------|--------|-----------------------|----------------------------|
|`ip_address`|Yes     |`String`|`ipv4` or `ipv6` format|IP address you want to check|


### Receiving response

|Key|Type|Constraints|
|---|---|---|
|`risk_level`|`String`|- Values:<br/>-`VERY_LOW`<br/>-`LOW`<br/>-`MEDIUM`<br/>-`HIGH`<br/>-`VERY_HIGH`<br/>-`NOT_CHECKED`|


### Examples

#### Example request

```json
{
    "ip_address": "64.236.213.12"
}
```

#### Example response

```json
{
    "risk_level": "LOW"
}
```
