# QRadar app best practices

This page outlines best practices for developing QRadar apps. The guidelines are designed to help make sure an app will
continue to work across different versions of QRadar with updates to the `qradar-app-base` image, and to match rules
for submitting an extension to the IBM X-Force App Exchange.

## Libraries

- Python apps should use the provided [QPylib library](https://github.com/ibm/qpylib) to make QRadar API calls.
- Apps that use JavaScript (either front-end or back-end using NodeJS) should use the [QJSlib
library](https://github.com/ibm/qjslib) to make REST calls.

## Dependencies

- Dependencies must be bundled with the app and must not be fetched over the internet at install time.
- Avoid installing over the packages that are included with the base image, unless the app strictly requires a different package
version. Use the latest base image's package list as a reference of packages to avoid installing over.
- Avoid installing dependencies that tie to a specific Python version. For example packages that specifically use a
certain CPython version, instead try to find a Red Hat package equivalent.
- Dependencies must be built into the image (using `/container/pip` and `/container/rpm`) and not installed during
container startup.
- Do not use a `/container/pip/requirements.txt` file to install dependencies, as this will attempt use the internet to
install the dependencies (the support for installing dependencies using a `requirements.txt` file is currently
supported, but will be removed in later QRadar releases).
- Use Red Hat Python packages where possible to help ensure compatibility with the `qradar-app-base` and any future
versions. Red Hat will provide backported security fixes which makes these packages more stable than PyPi versions.

## Start up

- Use the `as_root` feature to run commands as the root user at startup carefully and only when strictly
neccessary.
- App startup should be fast. Run configuration and scripts at build time (using the `container/build`
directory) where possible, instead of at app start (`container/run`).

## Data

- Any persistent data must be stored in the `/opt/app-root/store` directory in the app container. This directory
is managed by QRadar and is persisted when an app is started, stopped, or updated.
- Apps must be able to handle migrating data for app upgrades. For example, for a database schema migration that adds
a new column to an existing table, consider using a database migration tool, such as
[FlywayDB](https://github.com/flyway/flyway), to handle this.
- App migrations must be able to handle upgrade failures. If the upgrade fails, the data must be able to be rolled
back to a working state.
- Secret data should be stored securely using the [QPylib](https://github.com/ibm/qpylib) encdec functionality. See
the [Secure data storage and encryption](./secure_data_storage_and_encryption.md) for more information.

## Sacrosanct files

- There are some files in the QRadar app container that must not be overwritten. These are *sacrosanct* files. See [Sacrosanct files](./sacrosanct_files.md) for more information.

## Cross site request forgery

- Apps must protect against cross site request forgery (CSRF). See [Cross site request forgery](./cross_site_request_forgery.md) for more information.

## Testing

- You must test apps against the latest patch of every major release, which should include the latest released
`qradar-app-base` image version.

## Security

- Ensure that any admin or restricted access functionality is correctly secured in the app's `manifest.json` file by making use
of the `required_capabilities` field. Be careful to not expose any admin or restricted access functionality with public
access.
- Any part of the app that prompts a user to add or input credentials should be restricted to admin only access.

## Versioning

- Apps must specify a minimum supported QRadar version, and they should specify and use the
latest QRadar API version available on that version. You can see the [full list of which QRadar versions support which
API versions here](https://www.ibm.com/docs/en/qradar-common?topic=api-restful-overview).
- Apps must specify the QRadar API version they are making requests to. You can do this by providing the `Version`
header with any HTTP requests. If you are using the `qpylib` library, you can do this by providing the `version` parameter to
the `REST` function.
