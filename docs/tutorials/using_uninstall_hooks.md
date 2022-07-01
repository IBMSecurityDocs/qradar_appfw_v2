# Using uninstall hooks

> Please note that uninstall hooks are only supported in QRadar versions 7.5.0 and above.

This tutorial will outline how use [uninstall hooks](../documentation/uninstall_hooks.md) with a QRadar app.

In this tutorial we will set up a simple QRadar app that will create and allow modification of some QRadar reference
data - when the app is uninstalled the app will use an uninstall hook to delete and clean up any of the created
reference data.

The app will create a reference map called `uninstall_hooks_app_ref_map` on startup, and add the key `arbitrary_key`
to the map with an initial value of `100`, the app will then allow the user to modify this reference data. When the
app is uninstalled the `uninstall_hooks_app_ref_map` reference map will be deleted alongside any of its keys and values.

## Prerequisites

This tutorial requires the following dependencies:

- QRadar App SDK v2
- QRadar version 7.5.0 and above

## Create the app

Create a new directory for the app:

```bash
mkdir UninstallHooks && cd UninstallHooks
```

Use the QRadar App SDK to initialise the app code:

```bash
qapp create
```

## Write the manifest

Open the `manifest.json` file to edit some values to make it more relevant to the app, making it look like this:

```json
{
  "name": "Uninstall Hooks app",
  "description": "Demonstrates Uninstall Hooks",
  "version": "1.0.0",
  "image": "qradar-app-base:2.0.0",
  "areas": [
    {
      "id": "UninstallHookRefData",
      "text": "Uninstall hook ref data",
      "description": "Modify uninstall hook reference data",
      "url": "index",
      "required_capabilities": [
        "ADMIN"
      ]
    }
  ],
  "rest_methods": [
    {
      "name": "uninstall_delete_reference_data",
      "url": "/uninstall_delete_reference_data",
      "method": "POST"
    }
  ],
  "uninstall_hooks": [
    {
      "description": "Delete app reference data",
      "rest_method": "uninstall_delete_reference_data",
      "last_instance_only": "true"
    }
  ],
  "authentication": {
    "oauth2": {
      "authorisation_flow": "CLIENT_CREDENTIALS",
      "requested_capabilities": [
        "ADMIN"
      ]
    }
  },
  "uuid": "<your unique app UUID>"
}
```

This manifest defines some key properties:

- An `area` that provides the user interface for the app that will allow viewing and modifying the reference data,
requires the `ADMIN` capability.
- A `rest_method` called `uninstall_delete_reference_data` that allows a `POST` request to the
`/uninstall_delete_reference_data` endpoint in the app. This will be the REST method that is used as the uninstall
hook.
- A `uninstall_hook` that targets the `uninstall_delete_reference_data` REST method, which will run when the last
instance of the app is uninstalled.
- OAuth authentication using the `authentication` object to allow the uninstall hook to interact with the QRadar API
(since the app needs to be able to make QRadar API requests in the background on startup and uninstall).

## Write the code to manage the reference data

Create a new file called `ref_data.py` to handle any interactions with reference data, allowing the app to create, get,
update, and delete reference data:

```python
from qpylib import qpylib

REFERENCE_DATA_MAP_NAME = "uninstall_hooks_app_ref_map"
REFERENCE_DATA_MAP_KEY = "arbitrary_key"
REFERENCE_DATA_INIT_VALUE = 100


def update_reference_data_value(value):
    qpylib.REST(
        'post', '/api/reference_data/maps/{0}?key={1}&value={2}'.format(
            REFERENCE_DATA_MAP_NAME, REFERENCE_DATA_MAP_KEY, value))


def get_reference_data_value():
    response = qpylib.REST(
        'get', '/api/reference_data/maps/{0}'.format(REFERENCE_DATA_MAP_NAME))
    resp_data = response.json()
    data = resp_data['data']
    reference = data[REFERENCE_DATA_MAP_KEY]
    return reference['value']


def delete_reference_data():
    qpylib.REST('delete',
                '/api/reference_data/maps/{0}'.format(REFERENCE_DATA_MAP_NAME))


def create_reference_data_key_if_not_exists(data):
    if REFERENCE_DATA_MAP_KEY not in data:
        # Key value doesn't exist, create new
        update_reference_data_value(REFERENCE_DATA_INIT_VALUE)


def create_reference_data_map_if_not_exists():
    response = qpylib.REST(
        'get', '/api/reference_data/maps/{0}'.format(REFERENCE_DATA_MAP_NAME))
    if response.status_code == 404:
        # Doesn't exist, create new
        response = qpylib.REST(
            'post',
            '/api/reference_data/maps?element_type=NUM&name={0}'.format(
                REFERENCE_DATA_MAP_NAME))
        resp_data = response.json()
        create_reference_data_key_if_not_exists(resp_data)
    else:
        resp_data = response.json()
        create_reference_data_key_if_not_exists(resp_data)
```

