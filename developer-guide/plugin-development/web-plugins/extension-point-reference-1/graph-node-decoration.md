---
description: Add decorations to graph nodes
---

# Graph Node Decoration

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-decoration)

Graph node decorations are additional detail to display around a vertex when displayed in a graph. These decorations are implemented as [Cytoscape](http://js.cytoscape.org/) nodes inside of compound nodes. That allows them to be styled just like vertices using `org.bigconnect.graph.style` extensions.

{% hint style="danger" %}
Decorations can have performance impact on the graph.

Once a node displays a decoration, another container node is created that is never removed. Also each decoration is a full cytoscape node.
{% endhint %}

## Alignment Positions

The figure below shows the available positions. The alignment locations are automatically adjusted based on the placement of the text in a node.

![](http://localhost/extension-points/front-end/graphDecorations/alignment-options.png)

Annotated positions and `alignment` configuration value:

1. Top left `{ h: 'left', v: 'top' }`
2. Top center `{ h: 'center', v: 'top' }`
3. Top right `{ h: 'right', v: 'top' }`
4. Center left `{ h: 'left', v: 'center' }`
5. Center center `{ h: 'center', v: 'center' }`
6. Center right `{ h: 'right', v: 'center' }`
7. Bottom left `{ h: 'left', v: 'bottom' }`
8. Bottom center `{ h: 'center', v: 'bottom' }`
9. Bottom right `{ h: 'right', v: 'bottom' }`

_**Note:** There is no collision detection on decorations with equal alignments_.

![](http://localhost/extension-points/front-end/graphDecorations/decorations.png)

## Web Plugin

Register the plugin script in a web plugin.

```java
app.registerJavaScript("/com/mware/examples/graph_node_decoration/popover.js", false);
```

[GraphNodeDecorationWebAppPlugin.java \(line 17\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-decoration/src/main/java/com/mware/examples/graph_node_decoration/GraphNodeDecorationWebAppPlugin.java#L17)

## Register Extension

Register the decoration extension for a new decoration in the top-left corner of nodes. This decoration will apply to all vertices that have a comment, and display the number of comments in the decoration.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.node.decoration', {
    applyTo: function(v) {
        return _.any(v.properties, function(p) {
            return p.name === ONTOLOGY_CONSTANTS.PROP_COMMENT_ENTRY &&
                p.value.indexOf(onlyDecorationAlignmentsText) === -1 &&
                p.value.indexOf("popover") === -1;
        })
    },
    alignment: { h: 'left', v: 'top' },
    classes: 'custom',
    data: function(vertex) {
        return {
            label: vertex.properties.reduce(function(sum, p) {
                if (p.name === ONTOLOGY_CONSTANTS.PROP_COMMENT_ENTRY) {
                    return sum + 1;
                }
                return sum;
            }, 0)
        }
    }
});
```

[plugin.js \(lines 34–54\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-decoration/src/main/resources/com/mware/examples/graph_node_decoration/plugin.js#L34-L54)

The default graph style-sheet defines `label` as the content of the node.

```javascript
content: 'data(label)',
```

[styles.js \(line 213\)](https://github.com/mware-solutions/doc-examples/blob/master/main-source-doc/product/graph/styles.js#L213)

Register a style extension to format the decoration. All decorations have the `decoration` class, so append that to the selector to avoid conflicts with node classes.

```javascript
    onClick: function(event, data) {
        var id = this.id();
        require(['com/mware/examples/graph_node_decoration/popover'], function(Popover) {
            Popover.attachTo(data.cy.container(), {
                anchorTo: {
                    decorationId: id
                }
            });
        })
    }
});
```

[plugin.js \(lines 70–80\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-decoration/src/main/resources/com/mware/examples/graph_node_decoration/plugin.js#L70-L80)

### Popover Tutorial <a id="popover-tutorial"></a>

Decorations can have popovers that are opened when the user clicks on the decoration using the `onClick` handler

.![](http://localhost/extension-points/front-end/graphDecorations/popover.png)

#### Web Plugin <a id="web-plugin"></a>

Register the popover component and template.

```java
app.registerJavaScript("/com/mware/examples/graph_node_decoration/plugin.js", true);
app.registerJavaScript("/com/mware/examples/graph_node_decoration/popover.js", false);
app.registerJavaScriptTemplate("/com/mware/examples/graph_node_decoration/template.hbs");
```

[GraphNodeDecorationWebAppPlugin.java \(lines 16–18\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-decoration/src/main/java/com/mware/examples/graph_node_decoration/GraphNodeDecorationWebAppPlugin.java#L16-L18)

#### Register Extension <a id="register-extension"></a>

Register the decoration with an `onClick` handler.

```javascript
    onClick: function(event, data) {
        var id = this.id();
        require(['com/mware/examples/graph_node_decoration/popover'], function(Popover) {
            Popover.attachTo(data.cy.container(), {
                anchorTo: {
                    decorationId: id
                }
            });
        })
    }
});
```

[plugin.js \(lines 70–80\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-decoration/src/main/resources/com/mware/examples/graph_node_decoration/plugin.js#L70-L80)

#### Popover Component <a id="popover-component"></a>

Create the Flight component to render the popover.

```javascript
define([
    'public/v1/api',
    'util/popovers/withPopover'
], function(
    api,
    withPopover) {

    return api.defineComponent(DecorationPopover, withPopover);

    function DecorationPopover() {

        this.before('initialize', function(node, config) {
            config.template = '/com/mware/examples/graph_node_decoration/template'
            this.after('setupWithTemplate', function() {
            })
        });
    }
});
```

[popover.js](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-decoration/src/main/resources/com/mware/examples/graph_node_decoration/popover.js)

The `withPopover` mixin provides the popover specific handling to attach to the decoration.

Add the template with the necessary markup for the popover.

```markup
<div class="fileImportPopover popover top collapseVisibility">
  <div class="popover-title">Example</div>
  <div class="arrow"></div>
  <div class="popover-content">
    Popover for Graph Node
  </div>
</div>
```

[template.hbs](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-decoration/src/main/resources/com/mware/examples/graph_node_decoration/template.hbs)

The popover mixin calls `setupWithTemplate` to initialize the popover, so if extra work is needed to be done after the template has rendered, use `after('setupWithTemplate')`. `this.dialog` and `this.popover` are instance variables for the popover and the content.

