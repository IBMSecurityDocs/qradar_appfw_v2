# App upgrades

Upgrade your app by using IBM® QRadar® Extensions Management, the App Assistant, or via `qapp deploy` using the
[QRadar App SDK](./qradar_app_sdk_overview.md).

> **Tip:** As a best practice, store your app configuration and data in `/opt/app-root/store` because data in this
> directory is protected during app upgrades.


> **Important:** If you want to upgrade an installed app that uses a lot of memory, you might need to stop the app before you start the
> upgrade to avoid memory resource issues. Then restart the app after you complete the upgrade.
