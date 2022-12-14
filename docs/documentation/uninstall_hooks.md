# Uninstall hooks

QRadar 7.5.0+ includes uninstall hooks. An uninstall hook is a feature that allows an app to call a REST method as part
of the app uninstall process. These uninstall hooks can fit a variety of use cases.

**Examples:**

- Disabling a shared external resource that an app utilized
- Deleting data held externally from the app, such as QRadar reference data

> The app uninstall hooks should be used specifically for interacting with resources external to the app, if cleaning
> up data that is stored in an app consider using the [container cleanup script
> functionality](./new_features_appfw_v2.md#container-cleanup-script).

## Configuring an uninstall hook

An uninstall hook can be configured by adding an `uninstall_hook` to an app's `manifest.json` file. The uninstall hook
uses a `rest_method` to run the uninstall logic.

```json
"uninstall_hooks": [
  {
    "description": "Uninstall hook for my app",
    "rest_method": "uninstall_hook_rest_method",
    "documentation_url": "https://www.ibm.com/",
    "last_instance_only": "true"
  }
],
"rest_methods": [
  {
    "name": "uninstall_hook_rest_method",
    "url": "/uninstall_hook",
    "method": "POST"
  }
]
```

An uninstall hook can be configured with the following options:

- `description` - A description of the uninstall hook
- `rest_method` - The REST method to call when the uninstall hook is executed
- `documentation_url` - An optional link to the documentation to manually perform the uninstall if the hook has failed,
exposed to the user in an error message on hook failure
- `last_instance_only` - An optional flag, if set to `true` then the uninstall hook will only be run when the last
instance of an app is uninstalled, this can be useful for multi-tenanted apps

**Note**: the uninstall hook endpoint specified under rest_methods in the manifest json must:

- Return a success HTTP response code between 200->299 (inclusive).
- Return a valid JSON response, can be anything, but must be valid JSON, e.g. {} would be fine.

## Lifecycle of an uninstall hook

The lifecycle of the app uninstall hook is the following:

1. A request is made to delete an app definition.
2. The request is received and the app is placed into the `DELETING` state.
3. The uninstall hooks are executed if the deletion was by the ConfigServices user, otherwise the uninstall hooks are
skipped.
4. The app definition deletion continues as normal.

## Uninstall hook capabilities

An uninstall hook can only be ran by the ConfigServices user, if any other user attempts to run an uninstall hook (for
example by requesting an app uninstall) then the app will skip the uninstall hooks and include a warning in the JSON
response from the app uninstall API. The warning looks like this:

```txt
An error occurred while deleting app instance 1102. App uninstall hooks failed: Uninstall hook for my app. See server
logs for more details.
```

Apps that are uninstalled through QRadar extension management will be uninstalled using the ConfigServices user.
