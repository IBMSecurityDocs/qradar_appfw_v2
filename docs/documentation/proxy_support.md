# Proxy support

QRadar automatically exposes configured proxy information to apps in the form of environment variables injected at
runtime. This allows apps to support any proxy that QRadar is configured with.

## Proxy values from environment variables

QRadar injects a number of environment variables into apps to expose proxy information. Libraries such as Python
requests and QPyLib automatically pick up and use the following variables:

- `https_proxy`
- `http_proxy`
- `no_proxy`

QRadar also injects replicated equivalents that are designed to be manually retrieved and used as proxy variables:

- `QRADAR_HTTPS_PROXY`
- `QRADAR_HTTP_PROXY`
- `QRADAR_NO_PROXY`

> **Note:** Manual setup pf the proxy for a Python app is not necessary. QRadar provides the `https_proxy`,
> `http_proxy`, and `no_proxy` environment variables, which are automatically picked up by Python libraries such as
> requests and QPyLib. If you use another language/framework, check for built in support of these variables. If your framework
> does not support these variables, you can use the manual proxy values.

### Configuration with nva.conf

The setting `APP_PROXY_ENABLED` in the `nva.conf` file determines if the following variables are injected:

- `https_proxy`
- `http_proxy`
- `no_proxy`

If `APP_PROXY_ENABLED=true` or `APP_PROXY_ENABLED` is not explicitly set, the variables are injected. If
`APP_PROXY_ENABLED=false`, they are not injected.

The setting `APP_PROXY_ENV_VARIABLES_ENABLED` in the `nva.conf` file determines whether the following are injected:

- `QRADAR_HTTPS_PROXY`
- `QRADAR_HTTP_PROXY`
- `QRADAR_NO_PROXY`

If `APP_PROXY_ENV_VARIABLES_ENABLED=true` or `APP_PROXY_ENV_VARIABLES_ENABLED` is not explicitly set, the variables are
injected. If `APP_PROXY_ENV_VARIABLES_ENABLED=false`, they are not injected.
