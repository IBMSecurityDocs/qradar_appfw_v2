# Supporting Multi-Tenancy

You can designate that an app supports running in a multi-tenanted environment, with a
designated app instance per tenant.

You can designate an app as "multitenancy safe," meaning the app has been tested and is ready to be
used in a multi-tenanted environment.

This app simply reports the instance ID of the app instance that is serving the app content, allowing you to verify
that the app is serving from a different app instance for each tenant.

## Prerequisites

This tutorial requires the following dependencies:

- QRadar app SDK version 2
- An IBM QRadar configuration set up with multiple tenants

### Create the app

Create a new folder for the app:

```bash
mkdir MultitenancyMultipleInstance && cd MultitenancyMultipleInstance
```

Use the QRadar App SDK to initialize the app code:

```bash
qapp create
```

### Write the manifest

Edit the `manifest.json` file to to make it more relevant to the app:

```json
{
  "name": "Multitenancy Multiple Instance",
  "description": "App showing multitenancy capabilities, with each tenant having a dedicated app instance",
  "version": "1.0",
  "image": "qradar-app-base:2.0.0",
  "areas": [
    {
      "id": "QMultitenancyMultipleInstance",
      "text": "Multitenancy Multiple Instance",
      "description": "Area reporting the app instance ID",
      "url": "index",
      "required_capabilities": []
    }
  ],
  "uuid": "<your unique app UUID>",
  "multitenancy_safe": "true"
}
```

This manifest defines an area called `QMultitenancyMultipleInstance` that will serve the app user interface.

The `"multitenancy_safe": "true"` flag in the `manifest.json` file designates this app as tested and ready for use in
a multi-tenanted environment. This allows a QRadar administrator to provision a dedicated app instance per tenant.

### Write the Jinja template

Delete `app/templates/hello.html` and create a new file called `app/templates/index.html` using the following code:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Multitenancy Sample</title>
  </head>
  <body>
    <h1 id="title">Multitenancy app</h1>
    <label id="label">This instance's ID is:</label>
    <span id="app_id">{{ app_id }}</span>
  </body>
</html>
```

This sets up the Jinja template, which will display the app instance ID that allows verification that each tenant
has their own app instance.

### Create the app endpoint

Add the app HTTP endpoints by editing `app/views.py`:

```python
from flask import Blueprint, render_template
from qpylib import qpylib

# pylint: disable=invalid-name
viewsbp = Blueprint('viewsbp', __name__, url_prefix='/')


# Simple endpoint that grabs the instance's app ID and surfaces it by injecting
# it into HTML and returning it
@viewsbp.route('/index')
def index():
    app_id = qpylib.get_app_id()
    return render_template('index.html', app_id=app_id)
```

This sets up the app endpoint to retrieve the app instance ID and then injects the app instance ID into the Jinja
template. The endpoint name must match the name of the endpoint listed in the manifest.

### Package and deploy the app

Use the following commands to package and deploy the app:

```bash
qapp package -p <app zip name>

qapp deploy -p <app zip name> -q <qradar console> -q <qradar user>
```

### Creating an app instance for each tenant

Once the app is deployed onto a QRadar system, you can provision an app instance for specific tenants, either
using the QRadar Assistant or through the QRadar API.

To provision an app instance through the QRadar API, get the ID of the security profile associated with the
tenant and the deployed app's definition ID. With these two values, you can use an API call to create a new instance of
the app for a tenant:

```bash
curl
    -X POST                            \
    -u <qradar user>:<qradar password> \
    -H 'Version: 16.0'                 \
    -H 'Accept: application/json'      \
    'https://<qradar console>/api/gui_app_framework/applications?application_definition_id=<app definition id>&security_profile_id=<security profile id>'
```
