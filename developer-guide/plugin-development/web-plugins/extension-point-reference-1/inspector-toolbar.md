---
description: Add toolbar items to the element inspector
---

# Inspector Toolbar

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-detail-toolbar)

Allows additional toolbar items to be added to the Inspector.

![](../../../../.gitbook/assets/image%20%2831%29.png)

## Web Plugin

Register the resources needed.

```javascript
app.registerJavaScript("/com/mware/examples/detail_toolbar/plugin.js", true);
app.registerResourceBundle("/com/mware/examples/detail_toolbar/messages.properties");
```

[DetailToolbarWebAppPlugin.java \(lines 16–17\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-detail-toolbar/src/main/java/com/mware/examples/detail_toolbar/DetailToolbarWebAppPlugin.java#L16-L17)

## Register Extension

Now, register the toolbar item.

```javascript
bc.registry.registerExtension('org.bigconnect.detail.toolbar', {
    title: i18n('com.mware.examples.detail_toolbar.google'),
    event: 'detail-toolbar-google',
    canHandle: function(objects) {
        return objects.vertices.length === 1 && objects.edges.length === 0
            && /#person$/.test(objects.vertices[0].conceptType);
    }
});
```

[plugin.js \(lines 3–10\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-detail-toolbar/src/main/resources/com/mware/examples/detail_toolbar/plugin.js#L3-L10)

## Listen

Register a document-level listener for the event specified in the extension. The [`formatters.vertex.title`](http://localhost/javascript/module-formatters.vertex.html#.title) function transforms an element into a title string using the ontology title formula.

```javascript
bc.connect().then(function(api) {
    $(document).on('detail-toolbar-google', function(e, data) {
        var person = data.vertices[0];
        var name = api.formatters.vertex.title(person);
        var url = 'http://www.google.com/#safe=on&q=' + name;
        window.open(url, '_blank');
    });
});
```

[plugin.js \(lines 12–19\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-detail-toolbar/src/main/resources/com/mware/examples/detail_toolbar/plugin.js#L12-L19)

