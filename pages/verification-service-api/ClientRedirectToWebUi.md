---
layout: default
title: Client redirect to WEB UI
parent: Verification service API
has_toc: true
nav_order: 2
---

import useBaseUrl from '@docusaurus/useBaseUrl';

#  Client redirect to WEB UI

After successfully [generating a token](/API/GeneratingIdentificationToken) you should have a valid **authToken** that looks similarly to this:

*"tSfnDiNBT16iP7ThpP6K8QfF2maTK0Vvkxfvq4YV"*.

### Redirect action


You may initiate a HTTP redirect action for your client to `https://ivs.markid.eu/api/v2/redirect` by appending a generated token as a URL query string parameter.

|Query string parameter name           |Example value               |
|--------------------------------------|----------------------------|
|`authToken`                           |`tSfnDiNBT16iP7ThpP6K8QfF2maTK0Vvkxfvq4YV`|


<img alt="Flow" width="1000" src='https://documentation.markid.eu/resources/redirectionFlow.png' />

### Examples

An example redirect URL looks like this: <br/>
`https://ivs.markid.eu/api/v2/redirect?authToken=3FA5TFPA2ZE3LMPGGS1EGOJNJE` <br/>

And after the redirection, the user will see our identity verification UI:

<img alt="preview" width="1000" src='https://documentation.markid.eu/resources/UI-preview.png' />


#### PHP example

```php
<?php
  header("Location: https://ivs.markid.eu/api/v2/redirect?authToken=tSfnDiNBT16iP7ThpP6K8QfF2maTK0Vvkxfvq4YV");
  exit;
?>
```

#### Python Flask example

```python
from flask import Flask, redirect

app = Flask(__name__)

@app.route('/')
def redirect_to_markid():
    return redirect("https://ivs.markid.eu/api/v2/redirect?authToken=tSfnDiNBT16iP7ThpP6K8QfF2maTK0Vvkxfvq4YV", code=302)
```
A full python flask example can be found [here](/tutorials/api-integration/PythonFlaskRedirectExample)
