---
description: Add a section to the admin page
---

# Admin

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-admin)

Admin extensions allow sections to be added in the admin pane that when clicked, open a custom component. This tutorial registers three admin extensions that show React, Flight, and an admin extension that opens a link.

![](../../../../.gitbook/assets/image%20%2833%29.png)

## Create a web plugin

First, create the web plugin that registers the resources.

```java
app.registerJavaScript("/com/mware/examples/admin/plugin.js");

app.registerJavaScriptComponent("/com/mware/examples/admin/React.jsx");
app.registerJavaScript("/com/mware/examples/admin/flight.js", false);
app.registerJavaScriptTemplate("/com/mware/examples/admin/flight-template.hbs");

app.registerResourceBundle("/com/mware/examples/admin/messages.properties");
```

[AdminWebAppPlugin.java \(lines 17–23\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-admin/src/main/java/com/mware/examples/admin/AdminWebAppPlugin.java#L17-L23)

## Register Extension

Register the admin extensions in the `plugin.js` file:

```javascript
 bc.registry.registerExtension('org.bigconnect.admin', {
  section: i18n('com.mware.examples.admin.section'),
  name: i18n('com.mware.examples.admin.react.name'),
  subtitle: i18n('com.mware.examples.admin.react.subtitle'),
  componentPath: 'com.mware/examples/admin/React'
});
```

[plugin.js \(lines 3–8\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-admin/src/main/resources/com/mware/examples/admin/plugin.js#L3-L8)

The other two are very similar except we had `sortHint` to "Open URL" so it is first.

```javascript
options: {
    sortHint: 0
}
```

[plugin.js \(lines 22–24\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-admin/src/main/resources/com/mware/examples/admin/plugin.js#L22-L24)

Notice we use the globally available `i18n` function to display strings. These are defined in `messages.properties`

```text
com.mware.examples.admin.section=Examples
com.mware.examples.admin.react.name=React
com.mware.examples.admin.react.subtitle=React admin example
```

[messages.properties \(lines 2–4\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-admin/src/main/resources/com/mware/examples/admin/messages.properties#L2-L4)

