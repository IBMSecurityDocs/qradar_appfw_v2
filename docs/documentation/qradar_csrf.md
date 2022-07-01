# QRadar CSRF

QRadar 7.3.3 Fix Pack 9 and later allows apps to use QRadar's CSRF validation to protect against CSRF vulnerabilities.
This simplifies protecting app endpoints from CSRF attacks, with QRadar apps not having to provide their own protection
against these attacks.

## Configuring QRadar app CSRF protection

Apps can enable CSRF protection by setting the `use_qradar_csrf` flag in their app manifest files:

```json
{
    ...
    "use_qradar_csrf": "true"
}
```

The `use_qradar_csrf` means that any `POST/PUT/DELETE/PATCH` request to the app that is not from an authorized service
or from an internal QRadar process will require a valid CSRF token provided as a header in the request, if an invalid
token or no token is provided the request will fail with a `403 Forbidden` error.

The CSRF token should be included in the header of the request with the key `QRadarCSRF`. Use the [QJSLib JavaScript
library](../tutorials/qjslib_javascript_library.md) to handle populating this header automatically. If you are not
using QJSLib you can manually access the QRadar CSRF token by reading the `QRadarCSRF` cookie in the browser.

## CSRF protection process

1. The user loads a page in the QRadar user interface with the app on it with a `GET|HEAD|OPTIONS|TRACE` request, such
as an area.
2. This request generates a cookie which is stored in the browser called `QRadarCSRF` which can be used to validate
HTTP requests if provided as a header in the request.
3. The user performs an action that uses a `POST|PUT|DELETE|PATCH` request, the CSRF token is added as a header to this
request.
4. QRadar handles the request, if the CSRF token is provided and valid then the request will be forwarded to the app's
endpoints, if it is not provided or invalid a `403 Forbidden` error will be returned.

## Limitations

- QRadar CSRF is applied across every endpoint in the app, it cannot be selectively applied per endpoint.
- CSRF tokens must be provided in the header of the request, this is the only supported method of providing a CSRF
token. This means that HTML forms are not supported by QRadar CSRF, since HTML forms cannot set headers, instead
JavaScript should be used to make the request.
- When using any QRadar GUI actions with an app, make sure they are using `GET|HEAD|OPTIONS|TRACE`, if they use
`POST|PUT|DELETE|PATCH` they will fail as they will not be provided with the QRadar CSRF token.

See '[Using QRadar CSRF](../tutorials/using_qradar_csrf.md)' to see how to use QRadar CSRF with a QRadar app.
