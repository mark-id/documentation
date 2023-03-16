---
layout: default
title: Direct API processing
parent: Verification service API
has_toc: true
nav_order: 8
---

# Direct API processing
This endpoint lets you upload customer photos by yourself and start processing immediately without using Mark ID WEB or mobile SDK UI. The verification token is deactivated after this request.


{: .important }
Using this implementation type you lose these features: 3D liveness detection and short video before taking photos. 
If 3D liveness detection and short video before taking photos is required for you, please use another integration type.


### Sending request
Send a *HTTP POST* request to: `https://ivs.markid.eu/api/v2/process`

The request must contain *basic auth* headers where *username* is *API key* and *password* is *API secret*.

{: .note }
> ***API key*** and ***API secret*** can be retrieved by contacting *Mark ID tech support* or *Mark ID sales team*:
> - sales@markid.lt
> - info@markid.lt
> - via Dashboard

**The maximum request size is 20 MB**

The request must contain JSON with these parameters:

|      Key       | Required |              Explanation              |   Type   |    Constraints   |
| -------------- | -------- | ------------------------------------- | -------- | ------------------------------------------------------------------------------------------------ |
| `authToken`    | Yes      | Verification token                  | `String` | -                                                                                                |
| `country`      | Yes      | Country code in 3166-1 alpha-2 format | `String` | Any country in alpha-2 code                                                                      |
| `documentType` | Yes      | Document type                         | `String` | Possible values:<br/>- ID_CARD<br/>- PASSPORT<br/>- RESIDENCE_PERMIT<br/>- DRIVER_LICENSE<br/>- OTHER |
| `images`       | Yes      | Images for verification (Base64 format)| `Object` | -            |

#### Contents of images

|     JSON key     |   Type   | Can be null | Constraints       |               Explanation               |
| ---------------- | -------- | ----------- |-------------------| --------------------------------------- |
| `FRONT`          | `String` | Yes         | - Max size of 6MB | Base64 encoded image of document front  |
| `BACK`           | `String` | Yes         | - Max size of 6MB | Base64 encoded image of document back   |
| `FACE`           | `String` | Yes         | - Max size of 6MB | Base64 encoded image of persons face    |
| `UTILITY_BILL`   | `String` | Yes         | - Max size of 6MB | Base64 encoded image of an utility bill |

> Images required for a specific document should be provided, usually at least `FRONT` and `FACE`.

### Examples
#### Example request

```json
{
    "authToken": "3FA5TFPA2ZE3LMPGGS1EGOJNJE",
    "country": "LT",
    "documentType": "ID_CARD",
    "images": {
        "FRONT":"/9j/4AAQSkZJRgABAQAAAQABAAD/4...",
        "BACK":"/9j/4AAQSkZJRgABAQAAAQABAAD/4...",
        "FACE":"/9j/4AAQSkZJRgABAQAAAQABAAD/4..."
    }
}
```

#### Example responses
For successful API calls, which start the processing, there will be no message, just a response with a positive **200** status.


##### An example response that failed
Failed API calls will return a message identifying the problem.

```json
{
    "message": "No image provided for step 'BACK'",
    "identifier": "MISSING_VALUE",
    "documentation": "https://github.com/markid/Documentation",
    "severity": "NOT_SEVERE"
}
```

{: .note }
Note that in case of a malformed JSON body or API key/secret mismatch you will receive a standard *Mark ID* API error response. For more on *Mark ID* API responses visit [Mark ID error messages](/pages/fraud-prevention-services/StandardErrorMessages).
