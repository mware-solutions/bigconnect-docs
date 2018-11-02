---
description: Change the visuals style of graph objects
---

# Graph Style

Apply additional [Cytoscape styles](http://js.cytoscape.org/#style) to the graph. This is used to adjust the styling of all graph elements: Nodes, Edges, Decorations, etc.

* [Node Styles](http://js.cytoscape.org/#style/node-body)
* [Edges Styles](http://js.cytoscape.org/#style/edge-line)
* [Core Styles](http://js.cytoscape.org/#style/core)
* Style values can use [interpolation](http://js.cytoscape.org/#style/mappers) based on data values.

All style extensions have precedence over the built-in style if the selectors are similar specificity. The BigConnect style-sheet is defined as:

```javascript
        function getExtensionStyles() {
            // Mock the cytoscape style fn api to the json style
            const collector = styleCollector();

            /**
             * @callback org.visallo.graph.style~StyleFn
             * @param {object} cytoscapeStyle
             * @param {function} cytoscapeStyle.selector Switch to adjusting the passed in selector string
             * @param {function} cytoscapeStyle.style Add styles to the current selector. Accepts one parameter,
             * the `object` of styles to add
             * @param {function} cytoscapeStyle.css Alias of `style`
             */
            styleExtensions.forEach(fn => fn(collector.mock));
            return collector.styles;

            function styleCollector() {
                const styles = {};
                var currentSelector;
                const add = (obj) => {
                    if (!currentSelector) throw new Error('No selector found for style: ' + obj)
                    styles[currentSelector] = obj;
                    return api.mock;
                }
                const api = {
                    mock: {
                        selector: (str) => {
                            currentSelector = str;
                            return api.mock;
                        },
                        style: add,
                        css: add
                    },
                    get styles() {
                        return _.map(styles, (style, selector) => ({ selector, style }))
                    }
                }
                return api;
            }
        }

        function getSelectionStyles() {
            return [
                {
                    selector: 'node:selected',
                    css: {
                        'display': 'element',
                        'opacity': 1,
                        'background-image-opacity': 1,
                        'background-color': '#0088cc',
                        'border-color': '#0088cc',
                        'color': '#0088cc'
                    }
                },
                {
                    selector: 'node[selectedImageSrc]:selected',
                    css: {
                        'background-image': 'data(selectedImageSrc)'
                    }
                },
                {
                    selector: 'edge:selected',
                    css: {
                        'display': 'element',
                        'opacity': 1,
                        'line-color': '#0088cc',
                        'color': '#0088cc',
                        'target-arrow-color': '#0088cc',
                        'source-arrow-color': '#0088cc'
                    }
                }

            ];
        }

        function getDefaultStyles() {
            return [
                {
                    selector: 'core',
                    css: {
                        'outside-texture-bg-color': '#efefef'
                    }
                },
                {
                    selector: 'node',
                    css: {
                        'background-color': '#ccc',
                        'background-fit': 'contain',
                        'border-color': 'white',
                        'background-image-crossorigin': 'use-credentials',
                        'font-family': 'helvetica',
                        'font-size': 18 * pixelRatio,
                        'min-zoomed-font-size': 4,
                        'text-events': 'yes',
                        'text-outline-color': 'white',
                        'text-outline-width': 2,
                        'text-halign': 'center',
                        'text-valign': 'bottom',
                        content: 'Loading…',
                        opacity: 1,
                        color: '#999',
                        height: GENERIC_SIZE * pixelRatio,
                        shape: 'roundrectangle',
                        width: GENERIC_SIZE * pixelRatio
                    }
                },
                {
                    selector: 'node.ancillary',
                    css: {
                        'background-color': '#fff',
                        'background-fit': 'contain',
                        'border-color': 'white',
                        'background-image-crossorigin': 'use-credentials',
                        'font-family': 'helvetica',
                        'font-size': 18 * pixelRatio,
                        'min-zoomed-font-size': 4,
                        'text-events': 'yes',
                        'text-outline-color': 'transparent',
                        'text-outline-width': 0,
                        'text-halign': 'center',
                        'text-valign': 'center',
                        content: '',
                        opacity: 1,
                        color: '#333',
                        height: GENERIC_SIZE * pixelRatio,
                        shape: 'rectangle',
                        width: GENERIC_SIZE * pixelRatio
                    }
                },
                {
                    selector: 'node.ancillary.unhandled',
                    css: {
                        display: 'none'
                    }
                },
                {
                    selector: 'node.drawEdgeToMouse',
                    css: {
                        'background-opacity': 0,
                        'text-events': 'no',
                        width: GENERIC_SIZE * pixelRatio,
                        height: GENERIC_SIZE * pixelRatio,
                        shape: 'ellipse',
                        content: '',
                        events: 'no'
                    }
                },
                {
                    selector: 'node.v',
                    css: {
                        'background-color': '#fff',
                        'background-image': 'data(imageSrc)',
                        content: 'data(truncatedTitle)',
                    }
                },
                {
                    selector: 'node.c',
                    css: {
                        'background-color': '#fff',
                        'background-image': 'data(imageSrc)',
                        shape: 'rectangle',
                        content: 'data(truncatedTitle)',
                    }
                },
                {
                    selector: 'node.decorationParent',
                    css: {
                        'background-image': 'none',
                        'background-color': 'transparent',
                        'background-opacity': 0,
                        'compound-sizing-wrt-labels': 'exclude',
                        content: ''
                    }
                },
                {
                    selector: 'node.decorationParent:active',
                    css: {
                        'background-color': 'transparent',
                        'background-opacity': 0,
                        'overlay-color': 'transparent',
                        'overlay-padding': 0,
                        'overlay-opacity': 0,
                        'border-width': 0
                    }
                },
                {
                    selector: 'node.decoration',
                    css: {
                        'background-color': '#F89406',
                        'background-image': 'none',
                        'border-width': 2,
                        'border-style': 'solid',
                        'border-color': '#EF8E06',
                        'text-halign': 'center',
                        'text-valign': 'center',
                        'font-size': 20,
                        color: 'white',
                        'text-outline-color': 'transparent',
                        'text-outline-width': 0,
                        content: 'data(label)',
                        events: 'no',
                        shape: 'roundrectangle',
                        'padding-left': 5,
                        'padding-right': 5,
                        'padding-top': 3,
                        'padding-bottom': 3,
                        width: 'label',
                        height: 'label',
                        'z-index': 1
                    }
                },
                {
                    selector: 'node.decoration.hidden',
                    css: {
                        display: 'none'
                    }
                },
                {
                    selector: 'node.video',
                    css: {
                        shape: 'rectangle',
                        width: (CUSTOM_IMAGE_SIZE * pixelRatio) * VIDEO_ASPECT_RATIO,
                        height: (CUSTOM_IMAGE_SIZE * pixelRatio) / VIDEO_ASPECT_RATIO
                    }
                },
                {
                    selector: 'node.image',
                    css: {
                        shape: 'rectangle',
                        width: (CUSTOM_IMAGE_SIZE * pixelRatio) * IMAGE_ASPECT_RATIO,
                        height: (CUSTOM_IMAGE_SIZE * pixelRatio) / IMAGE_ASPECT_RATIO
                    }
                },
                {
                    selector: 'node.hasCustomGlyph',
                    css: {
                        width: CUSTOM_IMAGE_SIZE * pixelRatio,
                        height: CUSTOM_IMAGE_SIZE * pixelRatio
                    }
                },
                {
                    selector: 'node.hover',
                    css: {
                        opacity: 0.6
                    }
                },
                {
                    selector: 'node.focus',
                    css: {
                        color: '#00547e',
                        'font-weight': 'bold',
                        'overlay-color': '#a5e1ff',
                        'overlay-padding': 10 * pixelRatio,
                        'overlay-opacity': 0.5
                    }
                },
                {
                    selector: 'edge.focus',
                    css: {
                        'overlay-color': '#a5e1ff',
                        'overlay-padding': 7 * pixelRatio,
                        'overlay-opacity': 0.5
                    }
                },
                {
                    selector: 'node.temp',
                    css: {
                        'background-color': 'rgba(255,255,255,0.0)',
                        'background-image': 'none',
                        width: '1',
                        height: '1'
                    }
                },
                {
                    selector: 'node.controlDragSelection',
                    css: {
                        'border-width': 5 * pixelRatio,
                        'border-color': '#a5e1ff'
                    }
                },
                {
                    selector: 'edge',
                    css: {
                        'font-size': 11 * pixelRatio,
                        'target-arrow-shape': 'triangle',
                        color: '#aaa',
                        content: edgeLabels ? 'data(label)' : '',
                        'curve-style': edgesCount > MaxEdgesBeforeHayStackOptimization || optimizedGraph ? 'haystack' : 'bezier',
                        'min-zoomed-font-size': 3,
                        'text-outline-color': 'white',
                        'text-outline-width': 2,
                        width: 2.5 * pixelRatio
                    }
                },
                {
                    selector: 'edge.label',
                    css: {
                        content: 'data(label)',
                        'font-size': 12 * pixelRatio,
                        color: '#0088cc',
                        'text-outline-color': 'white',
                        'text-outline-width': 4
                    }
                },
                {
                    selector: 'edge.drawEdgeToMouse',
                    css: {
                        events: 'no',
                        width: 4,
                        'line-color': '#0088cc',
                        'line-style': 'dotted',
                        'target-arrow-color': '#0088cc'
                    }
                },
                {
                    selector: 'edge.path-hidden-verts',
                    css: {
                        'line-style': 'dashed',
                        content: 'data(label)',
                        'font-size': 16 * pixelRatio,
                        color: 'data(pathColor)',
                        'text-outline-color': 'white',
                        'text-outline-width': 4
                    }
                },
                {
                    selector: 'edge.path-edge',
                    css: {
                        'line-color': 'data(pathColor)',
                        'target-arrow-color': 'data(pathColor)',
                        'source-arrow-color': 'data(pathColor)',
                        width: 4 * pixelRatio
                    }
                },
                {
                    selector: 'edge.temp',
                    css: {
                        width: 4,
                        'line-color': '#0088cc',
                        'line-style': 'dotted',
                        'target-arrow-color': '#0088cc'
                    }
                }
            ];
        }
    }
// 49 lines hidden…
// 9 lines hidden…
// 160 lines hidden…
// 18 lines hidden…
```

[styles.js \(lines 15–359\)](https://github.com/mware-solutions/doc-examples/blob/master/main-source-doc/product/graph/styles.js#L15-L359)

### Examples <a id="examples"></a>

Change the opacity of a node based on a class.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.style', function(cytoscapeStylesheet) {
     cytoscapeStylesheet.selector('node.unknownName')
         .style({
             'opacity': '0.75'
         })
});
```

[plugin.js \(lines 11–16\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-class/src/main/resources/com/mware/examples/graph_node_class/plugin.js#L11-L16)

Change the line color and thickness of an edge.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.style', function(cytoscapeStylesheet) {
     cytoscapeStylesheet.selector('edge.hasComment')
         .style({
             color: '#ff0000',
             'line-color': '#ff0000',
             'target-arrow-color': '#ff0000',
             width: 5
         })
```

[plugin.js \(lines 14–21\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-edge-class/src/main/resources/com/mware/examples/graph_edge_class/plugin.js#L14-L21)

Change the color of an edge \(and edge arrow\) based on value of `data` attribute.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.style', function(cytoscapeStylesheet) {
     cytoscapeStylesheet.selector('edge[numProperties]')
         .style({
             'line-color': 'mapData(numProperties, 4, 10, blue, red)',
             'target-arrow-color': 'mapData(numProperties, 4, 10, blue, red)'
         })
});
```

[plugin.js \(lines 9–15\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-edge-transformer/src/main/resources/com/mware/examples/graph_edge_transformer/plugin.js#L9-L15)

Change the size of a node based on the value of a `data` attribute

```javascript
require(['public/v1/api'], function(bc) {

    bc.registry.registerExtension('org.bigconnect.graph.node.transformer', function(vertex, data) {
        data.numProperties = vertex.properties.length;
    });

    bc.registry.registerExtension('org.bigconnect.graph.style', function(cytoscapeStylesheet) {
        var pixelRatio = 'devicePixelRatio' in window ? window.devicePixelRatio : 1;
        cytoscapeStylesheet.selector('node[numProperties]')
            .style({
                'width': 'mapData(numProperties, 4, 15, ' + 30 * pixelRatio + ', ' + 75 * pixelRatio + ')',
                'height': 'mapData(numProperties, 4, 15, ' + 30 * pixelRatio + ', ' + 75 * pixelRatio + ')'
            })
    });
});
```

[plugin.js](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-transformer/src/main/resources/com/mware/examples/graph_node_transformer/plugin.js)

Add multiple entries for decorations.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.style', function(style) {
    style
        .selector('.decoration.custom')
        .css({
            shape: 'octagon',
            'background-color': 'orange',
            'border-color': 'darkorange',
            'padding-left': 10,
            'padding-right': 10,
            'font-size': 23 * retina.devicePixelRatio,
            width: 20 * retina.devicePixelRatio,
            height: 20 * retina.devicePixelRatio
        })
        .selector('.decoration.customAll')
        .css({
            shape: 'roundrectangle',
            'background-color': 'blue',
            'border-color': 'darkblue',
            'padding-left': 10,
            'padding-right': 10,
            'font-size': 19 * retina.devicePixelRatio,
            width: 15 * retina.devicePixelRatio,
            height: 15 * retina.devicePixelRatio
        })
```

[plugin.js \(lines 82–105\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-node-decoration/src/main/resources/com/mware/examples/graph_node_decoration/plugin.js#L82-L105)

