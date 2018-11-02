---
description: Register new websocket message listeners
---

# Websocket

Extension to register new listeners for websocket messages. Must be registered in JavaScript file registered with `app.registerWebWorkerJavaScript` in web app plugin.

```javascript
registry.registerExtension('org.bigconnect.websocket.message', {
    name: name,
    handler: function(data) {
    }
});
```

