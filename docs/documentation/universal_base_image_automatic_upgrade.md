# Universal Base Image automatic upgrade

QRadar 7.5.0+ includes a process called `app_base_image_upgrade_thread` to automatically ensure that apps
that use the *Red Hat Universal Base Image* (UBI) are using the latest version available on the QRadar.

## Conditions

The `app_base_image_upgrade_thread` runs every time the hostcontext service is started in QRadar. This process will
ensure that any apps installed using the UBI are using the latest version; if it finds apps that are using an outdated
version of the image it will upgrade them to use the latest UBI.

The process will be skipped if any of the following conditions are true:

- A patch is in progress.
- An app migration is in progress.
- The App Framework APIs are disabled.
- The Conman service is unavailable.

The `app_base_image_upgrade_thread` will output the results of this operation to the QRadar log stating which apps
(if any) it upgrades.

## Upgrades

The `app_base_image_upgrade_thread` will upgrade any apps that match the following conditions:

- Using the UBI.
- The latest UBI version shares the same major version as the version that the app is currently built with
(e.g. `2.0.4` to `2.1.0` is valid, but `2.0.4` to `3.1.0` is not valid).

The following scenarios may help explain how these conditions would be applied.

### Do upgrade if UBI minor version change

App definition:

```json
{
    "image": "qradar-app-base:2.0.4",
    "manifest": {
        "image": "qradar-app-base:2.0.0"
    }
}
```

Available UBI versions:

- `2.1.0`

In this example the latest version is `2.1.0`, the app definition is currently on `2.0.4`, and the image targeted
by the manifest is `2.0.0`. The conditions it matches are:

✅ Uses the UBI (`qradar-app-base`).

✅ The latest UBI version shares the same major version as the version that the app is currently built with
(`2.0.4` to `2.1.0`).

As a result this app will be upgraded from UBI version `2.0.4` to `2.1.0`.

### Do not upgrade if app is a non-UBI app

App definition:

```json
{
    "image": "centos-base:6.9.10",
}
```

Available UBI versions:

- `2.1.0`

In this example it does not use the UBI, it uses the old `centos-base` image. The conditions it matches are:

❌ Uses the UBI (`centos-base`).

As a result this app will not be upgraded.

### Do not upgrade if UBI major version change

App definition:

```json
{
    "image": "qradar-app-base:2.0.4",
    "manifest": {
        "image": "qradar-app-base:2.0.0"
    }
}
```

Available UBI versions:

- `3.1.0`
- `2.0.4`

In this example the latest version is `3.1.0`, the app definition is currently on `2.0.4`, and the image targeted
by the manifest is `2.0.0`. The conditions it matches are:

✅ Uses the UBI (`qradar-app-base`).

❌ The latest UBI version shares the same major version as the version that the app is currently built with
(`2.0.4` to `3.1.0`).

As a result this app will not be upgraded.
