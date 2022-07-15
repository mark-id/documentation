---
layout: default
title: FAQ
parent: Fraud prevention services
has_toc: true
nav_exclude: true
---

# Frequently asked questions
This page covers most popular FAQ's which are categorised into sections.

## Manual verifications FAQ's

- ##### What is a manual verification?
A verification type where a verification is reviewed by our staff members.

- ##### How manual verification is different from automatic verification?
Manual verification is being performed by our staff members while automatic verification is
being performed by our automated algorithms. 

- ##### How does manual verification work?
In our office we have manual reviewers that look at automatically verified verifications 
one more time. They ensure that there were no fraudulent actions in verification 
process and algorithms accurately read data from a document.  

- ##### When does a manual verification "kick in"?
By default every single automatically verified verification is reviewed once more 
by our manual reviewers. This behaviour can be altered by your custom needs.

- ##### How do I know when verification was verified manually/automatically?
After a finished verification process in iDenfy platform you will be notified via email
and/or a HTTP call to you custom API endpoint. By default you can expect one notification 
(if a verification is reviewed only manually) or two notifications (first one is from
automatic and the second one is from manual verifications). Manual verification notification
is always the last notification. 

- ##### Is it possible that after a manual verification photos can get changed or modified by a manual reviewer?
No. The only thing that might change is the data read from the document due to possible OCR reading inaccuracies.

## Verification status FAQ's

- ##### What does "SUSPECTED" mean and when is it triggered?
This status means that our algorithms have detected a possible fraudulent activity in
verification process e.g. printed document or a face from mobile screen, etc. In such case
verification is set with overall status as "SUSPECTED". We leave the decision to our partners whether to onboard users with "SUSPECTED" status or not.

:::note
If you use our AML services, iDenfy's system automatically checks the name, surname, date of birth of the user after a completed, successful verification and compares the information against the AML database. If there is a match from the database, the user's verification is also set with an overall status as "SUSPECTED" with all the results displayed in the verification pop-up or webhook. 
:::

- ##### What does "AUTO_UNVERIFIABLE" mean and when is it triggered?
This status means that the verification can not be reviewed automatically. Some of most common scenarios when this 
status is triggered is when a user select "Other documents" to do a verification (may not be applicable to you) or explicitly asks for a manual review (may not be applicable to you).

- ##### What causes verification to be denied?
There are many reasons why a verification can be denied. There might be various cases
associated with document scanning e.g. could not locate a face in a document, could 
not read name/surname from the document, document is too blurry, etc. Also, there might
be various cases associated with face detection and matching e.g. selfie face and 
document face look too different. Fraudulent attempts can also be detected and denied.

- ##### When verification is considered approved?
It is considered approved when document and face analysis both succeed.
Document analysis: data from a document was read successfully and matched 
data provided when generating token. Face analysis: selfie face and document face
both match. Also, in case LID or AML is enabled a person has a valid document and is 
not in PEP sanctions list.


## Verification flow FAQ's

- ##### I see that a user has done a second verification even though the previous one was successful. Why is he allowed to do further verifications?
Because iDenfy platform does not track individual people that come to identify themselves.
iDenfy tracks only verifications and each verification has a unique number. 
Therefore you should ensure on your side that a client is not allowed to do 
an additional verification after a successful one. Also, iDenfy recommends 
restricting token generation for user if a previous one is not yet expired. 

- ##### When verification process is complete?
Verification process by default has 2 attempts. If You tried to identify yourself, and all 2 attempts in a row were unsuccessful, your verification process is complete (unsuccessfully). If You get successful verification per 2 attempts, your verification process is complete (successfully). If your session time exceeds, your verification process is complete (unsuccessfully). Sometimes users can interrupt the verification process by closing the browser window, in this case the verification process is still not complete. It completes when session time expires. So, our manual reviewer team can’t review verification immediately because session time hasn't expired and verification hasn't appeared in our system.

## Generating token FAQ's

- ##### What is the difference between expiryTime and sessionLength parameters? And what do they do?
The **expiryTime** parameter defines how long the generated token is going to live.
For example, if token was generated with expiry time of 3600 (1 hour) - 
a client can start his verification process any time withing that 1 hour.
After an hour is passed, a token will no longer be valid and and a client
will see an internal “session expired” page.
However **sessionLength** determines how long a client can stay in verification platform.
The **sessionLength** parameter takes effect only when a client visits 
the verification platform.
For example, if token was generated with session length of 600 (10 minutes)
a client has 10 minutes to identify himself (take photos of documents and his face)
in the platform. After 10 minutes a client will see an internal “session expired” page.

- ##### What if my client’s name/surname contains non-Latin characters?
We accept any type of characters (Latin and non-Latin).

- ##### Why am I receiving "Partner reached token limit." Error when generating a token?
A "400 Bad Request" with a message "Partner reached token limit." And identifier "PERMISSIONS_ERROR" when trying to generate a token means that your environment has run out of verification tokens. Please contact techsupport@idenfy.com to resolve the issue.

## Webhook FAQ's

- ##### I am not receiving a webhook. Why?
Please check the [Callback troubleshooting](/pages/fraud-prevention-services/CallbackSigning#callback-troubleshooting) section. 

- ##### How do I differentiate which (manual or automatic) verification a webhook represents?
Within received webhook look for 'status' entry.
If `autoDocument` with `autoFace` fields are not empty and 
`manualDocument` with `manualFace` field are empty - it means the webhook represents 
automatic verification. If `manualDocument` with `manualFace` fields are not empty - 
it means the webhook represents manual verification.

- ##### What happens if my provided webhook endpoint is not accessible temporary?
iDenfy API will repeat a call once. If both times there was an error a "failed webhook"
state is associated with the verification. If user has successfully identified himself
and webhook has failed, a user will see an information popup, where it states that 
verification succeeded but there was an issue saving data. Also, a user will be 
redirected to a failure page.

- ##### Do you repeat webhook sending?
Yes. iDenfy API will repeat a call once (after 0.5 seconds) if the initial webhook sending has failed. If the second webhook fails - an associated verification is flaged with "webhook failed" state.

- ##### How many webhooks to expect?
If your verifications are processed only automatically - just one - after processing is done. 
If your verifications are processed automatically and then reviewed manually - then in total two - one after an automatic processing and one after a manual verification.

## General FAQ's

- ##### Why your platform can not detect camera on Chrome with iFrame integration?
When using an `iframe` tag, make sure to add an attribute `allow="camera"`. 

- ##### What are the differences between "Base" and "AdvancedBase" manager roles?

**"Base"** role generally consist of the following allowed actions:
* View verifications
* List verifications
* Generate verification tokens
* View statistics
* Generate verification PDF files
* Generate lists of verifications

**"AdvancedBase"** role is allowed all of the actions of **"Base"** role but additionally acts as an administrator of the environment with the following permissions:

* Create additional manager accounts
* View finances and service usage
* Update environment's configuration
* List all manager accounts, delete, deactivate, reset passwords, view activity
* API keys management

:::note
There can be custom roles applied to a manager as well. If you need to restrict or provide more access to the accounts in your environment,
contact techsupport@idenfy.com with your request to change or add new roles.
:::