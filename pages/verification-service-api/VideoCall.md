---
layout: default
title: Video call
parent: Verification service API
has_toc: true
nav_order: 6
---

# Video call

You can generate video call token and record video call.

## Create video call

This endpoint lets you create a video call token for video or photo verifications.

### Sending request
Send a *HTTP POST* request to: `https://ivs.markid.com/api/v2/video-call`

The request must contain *basic auth* headers where *username* is *API key* and *password* is *API secret*.

:::note
Client should be already created, otherwise generate new token with token type *VIDEO_CALL* or *VIDEO_CALL_PHOTO* 
:::

The request must contain JSON with these parameters:

|     Key    | Required |              Explanation              |   Type   | 
| -----------| -------- | ------------------------------------- | -------- | 
| `scanRef`  | Yes      | A unique string identifying a client verification.            | `String` | 
| `photos`   | No       | Indicates whether photos of client and document should be taken.| `Bool` | 

### Examples
#### Example request for video call endpoint to make a video call photo token

```json
{
    "scanRef": "unique_scan_ref",
    "photos": true
}
```
#### Example request for video call token

```json
{
    "scanRef": "unique_scan_ref"
}
```

#### Example responses
Successful API call returns json response with url which redirects to video call.

## Video call record

This endpoint lets you retrieve video call photos and video.

### Sending request
Send a *HTTP Post* request to: `https://ivs.markid.com/api/v2/video-call-record`

The request must contain JSON with these parameters:

|     Key    | Required |              Explanation              |   Type   |                                     Constraints  |
| -----------| -------- | ------------------------------------- | -------- | ------------------------------------------------------------------------------------------------ |
| `scanRef`  | Yes      | A unique string identifying a client verification. | `String` | -                                                                                                |

### Examples
#### Example request 

```json
{
    "scanRef": "unique_scan_ref"
}
```

#### Example responses
Successful API call returns json response with photos and video urls.
```json
{
    "photos": {
        "VIDEO_CALL_BACK": "http://this-is-a-document-back-photo-url",
        "VIDEO_CALL_FACE": "http://this-is-a-face-photo-url",
        "VIDEO_CALL_FRONT": "http://this-is-a-document-front-photo-url"
    },
    "videos": [
         "http://this-is-a-identification-video-url"
    ]
}
```
