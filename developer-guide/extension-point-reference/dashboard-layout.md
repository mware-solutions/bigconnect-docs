---
description: Change dashboard widget layout
---

# Dashboard Layout

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-layout)

Specifies the dashboard items, their sizes, and positions as the default dashboard configuration. Currently, only the first extension registered will be used. If no extension is registered, the system uses the default template.

All dashboard cards are placed in a grid system. The default grid is `12` columns and unbounded rows. If `metrics` is not defined or overlaps a previous definition, then the layout system will place the card automatically.

{% hint style="warning" %}
The console will show a warning if multiple extensions are found. The extension used is non-deterministic.
{% endhint %}

## Web Plugin

Register the plugin in a web plugin.

```javascript
app.registerJavaScript("/com/mware/examples/dashboard_layout/plugin.js", true);
```

[DashboardLayoutWebAppPlugin.java \(line 16\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-layout/src/main/java/com/mware/examples/dashboard_layout/DashboardLayoutWebAppPlugin.java#L16)

## Register the Layout

A layout is just the extension and initial configuration metrics for the items.

```javascript
bc.registry.registerExtension('org.bigconnect.web.dashboard.layout', [
    {
        extensionId: 'com.mware-examples-dashboard-item-react',
        configuration: { metrics: { x: 5, y: 0, width: 4, height: 3 } }
    },
    {
        extensionId: "com.mware-web-notifications",
        configuration: { metrics :{ x: 9, y: 0, width: 3, height: 3 } }
    }
]);
```

[plugin.js \(lines 3–12\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-layout/src/main/resources/com/mware/examples/dashboard_layout/plugin.js#L3-L12)

