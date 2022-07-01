# Getting available API versions

You can create an app that can query QRadar to determine which API versions are available in the version of
QRadar the app is running on.

Create a simple app that displays a table with the latest API version, all
available API versions, and a table of features that are supported by the available API versions.

## Prerequisites

- QRadar App SDK v2

## API version endpoint

The `/api/help/versions` endpoint reports which API versions are available on QRadar. The endpoint reports which API
versions exist, if they are deprecated, and if they have been removed. You can use this information determine which
API features are available to the app, allowing the app to customise its behaviour based on this.

A typical response from the endpoint looks like this:

```json
[
  ...
  {
    "version": "15.1",
    "deprecated": false,
    "removed": false,
    "root_resource_ids": [...],
    "id": 28,
  },
  {
    "version": "16.0",
    "deprecated": false,
    "removed": false,
    "root_resource_ids": [...],
    "id": 29,
  },
  {
    "version": "17.0",
    "deprecated": false,
    "removed": false,
    "root_resource_ids": [...],
    "id": 30,
  }
]
```

The `/api/help/versions` endpoint is available in QRadar API versions `6.0` and above.

## Create the app

1. Create a new directory for your app:

```bash
mkdir APIVersion && cd APIVersion
```

2. Use the QRadar App SDK to initialise the app code:

```bash
qapp create
```

## Write the app endpoints

Edit the `app/views.py` file using the following code:

```python
from flask import Blueprint, render_template
from packaging import version as package_version
from qpylib import qpylib

# pylint: disable=invalid-name
viewsbp = Blueprint('viewsbp', __name__, url_prefix='/')

# A list of features, paired with a list of known API versions that support the feature
FEATURES = [{
    'name': 'App multi-tenancy',
    'versions': ['13.0', '13.1', '14.0', '15.0', '15.1', '16.0', '17.0']
}, {
    'name': 'Proxy server API',
    'versions': ['13.0', '13.1', '14.0', '15.0', '15.1', '16.0', '17.0']
}, {
    'name': 'Certificate management API',
    'versions': ['16.0', '17.0']
}]


@viewsbp.route('/')
@viewsbp.route('/index')
def index():
    # Retrieve the API versions, parsing into a JSON list
    response = qpylib.REST('get', '/api/help/versions')
    versions = response.json()
    # Iterate over the features and determine which ones are enabled
    enabled_features = []
    for feature in FEATURES:
        enabled_features.append({
            'name': feature['name'],
            'enabled': is_feature_enabled(feature, versions)
        })
    return render_template('hello.html',
                           latest=get_latest_version(versions),
                           versions=versions,
                           features=enabled_features)


def get_latest_version(versions):
    latest = None
    for version in versions:
        # package_version.parse is from the packaging library, allows comparison of version strings
        if latest is None or package_version.parse(
                version['version']) > package_version.parse(latest):
            latest = version['version']
    return latest


def is_feature_enabled(feature, versions):
    # Determines if a feature is available by looping through the enabled versions and checking if there is an API
    # version enabled that supports the feature
    for feature_version in feature['versions']:
        for version in versions:
            if not version['removed'] and feature_version == version['version']:
                return True
    return False
```

This script sets up the `/index` endpoint, which returns a Jinja template that is injected with the latest API version, all
available API versions, and a list of features that are marked as either enabled or disabled.

## Write the Jinja template

Edit the `app/templates/hello.html` file using the following code:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>API versions</title>
  </head>
  <body>
    <h1>API versions</h1>
    <div>
      <h2>Latest API version:</h2>
      <span>{{ latest }}</span>
    </div>
    <div>
      <h2>API versions</h2>
      <table>
        <tr>
          <th>Version</th>
          <th>Deprecated</th>
          <th>Removed</th>
        </tr>
        {% for version in versions %}
          <tr>
            <td>{{ version.version }}</td>
            <td>{{ version.deprecated }}</td>
            <td>{{ version.removed }}</td>
          </tr>
        {% endfor %}
      </table>
    </div>
    <div>
      <h2>Features</h2>
      <table>
        <tr>
          <th>Feature</th>
          <th>Enabled</th>
        </tr>
        {% for feature in features %}
          <tr>
            <td>{{ feature.name }}</td>
            <td>{{ feature.enabled }}</td>
          </tr>
        {% endfor %}
      </table>
    </div>
  </body>
</html>
```

This html template includes the latest API version, a table of available API versions, and a table of features
that are marked as enabled or disabled.

## Test the app locally

The app is now ready to test locally using the QRadar App SDK.

Before you run the app locally, follow the steps in '[Allowing apps running through the SDK to make API
calls](./allowing_apps_running_through_the_sdk_to_make_api_calls.md)'.

After you have prepared your environment to allow local apps to make API calls, you can run the app:

```bash
qapp run
```

The QRadar App SDK reports the port that the app is running on, allowing
you to access the app at `http://localhost:<_app port_>`.

## Packaging the app and deploying it

The app can also be packaged and deployed to QRadar system using the QRadar App SDK:

```bash
qapp package -p <_app zip name_>

qapp deploy -p <_app zip name_> -q <_qradar console_> -u <_qradar user_>
```
