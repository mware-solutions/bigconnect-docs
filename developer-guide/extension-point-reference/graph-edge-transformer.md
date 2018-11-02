---
description: Modify edge data
---

# Graph Edge Transformer

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-edge-transformer)

Register a function that can modify the [Cytoscape](http://js.cytoscape.org/) edge data.

![](http://localhost/extension-points/front-end/graphEdge/transformer.png)

## Web Plugin

Create a web plugin and register the plugin.

```java
app.registerJavaScript("/com/mware/examples/graph_edge_transformer/plugin.js", true);
```

[GraphEdgeTransformerWebAppPlugin.java \(line 16\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-edge-transformer/src/main/java/com/mware/examples/graph_edge_transformer/GraphEdgeTransformerWebAppPlugin.java#L16)

## Register Extension

Register the transformer that counts all the properties of the collapsed edges and sets a new data parameter called `numProperties`.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.edge.transformer', function(data) {
    data.numProperties = data.edges.reduce(function(sum, edge) {
        return sum + edge.properties.length;
    }, 0)
});
```

[plugin.js \(lines 3–7\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-edge-transformer/src/main/resources/com/mware/examples/graph_edge_transformer/plugin.js#L3-L7)

Create a style extension to test. We use `mapData` to interpolate the number into a color.

```javascript
bc.registry.registerExtension('org.bigconect.graph.style', function(cytoscapeStylesheet) {
     cytoscapeStylesheet.selector('edge[numProperties]')
         .style({
             'line-color': 'mapData(numProperties, 4, 10, blue, red)',
             'target-arrow-color': 'mapData(numProperties, 4, 10, blue, red)'
         })
});
```

[plugin.js \(lines 9–15\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-edge-transformer/src/main/resources/com/mware/examples/graph_edge_transformer/plugin.js#L9-L15)

