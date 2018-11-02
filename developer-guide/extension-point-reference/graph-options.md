---
description: Add custom graph option menu items
---

# Graph Options

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-options)

Plugin to add custom options components \(Flight or React\) which display in the graph options menu \(next to Fit\) when the menu is opened.

![](http://localhost/extension-points/front-end/graphOptions/options.png)

For this tutorial we'll create a new options extension that adds a preferenced-backed checkbox. This could be used for toggling some built-in graph styles, for example.

## Web Plugin

Register the plugin script and React component in a web plugin.

```java
app.registerJavaScriptComponent("/com/mware/examples/graph_options/React.jsx");
app.registerResourceBundle("/com/mware/examples/graph_options/messages.properties");
```

[GraphOptionsWebAppPlugin.java \(lines 17–18\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-options/src/main/java/com/mware/examples/graph_options/GraphOptionsWebAppPlugin.java#L17-L18)

## Register Extension

Register the options extension pointing to the React component.

```javascript
bc.registry.registerExtension('org.bigconnect.graph.options', {
    identifier: 'com.mware-examples-graph-opts',
    optionComponentPath: 'com/mware/examples/graph_options/React'
});
```

[plugin.js \(lines 3–6\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-options/src/main/resources/com/mware/examples/graph_options/plugin.js#L3-L6)

#### Component <a id="component"></a>

Create the component that renders a checkbox, and looks up user preferences.

```javascript
define(['public/v1/api', 'create-react-class'], function(api, createReactClass) {

    const PREF_NAME = 'my-option-value'

    const MyOption = createReactClass({
        render() {
            const { cy } = this.props;
            const myOptionDefault = bcData.currentUser.uiPreferences[PREF_NAME];
            return (
                <label>My Setting
                    <input
                        onChange={this.onChange}
                        type="checkbox"
                        defaultChecked={myOptionDefault} />
                </label>
            );
        },
```

[React.jsx \(lines 1–17\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-options/src/main/resources/com/mware/examples/graph_options/React.jsx#L1-L17)

Implement the saving of the preference when the user clicks the checkbox. This updates the in memory user object, and updates the server.

```javascript
onChange(event) {
    const { checked } = event.target;
    bcData.currentUser.uiPreferences[PREF_NAME] = checked;
    api.connect()
        .then(c => c.dataRequest('user', 'preference', PREF_NAME, checked))
        .then(() => console.log('saved'))
}
```

[React.jsx \(lines 18–24\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-graph-options/src/main/resources/com/mware/examples/graph_options/React.jsx#L18-L24)

