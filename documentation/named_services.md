# Named services

A named service is a feature that allows other apps and parts of the QRadar UI to interact with an app. These named
services can fit a variety of use cases.

**Examples:**

- A background process exposing HTTP endpoints to query, such as a NodeJS express server.
- A background process that works within an app container without exposing endpoints.
- Standard Flask endpoints grouped as a named service to allow them to be queried.

## Interacting with a named service

Named services can be called by using a URL to direct to the named service. There are two methods for building a URL
that will route a request to a named service.

### Directly using named service of an app

The following example builds a URL using app ID and named service name:

```bash
https://<_console_ip_>/console/plugins/app_proxy:<_app_id_>:<_named_service_name_>/<_endpoint_>
```

This explicitly calls the named service on a specific app. This requires prior lookup of the app ID.

This method can be used with QPyLib:

```python
from qpylib import qpylib

# These values could be fetched from somewhere
app_id = 1001
named_service = 'test_named_service'
endpoint = 'my_endpoint'

response = qpylib.REST('GET', '/console/plugins/app_proxy:{0}:{1}/{2}'.format(_app_id, named_service, endpoint_))
```

### Using QRadar to perform app lookup of named service

In the following example, a URL is built using only the named service name, letting QRadar look up an app that provides it:

```http
https://<_console_ip_>/console/plugins/app_proxy:<_named_service_name_>/<_endpoint_>
```

This example is implicitly fetching the app to query for a named service. QRadar handles this lookup and uses the first app
that provides a matching named service.

This method can be used with QPyLib:

```python
from qpylib import qpylib

named_service = 'test_named_service'
endpoint = 'my_endpoint'

response = qpylib.REST('GET', '/console/plugins/app_proxy:{0}/{1}'.format(named_service, endpoint))
```

## QRadar UI and named services

Named services can be called from different parts of the QRadar UI, allowing an app to be integrated across different
parts of QRadar.

### Areas

An area can be set up to use a named service to populate page data:

```json
"areas": [
    {
        "id": "AreaUsingNamedService",
        "text": "Area Using a Named Service",
        "description": "Area that uses 'custom_named_service' named service to populate itself",
        "url": "/custom_endpoint",
        "required_capabilities": [],
        "named_service": "custom_named_service"
    }
]
```

This example uses the `custom_named_service` named service to provide page data from the `/custom_endpoint` endpoint.

### REST methods

REST methods can configured to route to named services:

```json
"rest_methods": [
    {
        "name": "custom_endpoint",
        "url": "/custom_endpoint",
        "method": "GET",
        "named_service": "custom_named_service"
    }
]
```

This example uses the `custom_named_service` named service to provide data for a REST method under the `/custom_endpoint`
endpoint.

### Configuration pages

Configuration pages can be populated by using a named service:

```json
"configuration_pages": [
    {
        "text": "Config Page Using Named Service",
        "description": "Config Page that uses 'custom_named_service' named service to populate itself",
        "url": "/custom_endpoint",
        "required_capabilities": [],
        "named_service": "custom_named_service"
    }
]
```

This example uses the `custom_named_service` named service to provide page data from the `/custom_endpoint` endpoint.

### GUI actions

You can use named services with GUI actions in two ways. The first is to use the named service to load the GUI action
icon:

```json
"gui_actions": [
    {
        "id": "NamedServiceRightClickIP",
        "text": "Icon from named service",
        "description": "Test right click with icon loaded from named service",
        "icon": "static/images/icon_from_named_service.png",
        "javascript": "alert('Right clicked IP!')",
        "groups": [
            "ipPopup"
        ],
        "required_capabilities": [],
        "named_service": "custom_named_service"
    }
],
```

This example uses the `custom_named_service` named service to handle loading the `icon_from_named_service.png` icon for a menu option when the user right-clicks an IP.

You can also use a REST method alongside a GUI action, allowing a request to
be sent to the REST method when the action is done:

```json
"rest_methods": [
    {
        "name": "EndpointForRightClick",
        "url": "/endpoint_for_right_click_action",
        "method": "GET",
        "named_service": "custom_named_service"
    }
],
"gui_actions": [
    {
        "id": "NamedServiceRightClickIP",
        "text": "Trigger named service",
        "description": "Test right click that calls an endpoint behind a named service",
        "rest_method": "EndpointForRightClick",
        "javascript": "alert(result)",
        "groups": [
            "ipPopup"
        ],
        "required_capabilities": []
    }
]
```

This example sets up a REST method called `EndpointForRightClick` that uses the `custom_named_service` named service to provide
the `/endpoint_for_right_click_action` endpoint. A GUI action `NamedServiceRightClickIP` is defined that uses this
REST method, which is triggered when this right-click option is selected, with some JavaScript to print out the response
from the REST method endpoint.

### Page scripts

You can use a named service to load page scripts:

```json
"page_scripts": [
    {
        "app_name": "SEM",
        "page_id": "OffenseList",
        "scripts": [
            "/static/js/script_1.js",
            "/static/js/script_2.js"
        ],
        "named_service": "custom_named_service"
    }
]
```

This loads two scripts, `/static/js/script_1.js` and `/static/js/script_2.js`, from the named service
`custom_named_service` in the QRadar UI on the offenses page.

### Fragments

You can use a named service to populate Page scripts:

```json
"fragments": [
    {
        "app_name": "SEM",
        "page_id": "OffenseList",
        "location": "header",
        "rest_endpoint": "/custom_endpoint",
        "named_service": "custom_named_service"
    }
]
```

This example uses the `custom_named_service` named service to provide page data from the `index` endpoint for a fragment in
the QRadar UI on the header of the Offenses page.

### Custom columns

You can use a named service to populate custom columns:

```json
"custom_columns": [
    {
        "app_name": "SEM",
        "page_id": "OffenseList",
        "label": "Custom Column Using Named Service",
        "rest_endpoint": "/custom_endpoint",
        "named_service": "custom_named_service"
    }
]
```

This example uses the `custom_named_service` named service to provide page data from the `index` endpoint for a custom column
on the Offense list table in the QRadar UI.
