# Inherit user-selected global theme for applications

Starting with QRadar UP (UpdatePackage) 12, new parameters are available that allow applications to inherit the user-selected theme across your applications.

## Key Content

From QRadar UP (UpdatePackage) 12, the parameters of the generic user-selected theme are automatically embedded into the first URL of every QRadar application. This eliminates inconsistencies and fragmented design in theme implementation across all applications.  This means that every individual application can seamlessly adopt these parameters and implement the theme, ensuring consistency throughout the entire platform.

The key parameters provided are:
- **ui** – Indicates the UI type in use.
- **theme** – Reflects the current theme selected in QRadar

For example: `?ui=new&theme=dark`

These values are available as query parameters in the request and can be accessed by using the first URL of the application. For example, when a user launches an application and user selected theme of QRadar machine is dark, then the first URL of the application contains the following parameters:
```text
http://localhost:<_app port_>/index.html?ui=new&theme=dark
```

Applications must extract these parameters and apply them consistently to their UI components. By parsing the query string, developers can identify the active theme and dynamically adjust styles, CSS classes, or component rendering to match the selected theme. This ensures that the QRadar theme is uniformly applied across applications, providing a unified user experience for end users.

Starting with QRadar UP (UpdatePackage) 12, all UI applications receive these parameters. From QRadar UP (UpdatePackage) 13, this theme implementation capability is extended to applications that are accessible from the **QRadar -> Admin page**. Many QRadar apps already adhere to a standard for theme implementation by using these query parameters to toggle between themes within the app. This same approach should be adopted for all future apps as well.

These parameters ensure that the QRadar theme mechanism is consistently available to all applications. Developers are expected to identify these parameters and apply them to ensure a consistent and unified user experience across the platform.
