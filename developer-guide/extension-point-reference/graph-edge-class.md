---
description: Change the style of graph edges
---

# Graph Edge Class

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-edge-class)

Function that can change the [Cytoscape](http://js.cytoscape.org/) style  of edges. Useful for customizing the style of edges on the graph.

![](http://localhost/extension-points/front-end/graphEdge/class.png)

## Web Plugin

Register a plugin file to register the extensions.

```java
app.registerJavaScript("/com/mware/examples/graph_edge_class/plugin.js", true);
```

[GraphEdgeClassWebAppPlugin.java \(line 16\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-edge-class/src/main/java/com/mware/examples/graph_edge_class/GraphEdgeClassWebAppPlugin.java#L16)

## Register Extension

Register the class extension that checks if any of the edges \(they are by default bundled together by type\) has a comment. If any of them do, add a class.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.edge.class', function(edges, type, classes) {
    var hasComment = _.any(edges, function(edge) {
        var comment = _.findWhere(edge.properties, { name: ONTOLOGY_CONSTANTS.PROP_COMMENT_ENTRY })
        return comment && comment.value.indexOf('class') >= 0;
    });

    if (hasComment) {
        classes.push('hasComment')
    }
});
```

[plugin.js \(lines 3–12\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-edge-class/src/main/resources/com/mware/examples/graph_edge_class/plugin.js#L3-L12)

Register a style extension to test.

```javascript

bc.registry.registerExtension('org.bigconnect.graph.style', function(cytoscapeStylesheet) {
     cytoscapeStylesheet.selector('edge.hasComment')
         .style({
             color: '#ff0000',
             'line-color': '#ff0000',
             'target-arrow-color': '#ff0000',
             width: 5
         })
});
```

[plugin.js \(lines 13–22\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-edge-class/src/main/resources/com/mware/examples/graph_edge_class/plugin.js#L13-L22)

