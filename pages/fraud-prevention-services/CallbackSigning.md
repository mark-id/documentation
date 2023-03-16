---
layout: default
title: Callback signing
parent: Fraud prevention services
has_toc: true
nav_exclude: true
---

# Callback signing

Callback signing is a simple method to secure your webhook endpoints from
foreign requests.

To check webhooks you need to compute SHA-256 HMAC of the whole HTTP body content 
with callback signature key, and compare that with `Mark ID-Signature` header.

We send the signature key along with the API keys upon environment creation, or you can contact our support team via Dashboard in order to receive it.

{: .important }
You should not use standard `==` operator to avoid timing attacks. You
should use `hmac.compare_digest`, `crypto.timingSafeEqual`  or equivalent constant time string comparison.

### Examples

#### Python example

```python
import hmac

CALLBACK_SIGNATURE_KEY = "Agegb..." 

@app.route('/Mark ID-callback')
def callback():
    signature = hmac.new(
        CALLBACK_SIGNATURE_KEY.encode(),
        request.get_data(),
        "sha256"
    ).hexdigest()
    
    request_signature = request.headers.get("Mark ID-Signature")
    
    if request_signature:
        if hmac.compare_digest(signature, request_signature):
            handle_request(request)
```

#### Javascript example

```javascript
const CALLBACK_SIGNATURE_KEY = "Agegb...";

function verifyPostData(req, res, next) {
    const payload = req.rawBody;
    
    if (!payload) {
      return next('Request body empty.')
    }

    const hmac = crypto.createHmac('sha256', CALLBACK_SIGNATURE_KEY)
    const digest = Buffer(hmac.update(payload).digest('hex'))
    const checksum = Buffer(req.headers['Mark ID-signature'])
    
    if (!checksum || !digest || !crypto.timingSafeEqual(checksum, digest)) {
        return next(`Request body digest (${digest}) did not match Mark ID-Signature (${checksum}).`)
    }
    
    return next()
}
```
