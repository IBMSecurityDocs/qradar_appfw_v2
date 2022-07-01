# Handling app certificates

You can design your app to package, load, and use custom certificates. 

Follow this procedure learn how to ensure that an app is able to correctly pick up certificates provided.

> **Note:** This method only installs a certificate for a single app. To install certificates on a QRadar
> instance and distribute them to all apps, the certificates must be imported through QRadar.

> **Tip:** If you have already added the certificate to the QRadar host and imported it into the trusted CA certificate
> bundle, you do not need to complete these steps.

## Prerequisites

- A QRadar app to add the custom certs to
- An SSL certificate to include in the app

## Add the custom certificate

Create a directory for the app certificates from the top-level directory of your app workspace:

```bash
mkdir store/certs
```

Copy the certificate into this directory:

```bash
cp <certificate> store/certs/<certificate>
```

The `store/certs` directory is designed specifically for storing custom app certificates. Since
this is in the `store/` directory, these certificates will persist across shutdowns, upgrades, and migrations. At
runtime, these certificates are copied to the `/opt/app-root/store/certs` directory.

## Write a startup script to process custom certificates

To process any custom certificates in the `/opt/app-root/store/certs` directory, the app needs to include a
startup script that calls a special script that is included in the app container to import any certificates in
`/opt/app-root/store/certs`.

Create a new file `container/run/import_certs.sh`:

```bash
as_root /opt/app-root/bin/update_ca_bundle.sh
```

This script runs at startup, and uses the `as_root` feature to run the special `update_ca_bundle.sh` script as an admin (sudo)
user. This script imports any certificates stored in `store/certs` and makes them available to the app.

To instruct QRadar to run this script as part of startup, you must include the `container/run/ordering.txt` file, which
tells QRadar which scripts to run as part of app startup. 

**Example:**

```txt
/opt/app-root/container/run/import_certs.sh
```

## Run and package the app

You can run the app locally by using the following command:

```bash
qapp run
```

Use the following commands to package and deploy the app:

```bash
qapp package -p <_app zip name_>

qapp deploy -p <_app zip name_> -q <_qradar console_> -q <_qradar user_>
```

## Adding a custom certificate to an app at runtime

Your app can support loading custom certificates at runtime, which allows users to upload their own certificates and the
app to load them without restarting.

1. Allow the user to upload a certificate file, save/copy this file to the `/opt/app-root/store/certs` directory.
2. From your code, run `sudo /opt/app-root/bin/update_ca_bundle.sh` to import the certificate.

**Python example:**

```python
import os

os.system('sudo /opt/app-root/bin/update_ca_bundle.sh')
```

The certificate is now imported into the app and can be used.

3. To automatically import the certs for this app in the future in the case of restarts, follow the process outlined
above in
'[Write a startup script to process custom certificates](#write-a-startup-script-to-process-custom-certificates)'.
