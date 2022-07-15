---
layout: default
title: Client redirect to WEB UI iFrame
parent: Verification service API
has_toc: true
nav_order: 4
---

# Client redirect to WEB UI iFrame
### Redirect action

If you wish to have an iFrame implementation – there is a slightly different approach. You will have to directly insert verification platform URL (https://ui.markid.eu/  ) with **authToken** query string parameter into your iframe tag. The **authToken** can be retrieved after [generating a token](/pages/verification-service-api/GeneratingIdentificationToken).


The main difference between the regular redirect and iframe implementation is that in iframe, the user can remain in your webpage and complete the verification without opening any additional browser windows.

The company's logo does not show up inside the verification session's UI in iframe.

After the process is finished, you may close the iframe and display a desired page to your client.


:::important 
* Do not use `successUrl`, `errorUrl`, `unverifiedUrl` in [token generation](/pages/verification-service-api/GeneratingIdentificationToken)
for iframe implementation. Using the mentioned parameters may break the flow.

* Locale parameter in token generation will have no effect in iFrame.

* To force the iFrame to use a locale, append `"lang=<alpha-2-country-code>"` URL parameter.

Please find the available locales and examples below.
:::

#### Available locales

Values:<br/>-`lt`(Lithuanian)<br/>-`en`(English)<br/>-`ru`(Russian)<br/>-`pl`(Polish)<br/>-`lv`(Latvian)<br/>-`ro`(Romanian)<br/>-`it`(Italian)<br/>-`de`(German)<br/>-`fr`(French)<br/>-`sv`(Swedish)<br/>-`es`(Spanish)<br/>-`hu`(Hungarian)<br/>-`ja`(Japanese)<br/>-`bg`(Bulgarian)<br/>-`et`(Estonian)<br/>-`cs`(Czech)

### Examples


|Query string parameter name           |Example value               |
|--------------------------------------|----------------------------|
|`authToken`                           |`3FA5TFPA2ZE3LMPGGS1EGOJNJE`|


An example redirect url: <br/> https://ui.markid.eu/?authToken=3FA5TFPA2ZE3LMPGGS1EGOJNJE

An example redirect url with english locale: <br/> https://ui.markid.eu/?authToken=3FA5TFPA2ZE3LMPGGS1EGOJNJE&lang=en

#### Example code

```html
<!DOCTYPE html>
<html>
  <body>
  
  <iframe 
    id='iframe' 
    allowfullscreen
    style="width:80%; height:800px;" 
    src="https://ui.markid.eu/?authToken=3FA5TFPA2ZE3LMPGGS1EGOJNJE"
    allow="camera"
  ></iframe>
  
  <p id='display'></p>
  
  <script>
  window.addEventListener("message", receiveMessage, false);
    function receiveMessage(event) {
    console.log(event);
    // ...
  }
  </script>
  
  </body>
</html>
```
:::note
The data object can be found in the console.log message. The **allowfullscreen** attribute is mandatory if you're using our 3D liveness feature.
:::

### Posssible values:


Information about the Mark IDIdentificationResult **autoIdentificationStatus** statuses:

|Name            |Description
|-------------------|------------------------------------
|`APPROVED`   |The user completed a verification flow and the verification status, provided by an automated platform, is APPROVED.
|`FAILED`|The user completed a verification flow and the verification status, provided by an automated platform, is FAILED.
|`UNVERIFIED`   |The user did not complete a verification flow and the verification status, provided by an automated platform, is UNVERIFIED.

Information about the Mark IDIdentificationResult **manualIdentificationStatus** statuses:

|Name            |Description
|-------------------|------------------------------------
|`APPROVED`   |The user completed a verification flow and was verified manually while waiting for the manual verification results. The verification status, provided by a manual review, is APPROVED.
|`FAILED`|The user completed a verification flow and was verified manually while waiting for the manual verification results. The verification status, provided by a manual review, is FAILED.
|`WAITING`|The user completed the verification flow and started waiting for the manual verification results. The manual verification review is **still ongoing**.
|`INACTIVE`   |The user was only verified by an automated platform, not by a manual reviewer. The verification performed by the user can still be verified by the manual review if your system uses the manual verification service.         
