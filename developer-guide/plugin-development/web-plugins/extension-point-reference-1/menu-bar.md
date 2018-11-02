---
description: Add new entries in the application menu bar
---

# Menu Bar

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-menubar)

Add additional icons into the menu bar that can open a component in the content area like the built-in dashboard.

The icon can refer to an existing icon bundled with BigConnect, or one registered with `registerFile` in a plugin. For best results, use a white mono-chromatic icon.

## Web Plugin

Register the plugin, a component for the pane, and a template for the _Welcome to BigConnect_ card.

```java
app.registerJavaScript("/com/mware/examples/menubar/plugin.js", true);
app.registerJavaScriptComponent("/com/mware/examples/menubar/Pane.jsx");
app.registerJavaScriptTemplate("/com/mware/examples/menubar/welcome.hbs");
app.registerResourceBundle("/com/mware/examples/menubar/messages.properties");
```

[MenubarWebAppPlugin.java \(lines 16–19\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-menubar/src/main/java/com/mware/examples/menubar/MenubarWebAppPlugin.java#L16-L19)

## Register Extension

Register the menu bar extension by pointing to the component and template. This one will use a bundled icon.

```javascript
bc.registry.registerExtension('org.bigconnect.menubar', {
    title: i18n('com.mware.examples.menubar.title'),
    identifier: 'com.mware-examples-menubar',
    action: {
        type: 'full',
        componentPath: 'com/mware/examples/menubar/Pane'
    },
    welcomeTemplatePath: 'hbs!com/mware/examples/menubar/welcome',
    icon: '../img/glyphicons/white/glyphicons_066_tags@2x.png',
    options: {
        placementHint: 'top',
        placementHintAfter: 'search',
    }
});
```

[plugin.js \(lines 3–16\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-menubar/src/main/resources/com/mware/examples/menubar/plugin.js#L3-L16)

## Component

Create a basic React component that displays some text and a button. Add some padding around the panel to match other panes.

```javascript
define([
    'create-react-class'
], function(createReactClass) {
    'use strict';

    const MenubarExamplePane = createReactClass({
        render() {
            return (
                <div style={{padding: '1em'}}>
                    Example Panel
                    
                    <button 
                        style={{display: 'block', width: '100%'}}
                        className="btn">Example</button>
                </div>
            )
        }
    });

    return MenubarExamplePane ;
});
```

[Pane.jsx](https://github.com/mware-solutions/doc-examples/blob/master/extension-menubar/src/main/resources/com/mware/examples/menubar/Pane.jsx)



