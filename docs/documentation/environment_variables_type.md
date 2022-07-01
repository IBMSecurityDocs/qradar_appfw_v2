# Environment variables type

Use the environment variables object type to define environment variables in your app's manifest file.

The environment variables object is not an IBM® QRadar® application type. The following environment variables are set
automatically by QRadar and are available to use in your app.

<table>
    <thead>
        <tr>
            <th>
                <div>Name</div>
            </th>
            <th>
                <div>Description</div>
            </th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>APP_GROUP_ID</td>
            <td>The group id for appuser. In App framework v2, processes in the container no longer run as root. Instead,
                they run as a new user called appuser.</td>
        </tr>
        <tr>
            <td>APP_ROOT</td>
            <td>The app root directory. In App framework v2 this is `/opt/app-root/app`.</td>
        </tr>
        <tr>
            <td>APP_USER_ID</td>
            <td>The user id for appuser.</td>
        </tr>
        <tr>
            <td>LANG</td>
            <td>The language code and character encoding that is used by the QRadar Console UI when the app is
                installed.</td>
        </tr>
        <tr>
            <td>
                <p>LOG_COLLECTOR_RATE_LIMIT</p>
            </td>
            <td>The log collector rate limit.</td>
        </tr>
        <tr>
            <td>QRADAR_APPHOST_EXISTS</td>
            <td>This variable is set to true if there is an apphost in the deployment. Otherwise it is set to false.</td>
        </tr>
        <tr>
            <td>QRADAR_APPLICATION_BASE_URL</td>
            <td>The web URL for the app.</td>
        </tr>
        <tr>
            <td>QRADAR_APP_ID</td>
            <td>The app instance id.</td>
        </tr>
        <tr>
            <td>QRADAR_APP_RUNNING_ON_APPHOST</td>
            <td>This variable is set to true if the app is running on an apphost. Otherwise it is set to false.</td>
        </tr>
        <tr>
            <td>QRADAR_APP_UUID</td>
            <td>The app definition UUID.</td>
        </tr>
        <tr>
            <td>QRADAR_BUILD_VERSION</td>
            <td>The qradar build version.</td>
        </tr>
        <tr>
            <td>QRADAR_CONSOLE_FQDN</td>
            <td>The console FQDN.</td>
        </tr>
        <tr>
            <td>QRADAR_CONSOLE_HOST_NAME</td>
            <td>The host name of the QRadar console.</td>
        </tr>
        <tr>
            <td>QRADAR_CONSOLE_IP</td>
            <td>The IP address of the QRadar console.</td>
        </tr>
        <tr>
            <td>QRADAR_HTTP_PROXY</td>
            <td>Http proxy details read from autoupdate settings in QRadar. 
            </br>**Note:** This is only set if
                there is an autoupdate setting present. This is then used in the app code.</td>
        </tr>
        <tr>
            <td>QRADAR_HTTPS_PROXY</td>
            <td>Https proxy details read from autoupdate settings in QRadar. 
            </br>**Note:** This is only set if
                there is an autoupdate setting present. This is then used in the app code.</td>
        </tr>
        <tr>
            <td>QRADAR_NO_PROXY</td>
            <td>No proxy details with entries for the local server. 
            </br>**Note:** This is only set if
                there is an autoupdate setting present. This is then used in the app code.</td>
        </tr>
        <tr>
            <td>QRADAR_VERSION</td>
            <td>The QRadar version.</td>
        </tr>
        <tr>
            <td>REQUESTS_CA_BUNDLE</td>
            <td>The path the CA cert bundle.</td>
        </tr>
        <tr>
            <td>
                <p>TZ</p>
            </td>
            <td>The timezone from the QRadar console.</td>
        </tr>
        <tr>
            <td>http_proxy</td>
            <td>Http proxy details read from autoupdate settings in QRadar. 
            </br>**Note:** This is only set if
                there is an autoupdate setting present. This is then used in the app code.</td>
        </tr>
        <tr>
            <td>https_proxy</td>
            <td>Https proxy details read from autoupdate settings in QRadar. 
            </br>**Note:** This is only set if
                there is an autoupdate setting present. This is then used in the app code.</td>
        </tr>
        <tr>
            <td>no_proxy</td>
            <td>No proxy details read from autoupdate settings in QRadar. 
            </br>**Note:** This is only set if
                there is an autoupdate setting present. This is then used in the app code.</td>
        </tr>
    </tbody>
</table>

Environment variables that you define in an app's manifest file are specific to that app and have no effect outside the app container.

The following code sample shows how to configure the `environment_variables` block in the manifest file.

```json
{
...
"environment_variables": [
		{
            "name": "ENV_VAR1",
            "value": "1"
		},
		{
            "name": "ENV_VAR2",
            "value": "2"
		},
		{
            "name": "ENV_VAR3",
            "value": "3"
		}
	],
...
}
```

The following table explains the manifest fields for the `environment_variables` block.

<table>
    <thead>
        <tr>
            <th>Field</th>
            <th>Type</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>name</td>
            <td>string</td>
            <td>The name of the environment variable. </br>
                Environment variable names cannot begin with a number, or contain an equals (=) sign.</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>value</td>
            <td>string</td>
            <td>The value of the environment variable.</td>
            <td>Yes</td>
        </tr>
    </tbody>
</table>
