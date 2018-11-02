---
description: Add custom logout handlers
---

# Logout

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-logout)

Plugin to add custom logout handlers. When the user explicitly logs out, or session expiration.

If the handler returns `false` all other logout handlers are skipped and the default logout process is cancelled.

## Web Plugin

Register the plugin resource in a web plugin.

```java
app.registerResourceBundle("/com/mware/examples/logout/messages.properties");
```

[LogoutWebAppPlugin.java \(line 17\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-logout/src/main/java/com/mware/examples/logout/LogoutWebAppPlugin.java#L17)

## Register Extension

Register the logout extension that warns the user, prevents the default logout action, and does the logout itself.

```javascript
bc.registry.registerExtension('org.bigconnect.logout', function() {
    var seconds = 3;
    alert('Will logout in ' + seconds + ' seconds');
    _.delay(function() {
        visallo.connect()
            .then(function(connected) {
                $(document).trigger('willLogout');
                return connected.dataRequest('user', 'logout');
            })
            .then(function() {
                window.location.reload();
            })
    }, seconds * 1000)
    return false;
});
```

[plugin.js \(lines 3–17\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-logout/src/main/resources/com/mware/examples/logout/plugin.js#L3-L17)

