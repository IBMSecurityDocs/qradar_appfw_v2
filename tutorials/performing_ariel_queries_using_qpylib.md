# Performing Ariel queries using QPyLib

Create a simple app that can perform queries on QRadar using QPyLib. 

Try these examples of the different ways you can perform a search using QPyLib and how to handle errors properly.

## Prerequisites

*   You'll need the latest version of the QRadar App SDK installed. [Download the SDK from the IBM X-Force App
Exchange](https://exchange.xforce.ibmcloud.com/hub/extension/517ff786d70b6dfa39dde485af6cbc8b).
*   If you want to debug your app locally, you also need the latest version of QPyLib. QPyLib is
QRadar's Python library for making API requests to QRadar. [Download the latest version of QPyLib from GitHub](https://github.com/IBM/qpylib).
*   Optional: Install Docker if you want to run your app locally.

## Installing the SDK and QPyLib

> **Tip:** If you already installed the SDK and QPyLib, you can skip this step.

To install the SDK, run `install.sh`. After you install the SDK, you have access to the `qapp` command
in the command line.

To install QPyLib, ensure that Python 3 is installed, then type the command `python3 setup.py install` to
install QPyLib.

You are now ready to create your app.

## Creating a new app

Start by creating a new folder for your app and navigate to the new folder:

```bash
mkdir ArielApp && cd ArielApp
```

Create a new app using `qapp create`. The `qapp` command interacts with the SDK, and it's the only command
you need when working with the SDK.

The folder you created earlier now contains all of the files a QRadar app needs. You can run your newly created app
using the command `qapp run`.

## Editing the app's manifest

Every QRadar app has a `manifest.json` file that contains metadata about the app. Change the `name` and `description`
fields to make them suitable for your app.

The manifest contains a field called `areas`; An `area` corresponds to a tab in QRadar. Change the `id`, `title` and `description`
fields in the area to make them suitable for your app. The `title` field is shown in the app's tab in QRadar. Update
the `required_capabilities` field to make it an empty array.

Your updated manifest should look like this example:

```json
{
	"name": "Ariel Query App",
	"description": "A simple app showing how to perform Ariel queries using QPyLib.",
	"version": "1.0",
	"image": "qradar-app-base:2.0.0",
	"areas": [
	{
		"id": "ArielQueryApp",
		"text": "Ariel Query App",
		"description": "A simple app showing how to perform Ariel queries using QPyLib.",
		"url": "index",
		"required_capabilities": []
	}
	],
	"uuid": "152e7431-9287-488d-9ac3-dd3b3644a780"
}
```

## Using QPyLib

All the Python code for your app goes in the `app` directory., which contains files that were
automatically generated when you called `qapp create`. 

Use the command `cd app` to enter the `app` directory and open `views.py` in your text editor. The `views.py`
file contains all of the routes your app will use. Think of a route like a page on a website.

This tutorial contains examples of different ways to search Ariel using QPyLib. Start by creating a new route
called `async_search`, using the following commands:

```python
@viewsbp.route('/async_search')
def async_search():
    return None
```

This creates a new route, but it doesn't do anything yet; It just returns `None`.

## Importing QPyLib dependencies

By default, `qapp create` imports all of QPyLib into `views.py`. You don't need all of QPyLib, just the Ariel helper
functions, so replace this text:

```python
from qpylib import qpylib
```

with this text:

```python
from qpylib.ariel import ArielSearch, ArielError
```

## Importing Flask dependencies

We need to copy parameters from HTTP requests, so import the requests module from Flask, replace this text:

```python
from flask import Blueprint, render_template, current_app, send_from_directory
```

with this text:

```python
from flask import Blueprint, render_template, current_app, send_from_directory, request
```

Finally, the responses returned by Ariel are JSON, so import the Python JSON library by using this command:

```python
import json
```

## Ariel setup

To use the Ariel functions from QPyLib, you must create an Ariel object. Above all your routes, but below
your imports, create a new Ariel object as shown:

```python
ariel = ArielSearch()
```

**Tip:** Inside each route you create, you can reference the `ariel` variable to use QPyLib's Ariel functions.

## Performing an asynchronous Ariel search

Some Ariel searches take a significant amount of time to complete, so users won't get the result instantly. These searches can be performed asynchronously, so your app doesn't freeze while waiting for the result of the search query. 

An asynchronous search can be performed by simply calling `search(<your query>)` and passing in your query. Each Ariel
helper method in QPyLib throws an exception if something goes wrong when performing the search, so it's important
to deal with them properly.

Update the `async_search` route you created earlier to perform an asynchronous search, as shown below:

```python
@viewsbp.route('/async_search')
def async_search():

    query = request.args.get('query')

    try:
        response = ariel.search(query)
    except ArielError as error:
        return { "Error": str(error) }

    return json.dumps(response)
```

This code grabs the search query from the request by inspecting the `query` parameter of the GET request. It then
passes the query to `ariel.search()` to actually perform the search. If something goes wrong with the query, for example,
it's malformed, an `ArielError` exception is thrown and an error message is returned. If the query
runs successfully, some JSON containing a `search_id` is returned. The `search_id` can then be used to retrieve
the actual results of the query.

## Performing a synchronous Ariel search

A synchronous search should be used for searches that usually run quickly. An asynchronous search returns `WAIT` if it
isn't complete. After the synchronous search completes successfully, it returns `COMPLETED`, followed by the record count retrieved by the search.

```python
@viewsbp.route('/sync_search')
def sync_search():

    query = request.args.get('query')
    timeout = 15
    sleep_interval = 20

    try:
        response = ariel.search_sync(query, timeout, sleep_interval)
    except ArielError as error:
        return { "Error": str(error) }

    return json.dumps(response)
```

The steps to perform a synchronous search are similar to an asynchronous search, except this
time you use the `search_sync()` function. You only need to provide the `query` argument to `search_sync()`; `timeout`
and `sleep_interval` are optional.

Because a synchronous search requires the app to wait until it has completed, you have the option of setting a timeout.
The timeout value forces the search to fail if the timeout is exceeded. If you notice a lot of timeouts occurring,
try performing an asynchronous search instead. The sleep interval determines how long in seconds QPyLib waits
before checking if the search has completed.

## Querying the status of a search


After performing a search, particularly a long-running one, you might want to check the status of the search to ensure it
was successful. You can do that with the `status()` function from QPyLib.

```python
@viewsbp.route('/search_status')
def search_status():

    search_id = request.args.get('search_id')

    try:
        response = ariel.status(search_id)
    except ArielError as error:
        return { "Error": str(error) }

    return json.dumps(response)
```

The `status()` function takes a search id, rather than an Ariel query as its main parameter. Each time you call
`search()`, the response of a successful search includes its `search_id` in the response. You can use this returned
`search_id` to look up the status of a previous query. In common with previous QPyLib functions, failure to retrieve the
status of a query throws an `ArielError` exception, which should be caught and dealt with.

## Showing all results from a search

Searches can return multiple results, and sometimes it's useful to look at the results of a search, rather than just the
search status. You can accomplish this by using the `results()` function.

```python
@viewsbp.route('/search_results')
def search_results():

    search_id = request.args.get('search_id')
    start = 0
    end = 5

    try:
        response = ariel.results(search_id, start, end)
    except ArielError as error:
        return { "Error": str(error) }
    except ValueError as error:
        return { "Range Error": str(error) }

    return json.dumps(response)
```

The `start` and `end` arguments to the `results()` function are optional. They determine the range of results that
are returned. If you provide an invalid range for start or end, a `ValueError` exception is thrown, along with an `ArielError`, 
which is thrown if the search fails.

## Creating the user interface


Before you create the user interface, you must change the default `hello()` route that the SDK generated. Replace this code:

```python
@viewsbp.route('/')
@viewsbp.route('/<name>')
def hello(name=None):
    qpylib.log('name={0}'.format(name), level='INFO')
    return render_template('hello.html', name=name)
```

with this code:

```python
@viewsbp.route('/index')
def index():
    return render_template('index.html')
```

Rename `templates/hello.html` to `templates/index.html`. The HTML code for the user interface goes into this directory.
The app looks for a route called `/index` when running (you can see this in the manifest by looking at the `url`
field).

Each of the endpoints returns JSON data. Your app needs a user interface that is shown in a
tab in QRadar. To keep things simple, each of the functions mentioned previously has its own text field and submit button. Users
type the query (or search ID if needed) into the text field and click submit to send the query. Below shows an
example text field and submit button for performing an asynchronous search.

```html
<div class="container">
<h3>Async Search</h3>
<div class="search_box">
  <input id='async_input' type='text' placeholder='Query'>
  <input type='submit' id='async_button' value='Submit' onclick='request("async_input", "async_search?query=")'>
</div>
<div class="output" id="response_async">
  <h4>Response</h4>
  <pre></pre>
</div>
</div>
```

Clicking submit calls a Javascript function named `request()`, which uses QJSLib to send a request to the `async_search`, 
routes that you created earlier. QJSLib is QRadar's Javascript library for making API calls. The library is
included in your app by default when you create your app using `qapp create`. The following code is the complete HTML code for the
user interface for `ArielApp`.

```html
<html>
<head>
    <title>Ariel Query App</title>
    <link rel="stylesheet" href="./static/styles.css">
</head>
<body>
    <h2>Ariel Query App</h2>
    <div class="container">
    <h3>Async Search</h3>
    <div class="search_box">
      <input id='async_input' type='text' placeholder='Query'>
      <input type='submit' id='async_button' value='Submit' onclick='request("async_input", "async_search?query=")'>
    </div>
    <div class="output" id="response_async">
      <h4>Response</h4>
      <pre></pre>
    </div>
    </div>
    <div class="container">
    <h3>Sync Search</h3>
    <div class="search_box">
      <input id='sync_input' type='text' placeholder='Query'>
      <input type='submit' id='sync_button' value='Submit' onclick='request("sync_input", "sync_search?query=")'>
    </div>
    <div class="output" id="response_sync">
      <h4>Response</h4>
      <pre></pre>
    </div>
    </div>
    <div class="container">
      <h3>Search Status</h3>
      <div class="search_box">
        <input id='status' type='text' placeholder='Search ID'>
        <input type='submit' id='status_button' value='Submit' onclick='request("status", "search_status?search_id=")'>
      </div>
      <div class="output" id="response_status">
        <h4>Response</h4>
        <pre></pre>
      </div>
      </div>
    <div class="container">
      <h3>Search Results</h3>
      <div class="search_box">
        <input id='results' type='text' placeholder='Search ID'>
        <input type='submit' id='results_button' value='Submit' onclick='request("results", "search_results?search_id=")'>
      </div>
      <div class="output" id="response_results">
        <h4>Response</h4>
        <pre></pre>
      </div>
      </div>
    <script type="text/javascript" src='./static/qjslib/qappfw.min.js'></script>
    <script type="text/javascript" src="./static/ariel.js"></script>
</body>
</html>
```

Notice two Javascript files are included, the first called `appfw.min.js` is QJSLib. The second file includes the
definition for the `request()` function mentioned above. The following code is the complete `ariel.js` file.

```javascript
const QRadar = window.qappfw.QRadar;
/**
 * HTTP GET request to Flask endpoints
 * @param {string} inputID - input to read for any parameter to attach
 * @param {string} endpoint - the Flask endpoint to send the request to
 */

function request(inputID, endpoint) {

  // Parameter is initially set as empty string
  var param = '';
  if (inputID) {
      // If there is an inputID provided
      // Extract the parameter from the input
      param = document.getElementById(inputID).value;
  }

  // Each search box has its own response box to show the output of each response
  let response_div = document.getElementById('response_' + inputID.split('_')[0]);
  response_div.style.display = 'block';
  // Insert loading text while waiting for the response
  response_div.children[1].innerText = 'Loading ...';

  // Use QRadar's fetch API to send requests, by using QRadar.fetch,
  // QRadar takes care of inserting the appropriate headers (such as CSRF) for you.
  QRadar.fetch(endpoint + param, { method: "get" })
  // If the request was successful use response.json() to retreive the body of the request as JSON
  .then(function(response) { return response.json() })
  // Update the response box with the output of the response
  .then(function(response) {
    response_div.children[1].innerText = JSON.stringify(response, null, 4)
  })
}
```

The request function uses the fetch API from QJSLib to send requests to the routes in the app. By using QRadar's fetch
function, QRadar's CSRF cookie is automatically added to each request so you don't have to think about it. The response
received is placed inside `response_div` and displayed on the page.

## Deploying To QRadar


Before an app can be deployed to QRadar, it must be packaged. Packaging an app produces a single file with
everything your app needs contained inside it. You can package your app using the `qapp package` command:

```bash
qapp package -p <your_app_name>
```

After you package your app, you can deploy using the `qapp deploy` command:

```bash
qapp deploy -p <your_app_name> -q <hostname_of_qradar_box> -u <qradar_username>
```

Wait a few seconds for the new tab with the name of your app to appear in QRadar.
