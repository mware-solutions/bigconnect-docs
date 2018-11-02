---
description: Add custom widget config options
---

# Dashboard Toolbar Item

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-toolbaritem)

Allows custom buttons to be rendered next to the cards configuration button. These buttons \(displayed as icons\) can send an event on click, or specify content to be rendered in a popover.

![](http://localhost/extension-points/front-end/dashboard/toolbar.png)

## Web Plugin

Register resources for the plugin, the component, and the toolbar icon.

```java
app.registerJavaScript("/com/mware/examples/dashboard_toolbaritem/plugin.js", true);
app.registerJavaScript("/com/mware/examples/dashboard_toolbaritem/popover.js", false);
app.registerResourceBundle("/com/mware/examples/dashboard_toolbaritem/messages.properties");
app.registerFile("/com/mware/examples/dashboard_toolbaritem/trash.png", "image/png");
```

[DashboardToolbaritemWebAppPlugin.java \(lines 16–19\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-toolbaritem/src/main/java/com/mware/examples/dashboard_toolbaritem/DashboardToolbaritemWebAppPlugin.java#L16-L19)

## Register Extension

Register the toolbar extension, specifying the component that will be rendered in a popover on click, and icon.

```javascript
bc.registry.registerExtension('org.bigconnect.dashboard.toolbar.item', {
    identifier: 'com.mware-examples-dashboard-toolbar',
    canHandle: function(options) {
        return options.extension.identifier === 'com.mware-web-notifications'
    },
    tooltip: 'My Example Action',
    icon: '/com/mware/examples/dashboard_toolbaritem/trash.png',
    action: {
        type: 'popover',
        componentPath: 'com/mware/examples/dashboard_toolbaritem/popover'
    }
});
```

[plugin.js \(lines 3–14\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-toolbaritem/src/main/resources/com/mware/examples/dashboard_toolbaritem/plugin.js#L3-L14)

## Create the Popover Component

The popover component must be flight component, this one just renders some text. The dashboard handles the creation of the popover and simply renders this component in the content area.

```javascript
function ExampleToolbarPopover() {
    this.after('initialize', function() {
        this.$node.text('Example Popover');
    });
}
```

[popover.js \(lines 8–14\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-toolbaritem/src/main/resources/com/mware/examples/dashboard_toolbaritem/popover.js#L8-L14)

If content is updated in the popover and the size changes, the component should trigger an event to notify the popover parent component. This will adjust the anchor point to match the size change.

```javascript
this.trigger('positionDialog');
```

