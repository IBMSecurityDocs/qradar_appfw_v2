# Using QRadar CSRF

> Please note that the QRadar App CSRF feature is only supported in QRadar versions 7.3.3 Fix Pack 9 and above.

This tutorial will explain how to use the [QRadar CSRF feature](../documentation/qradar_csrf.md) in your QRadar app.

In this tutorial we will set up a simple QRadar app that will keep track of a single value, stored in a text file,
which can be incremented or decremented using buttons in a simple user interface. The app itself is simple, intended
to demonstrate how to set up an app to use the QRadar CSRF feature.

## Prerequisites

This tutorial requires the following dependencies:

- QRadar App SDK v2
- QRadar version 7.3.3 Fix Pack 9 and above

## Create the app

Create a new directory for the app:

```bash
mkdir QRadarCSRF && cd QRadarCSRF
```

Use the QRadar App SDK to initialise the app code:

```bash
qapp create
```

## Write the manifest

Open the `manifest.json` file to edit some values to make it more relevant to the app, making it look like this:

```json
{
  "name": "QRadar CSRF",
  "description": "App demonstrating how to use QRadar CSRF",
  "version": "1.0",
  "image": "qradar-app-base:2.0.4",
  "areas": [
    {
      "id": "QRadarCSRF",
      "text": "QRadar CSRF",
      "description": "QRadar CSRF area",
      "url": "/",
      "required_capabilities": [
        "ADMIN"
      ]
    }
  ],
  "use_qradar_csrf": "true",
  "uuid": "<your unique app UUID>"
}
```

The key property in this manifest is the `use_qradar_csrf` flag - since this has been set to true the app will be
protected by QRadar's CSRF protection.

## Write the HTTP endpoints

Update `app/views.py` to add an endpoint for rendering the UI HTML with the retrieved value, and two more that use
`POST` requests to handle incrementing and decrementing the value.

```python
from flask import Blueprint, render_template
import os.path

# pylint: disable=invalid-name
viewsbp = Blueprint('viewsbp', __name__, url_prefix='/')

VALUE_FILE = "/opt/app-root/store/value"
INITIAL_VALUE = 0


@viewsbp.route('/')
def index():
    init_value()
    with open(VALUE_FILE, 'r') as file:
        value_str = file.read().replace('\n', '')
        return render_template('index.html', value=value_str)


@viewsbp.route('/increment', methods=['POST'])
def increment():
    init_value()
    modify_value(1)
    return 'OK'


@viewsbp.route('/decrement', methods=['POST'])
def decrement():
    init_value()
    modify_value(-1)
    return 'OK'


def init_value():
    # If the value file does not exist yet, initialise it with an initial value
    if not os.path.isfile(VALUE_FILE):
        with open(VALUE_FILE, "w") as f:
            f.write(str(INITIAL_VALUE))


def modify_value(modifier):
    # Modify the value, reading the existing value, adding a modifier to the value and saving the modified value
    # back to the value file
    with open(VALUE_FILE, "r") as f:
        value_str = f.read()

    value = int(value_str)
    value = value + modifier

    with open(VALUE_FILE, "w") as f:
        f.write(str(value))
```

## Write the Jinja template

Delete `app/templates/hello.html` and create a new file `app/templates/index.html`:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>QRadar CSRF</title>
    <script type="text/javascript" src='./static/qjslib/qappfw.min.js'></script>
    <script>
        var QRadar = window.qappfw.QRadar;

        function increment() {
            QRadar.fetch("increment", {
                method: "POST"
            }).then(function(response) {
                if (response.ok) {
                    location.reload();
                }
            });
        }

        function decrement() {
            QRadar.fetch("decrement", {
                method: "POST"
            }).then(function(response) {
                if (response.ok) {
                    location.reload();
                }
            });
        }
    </script>
  </head>
  <body>
    <h1>QRadar CSRF </h1>
    <p>Value: {{ value }}</p>
    <input type="button" value="Increment" onclick="increment()"/>
    <input type="button" value="Decrement" onclick="decrement()"/>
  </body>
</html>
```

This Jinja template displays the value injected into it, and provides two buttons that allow incrementing and
decrementing the app's value using the QJSLib library's `fetch` function, which automatically handles adding the
`QRadarCSRF` header to the HTTP requests.

## Test the app

The app can then be packaged and deployed with:

```bash
qapp package -p <app zip name>

qapp deploy -p <app zip name> -q <qradar console> -q <qradar user>
```

Visit the app's area tab in the QRadar UI called *QRadar CSRF*, the page will allow retrieving the value, incrementing
and decrementing. You can check the CSRF protection is in place by using `curl` to send a request to one of the app's
endpoints without the `QRadarCSRF` header provided, it should fail with a `403 Forbidden` error.
