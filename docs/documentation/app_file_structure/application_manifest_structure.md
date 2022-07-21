# Application manifest structure

The manifest is a JSON file that describes the capabilities that the app provides to IBM® QRadar®.

The following table describes the fields that you can include in the `manifest.json` file.

*Table 1. Application manifest fields*

<table>
    <thead>
        <tr>
            <th>Field</th>
            <th>Required</th>
            <th>Type</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>name</td>
            <td>Yes</td>
            <td>String</td>
            <td>The user-readable name of the app. If the app is globalized, this field can optionally point at a
                resource bundle key.</td>
        </tr>
        <tr>
            <td>description</td>
            <td>Yes</td>
            <td>String</td>
            <td>The user-readable description of the app. If the application is globalized, this field can optionally
                point at a resource bundle.</td>
        </tr>
        <tr>
            <td>version</td>
            <td>Yes</td>
            <td>String</td>
            <td>A version string for the app. We recommend you use the following format: `x.x.x`. 
            </br>**Example:** `1.0.0`</td>
        </tr>
        <tr>
            <td>uuid</td>
            <td>Yes</td>
            <td>String</td>
            <td>An RFC 4122-compliant universally unique identifier for the application.
                </br>
                The create command uses the Python UUID package to generate a random 128-bit number for the uuid value.
                </br>
                **Note:** If you do not use the SDK to create the app manifest file, you must manually enter a unique value in the uuid
                field.</td>
        </tr>
        <tr>
            <td>image</td>
            <td>No</td>
            <td>String</td>
            <td>The base image name to use when building your application. If none is specified, the default base image is used</td>
        </tr>
        <tr>
            <td>authentication</td>
            <td>No</td>
            <td>String</td>
            <td>Authorization for the app to access QRadar. The only mandatory entry is “requested_capabilities”:
                [&quot;&quot;].
                </br>
                For example, admin is a commonly used user capability. Enter at least one
                supported QRadar user capability. The installation fails if any of the requested_capabilities are not
                defined in QRadar.</td>
        </tr>
        <tr>
            <td>load_flask</td>
            <td>No</td>
            <td>Boolean</td>
            <td>Set to false if you don’t want to make Python Flask framework available to your app. 
            **Tip:** Disable Flask if you want your app to use a different web application framework.
                </br>
                If not specified, this field defaults to true.</td>
        </tr>
        <tr>
            <td>areas</td>
            <td>No</td>
            <td>Array of Area Type</td>
            <td>Area objects describe new complete pages of the application. In QRadar, Area objects are represented as
                tabs.</td>
        </tr>
        <tr>
            <td>rest_methods</td>
            <td>No</td>
            <td>Array of REST Method Type</td>
            <td>REST Method objects describe REST methods that the app exposes. REST Method objects are required
                parameters for Dashboard Items and Metadata Providers, and are optional for GUI Actions.</td>
        </tr>
        <tr>
            <td>dashboard_items</td>
            <td>No</td>
            <td>Array of Dashboard Item Type</td>
            <td>Dashboard Item objects describe the contents of new items that you want to expose to the QRadar
                dashboard.</td>
        </tr>
        <tr>
            <td>configuration_pages</td>
            <td>No</td>
            <td>Array of Configuration Page Type</td>
            <td>Configuration Page objects describe new complete pages of the app that represent configuration. In
                QRadar, configuration pages are opened from the Admin tab.</td>
        </tr>
        <tr>
            <td>gui_actions</td>
            <td>No</td>
            <td>Array of GUI Action Type</td>
            <td>GUI Action objects describe new actions that can be performed on items in the user interface by page
                toolbars or by right-click menus.</td>
        </tr>
        <tr>
            <td>page_scripts</td>
            <td>No</td>
            <td>Array of Page Script type</td>
            <td>Page Script objects describe new JavaScript files that you want to include within an existing page in
                QRadar. By default, these scripts run in their own namespace.</td>
        </tr>
        <tr>
            <td>metadata_providers</td>
            <td>No</td>
            <td>Array of Metadata Provider type</td>
            <td>Metadata Provider objects describe REST methods that can be called to fetch new metadata information for
                certain data types in QRadar 
                </br>**Example:** IP
                </br>Metadata is displayed in a tooltip when the user hovers over an
                item.</td>
        </tr>
        <tr>
            <td>resource_bundles</td>
            <td>No</td>
            <td>Array of Resource Bundle type</td>
            <td>Resource Bundle objects are used for language locales and locale properties file locations.</td>
        </tr>
        <tr>
            <td>resources</td>
            <td>No</td>
            <td>Integer</td>
            <td>Resource objects are used to configure the amount of memory (in megabytes) that is available for the app
                to use.</td>
        </tr>
        <tr>
            <td>fragments</td>
            <td>No</td>
            <td>Array of Fragments type</td>
            <td>Fragment objects are used to determine the injection point in the QRadar UI where content is added and
                the rest endpoint that is used to retrieve the content.</td>
        </tr>
        <tr>
            <td>custom_columns</td>
            <td>No</td>
            <td>Array of Custom Columns type</td>
            <td>Custom column objects are used to identify the context (the page and table in the QRadar UI) where a
                custom column is added, a label for the column header, the type of data to be added, and the rest
                endpoint that is used to add the column content.</td>
        </tr>
        <tr>
            <td>multitenancy_safe</td>
            <td>No</td>
            <td>Boolean</td>
            <td>If set to true, this key indicates that your application can work in a multi-tenant environment. If not
                set to true, it indicates that only one instance of your app can be created, and any user who is a member
                of a tenant is not able to see it.
                </br>
                If not specified, the default is false.</td>
        </tr>
        <tr>
            <td>single_instance_only</td>
            <td>No</td>
            <td>Boolean</td>
            <td>If set to true, only one instance of this app can be created. Typically, this indicates that this
                application can either provide multi-tenancy support itself, or that use of this application is meant
                only for administrators. If multitenancy_safe is also not set true, then users who are a member of a
                tenant cannot see this app. If multitenancy_safe is also set to true, then all users can see the
                app.
                </br>
                If not specified, the default is false.</td>
        </tr>
        <tr>
            <td>use_qradar_csrf</td>
            <td>No</td>
            <td>Boolean</td>
            <td>If set to true, csrf support is handled by QRadar for the app. 
            </br>If not specified, the default is false.
            </td>
        </tr>
        <tr>
            <td>environment_variables</td>
            <td>No</td>
            <td>Array of Environment Variable type</td>
            <td>Environment variable objects are used to specify environment variables that will be available within the
                container during runtime</td>
        </tr>
        <tr>
            <td>services</td>
            <td>No</td>
            <td>Array of Service type</td>
            <td>Service objects are used to define named services, service endpoints, and supervisord configuration
                parameters.</td>
        </tr>
    </tbody>
</table>
