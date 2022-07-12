---
layout: default
title: Verification flow customisation
parent: Verification service API
has_toc: true
nav_order: 9
---

import useBaseUrl from '@docusaurus/useBaseUrl';

### Flow customisation possibilities

Mark ID identity verification sessions can be configured with different flows that allows additional flexibility on the default flow that is displayed below:

<img alt="Full flow" width="700" src={useBaseUrl('/img/preview/defaultFlow.gif')} />


Changes may be applied to:

- Skipping certain steps - Country selection, document selection, instructions, uploaded photo confirmation.

- Forcing specific [language](/getting-started/FeaturesList#localisation) in the UI.

- Including or excluding [manual review](/extra/FAQ#manual-verifications-faqs).

- Redirection to a URL that was provided in the verification session's [request](/API/GeneratingIdentificationToken#sending-request).

- Adding an [additional step](/API/AdditionalSteps), such as utility bills, bank statements, or any picture/file of your description that won't be evaluated manually but could still be needed with each client's application.

- Additional flow customisation for [Android SDK](/mobile/Android/android-sdk#customizing-sdk-flow-optional) and [iOS SDK](/mobile/iOS/ios-sdk#customizing-sdk-flow-optional)


### Skipping certain steps

Country selection, document selection, and instructions screens are accompanied with the values that you provide inside the verification session's [request](/API/GeneratingIdentificationToken#sending-request). If the `country`, `documents`, `locale` parameters are included inside the request body, these values will be either preselected, or limited(e.g. `documents` that the user can choose from).
:::note
Document selection skipping will work only if available documents array size is 1, e.g, you allow only PASSPORT for this specific verification session.

To enable skipping of a certain screen or step, forward your request to techsupport@markid.eu
:::

### Forcing specific language

Using the same approach as described before, you can also apply it to default language with the `locale` parameter.
There is also a possibility to detect the IP address location of a client and automatically set the corresponding language. If the client is in a country with a language that we don't support yet, we will display the default language - English.


### Including or excluding manual review

With manual review enabled, it is the final step of the verification when the client's application is being reviewed by Mark ID staff.
Manual review can override Mark ID automatic check results, and correct OCR errors, if there are any.
In comparison with the automatic check, the manual review may make the process longer(an average verification is reviewed within 3-5 minutes), but it is highly recommended to keep the manual review for security purposes.

You can choose if you need to receive 1 or 2 [webhooks](/callbacks/ResultCallback). With only 1 webhook, you will receive only the final results of each verification. With 2 webhooks, you can receive preliminary(auto reviewed) results, and final(manually revied) results.
This will enable more flexibility on how you would like to handle the onboarding process and perhaps providing limited access to the user before the final results arrive.
The final results will include `"final":true` flag inside the webhook's body.

### Redirection to a URL

After the processing is finished, you can pass any of the below parameters with your URLs inside your session token request [request](/API/GeneratingIdentificationToken#sending-request) or add them inside your [environment's](https://admin.markid.eu) configuration so every generated session will already have these URL settings without specifying the parameters with API requests.

- `successUrl` A URL where the client will be redirected after a successful verification.

- `errorUrl`	A URL where the client will be redirected after a failed verification.

- `unverifiedUrl`	A URL where the client will be redirected after a non-analyzed verification. E.g. user immediately cancels the process.

Redirection functionality shouldn't be used with [iframe integration](/integration/ClientRedirectToWebUiIframe).
The timing of when the redirection should happen can also be changed by the two corresponding settings:

#### Immediate redirect

Immediate redirect can be turned on if you want your users to be redirected back to "Success" or "Failed" URL, depending on the automatic check results. Redirection is done immediately after they submit the verification and pass the automatic check. This way they won't be seeing the waiting window for human supervision confirmation.

#### Skip waiting for results
With this option turned on, your user will be immediately redirected to "unverifiedUrl" after they submit the verification. This way they won't be waiting for automatic check results. This option is used together with immediate redirect.

:::note
If you do not use our API, you can still [create the sessions](/tutorials/admin-platform/GenerateIdentificationToken) with the same parameters described above through [Mark ID administration platform](https://admin.markid.eu).
:::