# Installing NodeJS as a source dependency

You can install NodeJS as a source dependency for your app and replace the default Flask webserver with a NodeJS
Express server.

## Prerequisites

- QRadar App SDK v2
- Docker
- NodeJS and NPM installed

## Create the app

Create a new folder for your app:

```bash
mkdir NodeJSApp && cd NodeJSApp
```

Use the QRadar App SDK to initialise the app code:

```bash
qapp create
```

## Add the NodeJS and NPM RPMs

1. Create a new folder for the NodeJS and NPM RPMs that will be installed called `container/rpm`.

```bash
mkdir container/rpm
```

2. Download the latest NodeJS and NPM RPMs for RHEL UBI 8 using Docker:

```bash
docker run                                                    \
    -v $(pwd)/container/rpm:/rpm                              \
    registry.access.redhat.com/ubi8/ubi                       \
    yum download nodejs-10.21.0 npm-6.14.4 --downloaddir=/rpm
```

This uses the RHEL UBI 8 OS running inside Docker to download the NodeJS and NPM RPMs, which can be used by QRadar to install them into the app.

3. Create a new file called `container/rpm/ordering.txt`, which tells QRadar where to find the RPMs to install:

```txt
nodejs-10.21.0-3.module+el8.2.0+7071+d2377ea3.x86_64.rpm
npm-6.14.4-1.10.21.0.3.module+el8.2.0+7071+d2377ea3.x86_64.rpm
```

Make these file paths match the file names of the RPMs you downloaded.

## Write the manifest

Edit the `manifest.json` file to use the following values to make it more relevant to
the app.

**Example:**

```json
{
  "name": "NodeJSApp",
  "description": "App that uses NodeJS and an Express server",
  "version": "1.0.0",
  "image": "qradar-app-base:2.0.0",
  "load_flask": "false",
  "areas": [
    {
      "description": "Dummy tab that displays text from the node app",
      "id": "NodeJSHelloWorld",
      "named_service": "nodeservice",
      "required_capabilities": [],
      "text": "NodeHelloWorld",
      "url": "/index"
    }
  ],
  "services": [
    {
      "command": "node /opt/app-root/app/server.js",
      "directory": "/opt/app-root/app",
      "endpoints": [
        {
          "name": "appindexpage",
          "path": "/index",
          "http_method": "GET"
        }
      ],
      "name": "nodeservice",
      "path": "/index",
      "port": 5000,
      "version": "1",
      "stdout_logfile": "/opt/app-root/store/log/node_out.log",
      "stderr_logfile": "/opt/app-root/store/log/node_err.log"
    }
  ],
  "uuid": "<your unique app UUID>"
}
```

This manifest overrides the Flask webserver and instructs QRadar not to load Flask:

```json
"load_flask": "false",
```

The manifest then defines an `area` in the QRadar UI to display the app, called
`NodeJSHelloWorld` that is populated by calling the `/index` endpoint in the
named service called `nodeservice`:

```json
"areas": [
  {
    "description": "Dummy tab that displays text from the node app",
    "id": "NodeJSHelloWorld",
    "named_service": "nodeservice",
    "required_capabilities": [],
    "text": "NodeHelloWorld",
    "url": "/index"
  }
],
```

Finally, the manifest describes and defines a named service, which is a custom
command that will run the NodeJS Express server, exposing the path `/index`.
This named service also includes a `command` to run the NodeJS server,
alongside paths to output logs:

```json
"services": [
  {
    "command": "node /opt/app-root/app/server.js",
    "directory": "/opt/app-root/app",
    "endpoints": [
    {
        "name": "appindexpage",
        "path": "/index",
        "http_method": "GET"
    }
    ],
    "name": "nodeservice",
    "path": "/index",
    "port": 5000,
    "version": "1",
    "stdout_logfile": "/opt/app-root/store/log/node_out.log",
    "stderr_logfile": "/opt/app-root/store/log/node_err.log"
  }
],
```

These elements combined define how the NodeJS Express server is called
and displayed as part of the QRadar UI.

## Set up NPM to manage dependencies

1. Remove the `app/` folder, and recreate it as an empty folder:
```bash
rm -r app/ && mkdir app/ && cd app/
```

2. Use NPM to create a `package.json` file for managing your app's NodeJS dependencies:

```bash
npm init
```

3. Answer the questions when prompted. A `package.json` file is generated.

**Example:**

```json
{
  "name": "nodejs_app",'
  "version": "1.0.0",
  "description": "App that uses NodeJS and an Express server",
  "main": "server.js",
  "author": "IBM",
  "license": "<your license>"
}
```

4. To install express as a dependency, run the folowing command:

```bash
npm install --save-dev express
```

Express is now a dependency, and a new `app/node_modules/` folder is 
generated to store dependencies. 

**Tip:** Exclude this folder from version control.

## Write the NodeJS server

Writing the JavaScript server side code sets up some HTTP endpoints that respond with a simple message when accessed.

Create a new file called `app/server.js` with the following code:

```javascript
const express = require('express')

const app = express()

app.get('/index', (req, res) => res.send('Hello World!'))

app.get('/debug', (req, res) => res.send('Pong!'))

app.listen(5000, () => console.log('Server running on port 5000!'))
```

## Run and Package the app

Before running or packaging your app, ensure that you pull your app's NPM
dependencies down to the `app/node_modules` folder. This is vital to ensure that
users can install and run the app without internet access:

```bash
cd app && npm install && cd ..
```

To run the app locally from the project root, type the following command:

```bash
qapp run
```

To package and deploy your app from the project root, type the following commands:

```bash
qapp package -p <_app zip name_>

qapp deploy -p <_app zip name_> -q <q_radar console_> -u <_qradar user_>
```
