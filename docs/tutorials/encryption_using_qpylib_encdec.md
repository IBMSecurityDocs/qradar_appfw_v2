# Encryption Using the QPyLib Encdec Module

You can use the QPyLib Encdec module to securely store your app's data.

Follow these steps to set up a simple QRadar app that will provide a list of the 10 most recent vulnerabilities
published on the IBM X-Force Exchange (XFE). To facilitate this, the app must support storing an XFE API key in
a secure way that it can retrieve when needed.

## Prerequisites

- QRadar App SDK v2

This tutorial also requires an IBM X-Force Exchange API key and password. If you do not have one, you can create an API key on X-Force Exchange:

1. Go to [IBM X-Force Exchange](https://exchange.xforce.ibmcloud.com/).
2. Log in, or make an account if you don't have one.
3. Go to [your settings page](https://exchange.xforce.ibmcloud.com/settings/api).
4. Generate a new API key and password and copy them.

**Important:** Don't lose this information. You cannot retrieve it later.

## Create the App

1. Create a new directory for the app:

```bash
mkdir VulnerabilitiesApp && cd VulnerabilitiesApp
```

2. Use the QRadar App SDK to initialise the app code:

```bash
qapp create
```

## Write the manifest

Open the `manifest.json` file to edit some values to make it more relevant to the app. The completed file should look like this:

```json
{
  "name": "Recent X-Force Vulnerabilities",
  "description": "App showing recent X-Force Exchange vulnerabilities",
  "version": "1.0.0",
  "image": "qradar-app-base:2.0.0",
  "areas": [
    {
      "id": "QRecentVulnerabilities",
      "text": "Recent Vulnerabilities",
      "description": "Area showing 10 most recent X-Force Exchange Vulnerabilities",
      "url": "index",
      "required_capabilities": []
    }
  ],
  "uuid": "<your unique app UUID>"
}
```

This manifest defines an area called `QRecentVulnerabilities` that will serve the app user interface.

## Write the App Jinja template

Delete `app/templates/hello.html` and create a new file `app/templates/index.html` using the following code:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Recent IBM X-Force Exchange Vulnerabilities</title>
    <script type="text/javascript" src='./static/qjslib/qappfw.min.js'></script>
  </head>
  <body>
    <h1>Recent IBM X-Force Exchange Vulnerabilities</h1>
    {% if xforce_auth_set %}
    <ul>
        {% for vulnerability in vulnerabilities %}
        <li>
            {{vulnerability.type}} - {{vulnerability.title}}
        </li>
        {% endfor %}
    </ul>
    {% else %}
    <p>Error decrypting API key: {{ decryption_error }}</p>
    <h2>No API key, submit one to fetch recent vulnerabilities</h2>
    <input id="api-key-input" type="text" placeholder="IBM X-Force Exchange API Key"/>
    <input id="api-pass-input" type="text" placeholder="IBM X-Force Exchange API Password"/>
    <input id="api-key-submit" type="submit">
    <script>
        // Using QJSLib library to make HTTP request
        var QRadar = window.qappfw.QRadar;

        document.getElementById("api-key-submit").addEventListener("click", function() {
            // Get input values
            var apiKey = document.getElementById("api-key-input").value;
            var apiPass = document.getElementById("api-pass-input").value;

            // POST xforce API details to set-api-key endpoint
            QRadar.fetch("set-api-key", {
                method: "post",
                headers: {
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                    key: apiKey,
                    pass: apiPass
                })
            })
                .then(function(response) {
                    if (response.ok) {
                        // Success setting api auth details, refresh
                        window.location.reload(true);
                        return;
                    }
                })
                .catch(function(error) {
                    // Error, display error to user
                    alert(error)
                });
        });
    </script>
    {% endif %}
  </body>
</html>
```

This Jinja template provides the entire user interface for the app, with two key pieces of functionality. The template
handles displaying a list of vulnerabilities, if they are provided, and the X-Force API authorization is set. If
there is no X-Force API authorization set, the app will instead provide a simple form to allow the user to submit an X-Force API key
and password.

The submission of the X-Force API auth uses the QJSLib library to make an HTTP POST request to the `set-api-key`
endpoint, populating the body with the API authorization details that the user provides.

## Create the app endpoints

Add the app HTTP endpoints by editing `app/views.py`:

```python
import json
import requests
from flask import Blueprint, render_template, request
from qpylib.encdec import Encryption, EncryptionError

# pylint: disable=invalid-name
viewsbp = Blueprint('viewsbp', __name__, url_prefix='/')

# Endpoint that decrypts the xforce api key and password, then uses it to get
# the 10 most recent X-Force Exchange vulnerabilities. If xforce details are
# retrieved successfully it will show the 10 vulnerabilities, if there is an
# error decrypting/reading the xforce secrets it will show an interface for the
# user to provide these details
@viewsbp.route('/')
@viewsbp.route('/index')
def index():
    try:
        # Decrypt the X-Force API Key
        encKey = Encryption({'name': 'xforce_key', 'user': 'shared'})
        key = encKey.decrypt()

        # Decrypt the X-Force Password
        encPass = Encryption({'name': 'xforce_pass', 'user': 'shared'})
        passwd = encPass.decrypt()

        # Get the 10 most recent X-Force Exchange vulnerabilities
        response = requests.get("https://api.xforce.ibmcloud.com/vulnerabilities/?limit=10", auth=(key, passwd))
        vulnerabilities = response.json()

        return render_template('index.html', xforce_auth_set=True, vulnerabilities=vulnerabilities)
    except EncryptionError as err:
        # An error occured, ask the user to input the API details again
        return render_template('index.html', xforce_auth_set=False, decryption_error=str(err))


# Endpoint that takes xforce api auth details, and encrypts and stores them
# using the QPyLib encdec module, if successful returns a 204 no content
# response
@viewsbp.route('/set-api-key', methods=['POST'])
def set_api_key():
    # Load provided API details from JSON
    xforce_data = json.loads(request.data)

    # Encrypt the API Key
    encKey = Encryption({'name': 'xforce_key', 'user': 'shared'})
    encKey.encrypt(xforce_data["key"])

    # Encrypt the API Password
    encPass = Encryption({'name': 'xforce_pass', 'user': 'shared'})
    encPass.encrypt(xforce_data["pass"])

    # Success, return a 204 no content
    return ('', 204)
```

This sets up the app's two HTTP endpoints, one to serve the templated user interface, injected with the 10 most recent
vulnerabilities.

## Test the app locally

The app is now ready to test locally using the QRadar App SDK.

**Important:** There is one extra step before you can run this app, you must add a line to the `qenv.ini` file to inject the required
`QRADAR_APP_UUID` environment variable into the app. This is required for the QPyLib Encdec module to work when
running a QRadar app locally. It is not required when the app is deployed on QRadar. The completed `qenv.ini` file should look
like this:

```ini
[qradar]
QRADAR_CONSOLE_FQDN=
QRADAR_CONSOLE_IP=

[proxy]
QRADAR_REST_PROXY=

[app]
SEC_ADMIN_TOKEN=
QRADAR_APP_UUID=<your unique app UUID>
```

After you add this line, you can run your app with the QRadar App SDK:

```bash
qapp run
```

The QRadar App SDK reports the port that the app is running on so you can access the app at `http://localhost:<_app port_>`.

## QPyLib Encdec

To find out more about the QPyLib Encdec module, see 
[Secure data storage and encryption](../documentation/secure_data_storage_and_encryption.md).
