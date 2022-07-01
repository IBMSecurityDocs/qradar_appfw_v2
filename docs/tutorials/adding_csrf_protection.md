# Adding CSRF protection

Cross Site Request Forgery (CSRF) is a vulnerability that QRadar apps must be secured against. 

You can take steps to protect your app against CSRF by using the [Flask-WTF](https://flask-wtf.readthedocs.io/en/stable/)
library.

Follow the steps below to create a simple app that includes a form submission protected from CSRF attacks by Flask-WTF. The
app allows a form to submit a POST request to an app endpoint. This form allows users to set
a value for the app to store and then display to anyone who loads the app.

## Prerequisites

- QRadar App SDK v2
- Docker

## Create the app

Create a new directory for your app:
```bash
mkdir CSRFApp && cd CSRFApp
```

Use the QRadar App SDK to initialise the app code:
```bash
qapp create
```

This generates code for a simple hello world QRadar app that you can use as a starting point.

## Download the CSRF Python libraries

Create a new directory to store Python libraries for the app:
```bash
mkdir container/pip
```

Download the WTForms and Flask-WTF Python libraries for RHEL UBI 8 using Docker:

```bash
docker run                                                    \
    -v $(pwd)/container/pip:/opt/app-root/pip                 \
    registry.access.redhat.com/ubi8/python-38                 \
    pip download Flask-WTF WTForms --no-deps --dest /opt/app-root/pip
```

**Note:** This uses the RHEL UBI 8 OS running inside Docker to download the Flask-WTF and WTForms Python libraries, which QRadar can install into the app.

## Add the CSRF initialization code

The app needs to initialize the Flask-WTF library at startup so it can be used by Flask to generate CSRF tokens and
validate them. Modify the `app/__init__.py` startup file to include the following changes:

Import the required libraries:
```python
import secrets # For generating a new secret key
from flask_wtf.csrf import CSRFProtect # To add CSRF protection
from qpylib.encdec import Encryption, EncryptionError # To handle storing the secret key securely
```

Enable the CSRF protection from Flask-WTF at startup:
```python
# Create a Flask instance.
qflask = Flask(__name__)

# Add CSRF protection to the Flask app
csrf = CSRFProtect()
csrf.init_app(qflask)
```

You must provide the Flask-WTF library with a secret, which is stored in Flask's configuration as `SECRET_KEY`, that it can
use to reliably generate and validate CSRF tokens. At startup, this is either read from a local or encrypted file. If the file does not exist, a new one must be generated:

```python
# Generate or retrieve a secret key
secret_key = ""
try:
    # Read in secret key
    secret_key_store = Encryption({'name': 'secret_key', 'user': 'shared'})
    secret_key = secret_key_store.decrypt()
except EncryptionError:
    # If secret key file doesn't exist/fail to decrypt it,
    # generate a new random password for it and encrypt it
    secret_key = secrets.token_urlsafe(64)
    secret_key_store = Encryption({'name': 'secret_key', 'user': 'shared'})
    secret_key_store.encrypt(secret_key)

qflask.config["SECRET_KEY"] = secret_key
```

## Add the app endpoints

Edit `app/views.py` and to add in the new endpoints:

```python
from flask import Blueprint, render_template, Response, request, redirect, session
from qpylib.encdec import Encryption, EncryptionError

# pylint: disable=invalid-name
viewsbp = Blueprint('viewsbp', __name__, url_prefix='/')

@viewsbp.route('/')
@viewsbp.route('/index')
def hello():
    # Get value if it's been set, return the Jinja template with the value
    try:
        # Read in value
        value_store = Encryption({'name': 'value', 'user': 'shared'})
        value = value_store.decrypt()
        return render_template('hello.html', value=value)
    except EncryptionError:
        # If the value hasn't been set/fails to read, return None as the value
        return render_template('hello.html', value=None)

@viewsbp.route('/value', methods=['POST'])
def set_value():
    # Set the value to the value provided in the HTTP POST request
    value_store = Encryption({'name': 'value', 'user': 'shared'})
    value_store.encrypt(request.form['value'])
    return redirect('/index', code=303)

```

These two endpoints show the user a rendered page with the app value retrieved by using QPyLib's encdec module,
and sets the value using the encdec module. The code in the example has no CSRF protection code, as this is automatically
handled by the Flask-WTF library globally.

## Update the Jinja template

Edit `app/templates/hello.html` to display the rendered user session value and include a form allowing the session
value to be updated, including the CSRF token:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Value</title>
    <link href="static/styles.css" rel="stylesheet">
  </head>
  <body>
    <p>The value is: '{{ value }}'</p>
    <form action="/value" method="POST">
      <!-- Inject the CSRF token into the form -->
      <input type="hidden" name="csrf_token" value="{{ csrf_token() }}"/>
      <input type="text" name="value" placeholder="New value" />
      <input type="submit" value="Set" />
    </form>
  </body>
</html>
```

## Test the app locally

You can now test the app by running it locally with the QRadar App SDK.

**Important:** There is one extra step before you can run this app, you must add a line to the `qenv.ini` file to inject the required
`QRADAR_APP_UUID` environment variable into the app. This is required for the QPyLib Encdec module to work when
running a QRadar app locally, it is not required when deployed on a QRadar instance. The `qenv.ini` file should look
like this:

```ini
[qradar]
QRADAR_CONSOLE_FQDN=
QRADAR_CONSOLE_IP=

[proxy]
QRADAR_REST_PROXY=

[app]
SEC_ADMIN_TOKEN=
QRADAR_APP_UUID=<your unique app UUID which can be retrieved from the manifest.json file>
```

After you add this line, you can run your app with the QRadar App SDK:

```bash
qapp run
```

The QRadar App SDK displays the port that the app is running on, allowing
you to access the app at `http://localhost:<_app port_>`.

## Packaging the app and deploying it

The app can also be packaged and deployed to a QRadar system using the QRadar App SDK:

```bash
qapp package -p <app zip name>

qapp deploy -p <app zip name> -q <qradar console> -u <qradar user>
```
