# App file structure

An IBM® QRadar® app that you create is distributed within a compressed file.

The Hello World sample app that is created when you set up your development environment is a basic template that you
can use for your application. However, the application file structure can be more complex.

The following list outlines the layout of files and sub directories that you can add to the root directory of your app.
It also outlines the required nomenclature for app files and sub directories:

- App Root Folder
    - `app/` - Main directory for application files.
    - `app/__init__.py` - Main entry point into the web app.
    - `app/views.py` - File that contains view functions that respond to requests to your application.
    - `app/templates` - Optional subdirectory that contains any Python, Flask, or Jinja templates that are required by
    the app.
    - `app/static` - Optional subdirectory that contains CSS, JavaScript, globalization, and other resource files
        - `app/static/css` - Cascading style sheets used by the app.
        - `app/static/js` - Javascript files used by the app.
        - `app/static/resources` - Resource bundle properties files used by the app. Text strings for globalization are
        stored as key/value pairs in Java format properties files. If you configured text strings for globalization,
        they appear in QRadar when the user sets their preferences for the relevant locale. 
        </b>**Note:** If no matching resource bundle properties file is found, the app defaults to the English translation if present.
    - `container/` - Optional directory that can be added with any of the following subdirectories:
        - `container/pip` - This contains any extra Python libraries that the app requires. See [Source dependencies](./source_dependencies.md) for more details.
        - `container/rpm` - This contains any RPM dependencies that the app requires. RPMs must be RHEL 8 x86_64
        compatible. See [Source dependencies](./source_dependencies.md) for more details.
        - `container/build` - This contains any dependencies that the app requires that are not RPMs or Python
        libraries. See [Build and Runtime dependencies](./build_and_runtime_dependencies.md) for more details.
        - `container/run` - This contains any dependencies that the app requires that are not RPMs or Python libraries.
        See [Build and Runtime dependencies](./build_and_runtime_dependencies.md) for more details.
        - `container/clean` - This contains the `cleanup.sh` script a shell script that runs during container shut
        down. This shell script can be used to shut down app processes gracefully (e.g., database connections).
        - `container/service` - This contains scripts that start background processes used in the app container.
        - `container/conf` - This contains configuration files for any services and general use.
        - `container/conf/supervisord.d` - This contains supervisord include files. The contents of this directory are 
        copied to `/etc/supervisord.d/` and are included as a part of the supervisord configuration at container
        startup.
    - `manifest.json` - The application manifest description file. See Application manifest structure for more details

[**Application manifest structure**](./application_manifest_structure.md)

The manifest is a JSON file that describes to QRadar the capabilities that the app provides.

[**Source dependencies**](./source_dependencies.md)

If your app requires dependencies, such as RPMs or Python libraries

[**Build and runtime dependencies**](./build_and_runtime_dependencies.md)

If your app requires additional configuration dependencies at image build time or at container startup
