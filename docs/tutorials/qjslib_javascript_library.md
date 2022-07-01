# QJSLib - Javascript library

The QRadar® JavaScript library (QJSLib) provides helper functions for common QRadar calls that you can integrate into
your own scripts.

You can use the helper functions that are included in the app framework JavaScript library (QJSLib) to do the following
tasks:

- Get information on the current application, user, item, or selected table rows.
- Open the Details page for an asset.
- Search for an event or flow by using a specified AQL query.
- Open the Details page for an offense.
- Call a REST method by using an XMLHttpRequest.
- Retrieve named services and named service endpoints.

For a full list of QJSLib functionality and a reference of available functions, see the [QJSLib GitHub
wiki](https://github.com/IBM/qjslib/wiki/qappfw).

## Importing QJSLib

You can import QJSLib either as a dependency referenced directly in the HTML and loaded through the
browser, or bundled with webpack as a packaged dependency with your JavaScript bundle (imported with NPM).

The following tutorials explain and showcase each approach.

### Browser script

This tutorial requires the following dependencies:

- QRadar app SDK v2

#### Create the Browser App

Create a new folder for the app:

```bash
mkdir QJSLibBrowser && cd QJSLibBrowser
```

Use the QRadar app SDK to initialise the app code:

```bash
qapp create
```

#### Write the browser app manifest

Open the `manifest.json` file to edit some values to make it more relevant to the app.

**Example:**

```json
{
  "name": "QJSLib as a browser import",
  "description": "App showing how to use QJSLib imported in the browser",
  "version": "1.0.0",
  "image": "qradar-app-base:2.0.0",
  "areas": [
    {
      "id": "QQJSLibBrowser",
      "text": "QJSLib",
      "description": "Area displaying QJSLib functionality",
      "url": "index",
      "required_capabilities": []
    }
  ],
  "uuid": "<your unique app UUID>"
}
```

This manifest defines an area called `QQJSLibBrowser` that serves the app user interface.

#### Write the browser app HTML

Delete `app/templates/hello.html` and create a new file called `app/templates/index.html` using this code:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>QJSLib as a browser import</title>
  </head>
  <body>
    <h1>QJSLib as a browser import</h1>
    <p id="qjslib-load-status">QJSLib not loaded</p>
    <p id="current-user-label">
      Currently logged in as: <span id="current-user"></span>
    </p>
    <p id="installed-apps-label">
      Installed apps:
      <ul id="installed-apps"></ul>
    </p>
    <script src="./static/qjslib/qappfw.min.js"></script>
    <script src="./static/main.js"></script>
  </body>
</html>
```

This HTML defines some places for us to modify with our JavaScript, adding in values retrieved using QJSLib, such as
the currently logged in user and a list of all installed apps.

The HTML also handles loading both the QJSLib script from `app/static/qjslib/qappfw.min.js` and the app JavaScript file
from `app/static/main.js`.

#### Write the browser app endpoint

Add the app HTTP endpoint by editing `app/views.py`:

```python
from flask import Blueprint, render_template

# pylint: disable=invalid-name
viewsbp = Blueprint('viewsbp', __name__, url_prefix='/')


# Simple endpoint that returns index.html, all work is done client side
# with QJSLib
@viewsbp.route('/index')
def index():
    return render_template('index.html')
```

This sets up the app `/index` endpoint to return `app/templates/index.html`.

#### Write the browser JavaScript

Add the app JavaScript by creating a file called `app/static/main.js` using the following code:

```javascript
const QRadar = window.qappfw.QRadar;

const loadField = document.getElementById("qjslib-load-status");
const currentUserField = document.getElementById("current-user");
const installedAppsList = document.getElementById("installed-apps");

QRadar.fetch("/api/gui_app_framework/applications")
    .then(function(response)  { return response.json() })
    .then(function(apps) {
        for (const app of apps) {
            const appItem = document.createElement('li');
            appItem.appendChild(document.createTextNode(app.manifest.name));
            installedAppsList.appendChild(appItem);
        }
    })
    .catch(function(error) { alert(error) });

const currentUser = QRadar.getCurrentUser()
currentUserField.innerText = currentUser.username;

loadField.innerText = "QJSLib loaded successfully"
```

This Javascript first reassigns the QJSLib library from the global window scope into `QRadar` for easier use, and
then gets HTML elements from the page, which are referenced later.

The JavaScript then makes a number of calls using QJSLib, assigning the output to the HTML elements that were retrieved earlier.
This includes an asynchronous HTTP request using `QRadar.fetch`.

#### Run and package the browser app

The app can then be run locally by using the following command:

```bash
qapp run
```

Use the following commands to package and deploy the app:

```bash
qapp package -p <app zip name>

qapp deploy -p <app zip name> -q <qradar console> -q <qradar user>
```

### NPM package bundled with Webpack

This tutorial requires the following dependencies:

- QRadar app SDK version 2
- NodeJS
- NPM

#### Create the NPM app

Create a new folder for the app:

```bash
mkdir QJSLibNPM && cd QJSLibNPM
```

Use the QRadar app SDK to initialise the app code:

```bash
qapp create
```

#### Set up NPM and Webpack

To set up NPM and webpack for handling JavaScript dependencies and bundling, start with the following command:

```bash
npm init
```

This command asks several questions for setting up your project. After you answer them, a `package.json` file generated.

Add QJSLib as a dependency:

```bash
npm install qjslib
```

Add Webpack as a development dependency:

```bash
npm install webpack webpack-cli --save-dev
```

Configure webpack by creating a `webpack.config.js`:

```javascript
const path = require('path');

module.exports = {
    entry: './src/index.js',
    output: {
        filename: 'main.js',
        path: path.resolve(__dirname, 'app/static'),
    },
};
```

This configures Webpack to take the `src/index.js` file and bundle any of its dependencies into `app/static/main.js`.

5. Finally, configure your `package.json` using the following code:

```json
{
  "name": "qjslib_npm",
  "version": "1.0.0",
  "description": "Sample app showing how to use QJSLib imported as an NPM package",
  "main": "src/index.js",
  "scripts": {
    "build": "webpack --mode=development"
  },
  "author": "IBM",
  "license": "GSA ADP",
  "dependencies": {
    "qjslib": "^1.1.0"
  },
  "devDependencies": {
    "webpack": "^4.44.1",
    "webpack-cli": "^3.3.12"
  }
}
```

This key field allows you to easily build your app's JavaScript:

```json
  "scripts": {
    "build": "webpack --mode=development"
  },
```

#### Write the NPM app manifest

Open the `manifest.json` file to edit some values to make it more relevant to the app:

```json
{
  "name": "QJSLib as an NPM package",
  "description": "App showing how to use QJSLib imported as an NPM package",
  "version": "1.0.0",
  "image": "qradar-app-base:2.0.0",
  "areas": [
    {
      "id": "QQJSLibNPM",
      "text": "QJSLib",
      "description": "Area displaying QJSLib functionality",
      "url": "index",
      "required_capabilities": []
    }
  ],
  "uuid": "<your unique app UUID>"
}
```

This manifest defines an area called `QQJSLibNPM` that serves the app user interface.

#### Write the NPM app HTML

Delete `app/templates/hello.html` and create a new file called `app/templates/index.html` using the following code:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>QJSLib as an NPM package</title>
  </head>
  <body>
    <h1>QJSLib as an NPM package</h1>
    <p id="qjslib-load-status">QJSLib not loaded</p>
    <p id="current-user-label">
      Currently logged in as: <span id="current-user"></span>
    </p>
    <p id="installed-apps-label">
      Installed apps:
      <ul id="installed-apps"></ul>
    </p>
    <script src="static/main.js"></script>
  </body>
</html>
```

This HTML defines some places to modify with JavaScript, adding in values retrieved using QJSLib, such as
the currently logged in user and a list of all installed apps.

#### Write the NPM app endpoint

Add the app HTTP endpoint by editing `app/views.py`:

```python
from flask import Blueprint, render_template

# pylint: disable=invalid-name
viewsbp = Blueprint('viewsbp', __name__, url_prefix='/')


# Simple endpoint that returns index.html, all work is done client side
# with QJSLib
@viewsbp.route('/index')
def index():
    return render_template('index.html')
```

This sets up the app `/index` endpoint to return `app/templates/index.html`.

#### Write the NPM JavaScript

Add the app JavaScript by creating a file called `src/index.js`:

```javascript
import { QRadar } from "qjslib";

const loadField = document.getElementById("qjslib-load-status");
const currentUserField = document.getElementById("current-user");
const installedAppsList = document.getElementById("installed-apps");

QRadar.fetch("/api/gui_app_framework/applications")
    .then((response) => response.json())
    .then((apps) => {
        for (const app of apps) {
            const appItem = document.createElement('li');
            appItem.appendChild(document.createTextNode(app.manifest.name));
            installedAppsList.appendChild(appItem);
        }
    })
    .catch((error) => alert(error));

const currentUser = QRadar.getCurrentUser()
currentUserField.innerText = currentUser.username;

loadField.innerText = "QJSLib loaded successfully"
```

This JavaScript file is transpiled and bundled at build time into `app/static/main.js`, along with QJSLib.

This Javascript first loads the QJSLib library for use in this script:

```javascript
import { QRadar } from "qjslib";
```

The JavaScript then makes a number of calls using QJSLib, assigning the output to the HTML elements retrieved earlier.
This includes an asynchronous HTTP request using `QRadar.fetch`.

#### Run and package the NPM app

You can run the app locally by using the following command:

```bash
npm run build && qapp run
```

Use the following commands to package and deploy the app:

```bash
npm run build && qapp package -p <app zip name>

qapp deploy -p <app zip name> -q <qradar console> -q <qradar user>
```
