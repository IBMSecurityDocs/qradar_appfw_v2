# Configuring apps running through the SDK to make API calls

To communicate with QRadar, an app needs information about the QRadar console it is communicating with and a token
to authenticate with. Your locally running app can receive this information as environment variables by configuring
the `qenv.ini` file provided in the SDK.

Three environment variables are required to make API calls to QRadar:

- `QRADAR_CONSOLE_FQDN` = The fully qualified domain name of the QRadar console.
- `QRADAR_CONSOLE_IP` = The IP address of the QRadar console.
- `SEC_ADMIN_TOKEN` = A token that allows the app to authenticate with QRadar.

## Procedure

1. Log into the QRadar console.
2. On the **Admin** tab, click **Authorized Services**.
4. Click **Add Authorized Service**.
5. Name the token, grant it an appropriate **User Role** and **Security Profile**, and configure the **Expiry Date**.
6. Click **Create Service**.
7. Select the service in the table, and copy the token.

Open the `qenv.ini` file and ensure that the properties below are set:

```ini
[qradar]
QRADAR_CONSOLE_FQDN=<FQDN of the QRadar box for the app to query>
QRADAR_CONSOLE_IP=<IP address of the QRadar box for the app to query>

[proxy]
QRADAR_REST_PROXY=<If using a proxy, set this value to the proxy address>

[app]
SEC_ADMIN_TOKEN=<generated authorized service token for the app to use when querying QRadar>
```

After these values are set, and if the app uses QPyLib for HTTP requests, the app running through the SDK should be able
to interact with the QRadar API. Try running the app with the following command:

```bash
qapp run
```
