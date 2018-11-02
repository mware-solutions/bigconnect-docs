---
description: Adjust the data for graph nodes
---

# Graph Node Transformer

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-transformer)

Allows extensions to adjust the data attribute of [Cytoscape](http://js.cytoscape.org/) nodes.![](http://localhost/extension-points/front-end/graphNode/transformer.png)

This tutorial will adjust the size of graph nodes based on how many properties they have.

## Web Plugin

Register the plugin script in a web plugin.

```java
app.registerJavaScript("/com/mware/examples/graph_node_transformer/plugin.js", true);
```

[GraphNodeTransformerWebAppPlugin.java \(line 16\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-transformer/src/main/java/com/mware/examples/graph_node_transformer/GraphNodeTransformerWebAppPlugin.java#L16)

## Register Extension

Register the transformer extension that just places a property count into the data object.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.node.transformer', function(vertex, data) {
    data.numProperties = vertex.properties.length;
});
```

[plugin.js \(lines 3–5\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-transformer/src/main/resources/com/mware/examples/graph_node_transformer/plugin.js#L3-L5)

Register a style extension to test the transformer. The selector checks for the data property and adjusts the size of the node depending on `numProperties`.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.style', function(cytoscapeStylesheet) {
    var pixelRatio = 'devicePixelRatio' in window ? window.devicePixelRatio : 1;
    cytoscapeStylesheet.selector('node[numProperties]')
        .style({
            'width': 'mapData(numProperties, 4, 15, ' + 30 * pixelRatio + ', ' + 75 * pixelRatio + ')',
            'height': 'mapData(numProperties, 4, 15, ' + 30 * pixelRatio + ', ' + 75 * pixelRatio + ')'
        })
});
```

[plugin.js \(lines 7–14\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-transformer/src/main/resources/com/mware/examples/graph_node_transformer/plugin.js#L7-L14)