## Add reference data creation to app startup

Modify the app startup by editing `app/__init__.py`, add an import that will allow the startup code to run
the `create_reference_data_map_if_not_exists` function to create reference data:

```python
from .ref_data import create_reference_data_map_if_not_exists
```

Next add a call to this function as part of the `create_app` function:

```python
    # Import additional endpoints.
    # For more information see:
    #   https://flask.palletsprojects.com/en/1.1.x/tutorial/views
    from . import views
    qflask.register_blueprint(views.viewsbp)
    from . import dev
    qflask.register_blueprint(dev.devbp)

    create_reference_data_map_if_not_exists()

    return qflask
```

When the app starts up it will now create the reference data if it doesn't already exist.

## Add the app HTTP endpoints

Add the app HTTP endpoints by editing `app/views.py`:

```python
from flask import Blueprint, render_template, request, redirect
from .ref_data import delete_reference_data, get_reference_data_value, update_reference_data_value

# pylint: disable=invalid-name
viewsbp = Blueprint('views', __name__, url_prefix='/')


@viewsbp.route('/')
@viewsbp.route('/index')
def index():
    return render_template('index.html', value=get_reference_data_value())


@viewsbp.route('/set_reference_data', methods=['POST'])
def set_reference_data():
    value = request.form['value']
    update_reference_data_value(value)
    return redirect('/', code=303)


@viewsbp.route('/uninstall_delete_reference_data', methods=['POST'])
def uninstall_delete_reference_data():
    delete_reference_data()
    return "OK!", 200
```

This sets up three endpoints:

- `/index` renders and returns the app's user interface HTML, injected with the reference data value
- `/set_reference_data` accepts a `POST` request using form data to update the reference data value
- `/uninstall_delete_reference_data` is the uninstall hook, it will delete the reference data when it is called.


## Write the Jinja template

Delete `app/templates/hello.html` and create a new file `app/templates/index.html`:

```html
<!DOCTYPE html>
<html>

<head>
  <title>Uninstall hook reference data</title>
</head>

<body>
  <h1>Managing reference data with an uninstall hook</h1>
  <form action="set_reference_data" method="POST">
    <label id="ref-data-value-label" for="ref-data-value-input">Reference data value:</label>
    <input id="ref-data-value-input" type="number" name="value" value="{{ value }}" />
    <input id="ref-data-value-submit" type="submit" value="Set reference data" />
  </form>
</body>

</html>
```

This Jinja template provides the user interface for the app, allowing the user to view and update the reference data
value.

## Test the app

Testing this app requires that the app be bundled as an extension, follow these steps to create an extension:

- Package the app using the QRadar App SDK v2 with

```bash
qapp package -p uninstall_hooks.zip
```

- Install the app on a QRadar 7.5.0+ system using the SDK with

```bash
qapp deploy -q <console address> -u <user> -p uninstall_hooks.zip
```

- Authorize the app install, note the installed app ID
- Once the app has successfully installed an extension can be created, use SSH to log in to QRadar as the root user.
- Run the content management export tool to generate an extension:

```bash
/opt/qradar/bin/contentManagement.pl -a export -c installed_application -i <app id from the install>
```

- The content management tool will report an extension zip that has been generated, copy this zip back from QRadar
using the SCP tool.
- Delete the previously installed app using the SDK:

```bash
qapp delete -q <console address> -u <user> -a <app id from the install>
```

You now have an extension that can be installed on a QRadar box, install the packaged extension through the extension
management screen, choosing to start an instance of the app.

Once the app is installed you should be able to access the `Uninstall hook ref data` area in the QRadar UI to modify
the reference data. You can see the reference data by sending a GET request to the
`/api/reference_data/maps/uninstall_hooks_app_ref_map` API endpoint on QRadar.

Try modifying the reference data, and check that it updates as expected, before uninstalling the extension and checking
that the reference data has been deleted correctly, the `/api/reference_data/maps/uninstall_hooks_app_ref_map` should
return a `404 Not Found` if the uninstall hook ran successfully.

You can monitor the app's `/opt/app-root/store/log/startup.log` file to watch the uninstall hook that is running, the
app will log out any HTTP requests it receives, including requests sent during the uninstall. The logs should look like
this:

```txt
169.254.3.1 - - [28/Apr/2021 12:52:53] "GET /debug HTTP/1.1" 200 -
169.254.3.1 - - [28/Apr/2021 12:53:16] "POST /uninstall_delete_reference_data HTTP/1.1" 200 -
```

This shows a successful response to the uninstall hook (HTTP `200 OK`).
