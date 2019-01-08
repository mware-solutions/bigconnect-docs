---
description: Add custom graph view overlay components
---

# Graph View

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-view)

Plugin to add custom view components which overlay the graph. Used for toolbars, etc., that interact with the graph.

Views can be Flight or React components and should be styled to be absolutely positioned. The absolute position given is relative to the graph. 0,0 is top-left corner of graph.

![](../../../../.gitbook/assets/image%20%2832%29.png)

## Web Plugin

Register the plugin script, React component, and less in a web plugin.

```java
app.registerJavaScript("/com/mware/examples/graph_view/plugin.js", true);
app.registerJavaScriptComponent("/com/mware/examples/graph_view/View.jsx");
app.registerLess("/com/mware/examples/graph_view/style.less");
app.registerResourceBundle("/com/mware/examples/graph_view/messages.properties");
```

[GraphViewWebAppPlugin.java \(lines 16–19\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-view/src/main/java/com/mware/examples/graph_view/GraphViewWebAppPlugin.java#L16-L19)

#### Register Extension <a id="register-extension"></a>

Register the options extension pointing to the React component.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.view', {
    componentPath: 'com/mware/examples/graph_view/View',
    className: 'com.mware-examples-graph-view'
});
```

[plugin.js \(lines 3–6\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-view/src/main/resources/com/mware/examples/graph_view/plugin.js#L3-L6)

## Component

Create the graph view component. This one will be like a floating toolbar panel.

```javascript
const GraphViewExample = createReactClass({
    render() {
        return (
            <div>
                Custom palette
                <div className="btn-toolbar">
                    <div className="btn-group">
                        <button className="btn btn-mini">A</button>
                        <button className="btn btn-mini">B</button>
                        <button className="btn btn-mini">C</button>
                    </div>
                    <div className="btn-group">
                        <button className="btn btn-mini">D</button>
                        <button className="btn btn-mini">E</button>
                    </div>
                </div>
            </div>
        )
    }
});
```

[View.jsx \(lines 6–25\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-view/src/main/resources/com/mware/examples/graph_view/View.jsx#L6-L25)

#### Style <a id="style"></a>

The less style file is wrapped in the class name defined in the component to avoid conflicts with other plugins and core BigConnect styles.

```css
.com.mware-examples-graph-view {
  background: #ececec;
  display: inline-block;
  padding: 0.5em;
  margin: 0.5em;
  border: 1px solid #bdbdbd;
  box-shadow: 0 0px 20px rgba(0,0,0,0.1);
  border-radius: 2px;
  color: #4c4c4c;
  text-align: center;

  .btn-toolbar {
    margin: 0;
  } 
}

.graph-views {
    background-color: transparent !important;
}
```

[style.less](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-view/src/main/resources/com/mware/examples/graph_view/style.less)

