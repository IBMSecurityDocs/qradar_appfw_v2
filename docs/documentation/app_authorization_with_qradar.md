# App authorization with QRadar

Apps use authorization service tokens to authorize access to QRadar® resources.

Configure authorization parameters in the authentication section of the manifest file. The only mandatory entry is `requested_capabilities`. When an application with this authorization parameter is installed via extension
management, the app is be created until authorization is completed through the Application Assistant App.

The following example shows the authentication section in the manifest file:

```json
"authentication": {
  "oauth2": {
    "authorisation_flow": "CLIENT_CREDENTIALS",
    "requested_capabilities": [
      "SEM"
    ]
  }
}
```

The `authorisation_flow` entry is optional. The only accepted value is `CLIENT_CREDENTIALS`.

If the authorization is not configured as `CLIENT_CREDENTIALS`, the installation fails and returns the following
message:

```txt
OAuth flow type X is not currently supported
```

The `requested_capabilities` entry must contain at least one entry. It provides the capability or permissions that the app
needs to function in QRadar. The app installation fails if the capability that is configured is
not listed in QRadar.

The user navigates to the Application Assistant app and selects a user with the capabilities requested by the
app. For example, a user with the SEM capability from the example above.

When you select the authorization, the instance is created, along with an authorized service token that matches the app
instance id and the selected user's role. The app then has access to that authorized service token for making
QRadar resource requests.

> **Note:** The user must deploy changes to enable the authorized service token.
