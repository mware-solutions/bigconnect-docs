---
description: Register new types of graph layout
---

# Graph Layout

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-layout)

Plugin to add [Cytoscape layout](http://js.cytoscape.org/#layouts)

![](http://localhost/extension-points/front-end/graphLayout/layout.png)

## Web Plugin

Register resources for the plugin and message bundle.

```javascript
app.registerJavaScript("/com/mware/examples/graph_layout/plugin.js", true);
app.registerResourceBundle("/com/mware/examples/graph_layout/messages.properties");
```

[GraphLayoutWebAppPlugin.java \(lines 16–17\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-layout/src/main/java/com/mware/examples/graph_layout/GraphLayoutWebAppPlugin.java#L16-L17)

## Register Extension

Register the layout extension.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.layout', MyLayout);
```

[plugin.js \(line 36\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-layout/src/main/resources/com/mware/examples/graph_layout/plugin.js#L36)

## Create the Layout Class

The layout class is initialized with options for the layout. These options include the `cy` instance and the elements \(`eles`\) to layout. These should be filtered to real vertices `.filter('.v')` so we are not moving decorations.

```javascript
MyLayout.identifier = 'example-random';
function MyLayout(options) {
    this.options = options;
}

MyLayout.prototype.run = function() {
    var cy = this.options.cy;

    // Use `eles` option allows layout
    // to work if running on all or selection
    // of nodes
    var nodes = this.options.eles || cy.nodes();

    var width = $(window).width();
    var height = $(window).height();

    nodes.filter('.v').each(function(i, node) {
        node.renderedPosition(retina.pointsToPixels({
            x: Math.round(Math.random() * width),
            y: Math.round(Math.random() * height)
        }));
    })

    // Must call ready and stop callbacks
    cy.one("layoutready", this.options.ready);
    cy.trigger("layoutready");

    cy.one("layoutstop", this.options.stop);
    cy.trigger("layoutstop");

    return this;
};
```

[plugin.js \(lines 3–34\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-layout/src/main/resources/com/mware/examples/graph_layout/plugin.js#L3-L34)

The positions are generated using a random number using the current window width. The use of `retina.pointsToPixels` allows transformation from virtual points to actual screen pixels in the case of a hidpi display.

```javascript
node.renderedPosition(retina.pointsToPixels({
    x: Math.round(Math.random() * width),
    y: Math.round(Math.random() * height)
}));
```

[plugin.js \(lines 20–23\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-layout/src/main/resources/com/mware/examples/graph_layout/plugin.js#L20-L23)

## Message Bundle

Add a i18n value in a `MessageBundle.properties`. This will be displayed in the graph context menu.

```text
graph.layout.example-random.displayName=Random (Example)
```

[messages.properties \(line 1\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-layout/src/main/resources/com/mware/examples/graph_layout/messages.properties#L1)

