---
description: Add an Export menu option
---

# Graph Export

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-export)

Register a function that can add a menu option in export context menu. If multiple export plugins are registered, they are placed in an "Export" submenu

.![](http://localhost/extension-points/front-end/graphExport/menu.png) ![](http://localhost/extension-points/front-end/graphExport/export.png)

## Web Plugin

Register the resources need for export, a plugin, component, template, stylesheet, and message bundle.

```java
    app.registerJavaScript("/com/mware/examples/graph_export/configuration.js", false);
    app.registerJavaScriptTemplate("/com/mware/examples/graph_export/template.hbs");
    app.registerLess("/com/mware/examples/graph_export/style.less");
    app.registerResourceBundle("/com/mware/examples/graph_export/messages.properties");
}
```

[GraphExportWebAppPlugin.java \(lines 16–20\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-export/src/main/java/com/mware/examples/graph_export/GraphExportWebAppPlugin.java#L16-L20)

## Register Extension

Register the extension to add a menu item to the graph context menu.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.export', {
    menuItem: i18n('com.mware.examples.graph_export.label'),
    componentPath: 'com/mware/examples/graph_export/configuration'
});
```

[plugin.js \(lines 3–6\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-export/src/main/resources/com/mware/examples/graph_export/plugin.js#L3-L6)

## Define Component

Create the Flight component that logs the [Cytoscape](http://js.cytoscape.org/) `json` backup.

```text
function GraphExportExample() {

    this.after('initialize', function() {
        this.$node.html(template({
            productId: this.attr.productId,
            workspaceId: this.attr.workspaceId
        }));
        this.$node.find('pre')[0].textContent = JSON.stringify(this.attr.cy.json(), null, 2)
    });

}
```

[configuration.js \(lines 11–21\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-export/src/main/resources/com/mware/examples/graph_export/configuration.js#L11-L21)

## Style

Add some stylesheet declarations in a wrapper class to avoid collisions with other plugins.

```css
.com.mware-examples-graph_export {

    pre {
        width: 100%;
        min-width: 15em;
        font-family: menlo;
        font-size: 80%;
        height: 10em;
        overflow: auto;
        display: block;
        padding: 0.5em;
        box-sizing: border-box;
        white-space: pre;
    }

    code {
        width: 100%;
        overflow: hidden;
        text-overflow: ellipsis;
        display: block;
        margin: 0;
        padding: 0 1em;
        box-sizing: border-box;
    }
}
// 7 lines hidden…
// 10 lines hidden…
```

[style.less](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-export/src/main/resources/com/mware/examples/graph_export/style.less)

