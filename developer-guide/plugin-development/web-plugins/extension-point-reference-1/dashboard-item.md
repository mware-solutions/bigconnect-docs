---
description: Create new dashboard widgets
---

# Dashboard Item

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-item)

Dashboard items are user-selectable content placed on the dashboard. These could be charts using the `report` configuration or custom components in React or FlightJS.

![](../../../../.gitbook/assets/image%20%2836%29.png)

In this tutorial we will create two new dashboard items:

* Component that defines a report to show concept counts
* Custom React component that renders a number and configuration to increment

## Web Plugin

Register the resources.

```javascript
app.registerJavaScript("/com/mware/examples/dashboard_item/plugin.js", true);

app.registerJavaScriptComponent("/com/mware/examples/dashboard_item/React.jsx");
app.registerJavaScriptComponent("/com/mware/examples/dashboard_item/Config.jsx");

app.registerResourceBundle("/com/mware/examples/dashboard_item/messages.properties");
```

[DashboardItemWebAppPlugin.java \(lines 16–21\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-item/src/main/java/com/mware/examples/dashboard_item/DashboardItemWebAppPlugin.java#L16-L21)

## Register Extension

Register two extensions, a report-style card, and a custom component.

```javascript
bc.registry.registerExtension('org.bigconnect.web.dashboard.item', {
        title: 'Concept Type Counts',
        description: 'Show total counts for entity types',
        identifier: 'com.mware-examples-dashboard-item',
        report: {
            defaultRenderer: 'org.bigconnect-pie',
            endpoint: '/vertex/search',
            endpointParameters: {
                q: '*',
                size: 0,
                filter: '[]',
                aggregations: [
                    {
                        type: 'term',
                        name: 'field',
                        field: ONTOLOGY_CONSTANTS.PROP_CONCEPT_TYPE
                    }
                ].map(JSON.stringify)
            }
        }
    });

    bc.registry.registerExtension('org.bigconnect.web.dashboard.item', {
        title: 'React Component',
        description: 'Example React Component',
        identifier: 'com.mware-examples-dashboard-item-react',
        componentPath: 'com/mware/examples/dashboard_item/React',
        configurationPath: 'com/mware/examples/dashboard_item/Config'
    });
```

[plugin.js \(lines 3–31\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-item/src/main/resources/com/mware/examples/dashboard_item/plugin.js#L3-L31)

## Report Configuration

```javascript
defaultRenderer: 'org.bigconnect-pie',
endpoint: '/vertex/search',
endpointParameters: {
    q: '*',
    size: 0,
    filter: '[]',
    aggregations: [
        {
            type: 'term',
            name: 'field',
            field: ONTOLOGY_CONSTANTS.PROP_CONCEPT_TYPE
        }
    ].map(JSON.stringify)
}
```

[plugin.js \(lines 8–21\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-item/src/main/resources/com/mware/examples/dashboard_item/plugin.js#L8-L21)

## Create the Custom Component

The custom component will also register a configuration component. It will access a `count` property in the configuration and display the current value.

```javascript
        item: { ...item, configuration: { ...item.configuration, count: 0 } },
        extension
    })
});
```

[React.jsx \(lines 11–14\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-item/src/main/resources/com/mware/examples/dashboard_item/React.jsx#L11-L14)

In the configuration component, we increment the count \(creating if needed\) when the button is clicked.

```javascript
const { item, extension } = this.props;
const { configuration:previous } = item;
const configuration = { ...previous, count: (previous.count || 0) + 1 };

this.props.configurationChanged({
    item: { ...item, configuration },
    extension
})
```

[Config.jsx \(lines 15–22\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-item/src/main/resources/com/mware/examples/dashboard_item/Config.jsx#L15-L22)

## Wiring Refresh

Dashboard triggers `refreshData` on all items when the user clicks the refresh button in the top-right corner. To wire this message in a React component we need the DOM Element of the item, so first register a `ref` in `render()`

```text
<div ref="wrap">
```

[React.jsx \(line 23\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-item/src/main/resources/com/mware/examples/dashboard_item/React.jsx#L23)

Then, listen for the event, we must use Jquery to listen as Flight uses non-standard event triggering.

```javascript
componentDidMount() {
    const { item, extension } = this.props;
    $(this.refs.wrap.parentNode).on('refreshData', (event) => {
        this.props.configurationChanged({
            item: { ...item, configuration: { ...item.configuration, count: 0 } },
            extension
        })
    });
},
```

[React.jsx \(lines 7–15\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-item/src/main/resources/com/mware/examples/dashboard_item/React.jsx#L7-L15)

Finally, unregister the listener on teardown

```javascript
componentWillUnmount() {
    $(this.refs.wrap.parentNode).off('refreshData');
},
```

[React.jsx \(lines 16–18\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-item/src/main/resources/com/mware/examples/dashboard_item/React.jsx#L16-L18)

## Custom UI Elements



![](../../../../.gitbook/assets/image%20%2830%29.png)

Provide a `configurationPath` to the extension to add an additional user interface component in the configure popover. The figure describes how the configuration interface is generated for the saved search dashboard item.

The possible configuration can come from:

* Default configuration \(edit title\)
* Extension specific \(configuration defined in extension `configurationPath`\)
* Report configuration \(choose which `reportRenderer`\)
* Report chosen configuration \(Report defined `configurationPath`\)

The configuration component gets attributes of the item when opened.

* `extension` The extension registered
* `item` The item instance which includes `configuration`

To update an items configuration, trigger `configurationChanged` in FlightJS or call `configurationChanged` from `props` in React.

```javascript
// Flight Example

this.attr.item.configuration.myConfigOption = 'newValue';
this.trigger('configurationChanged', {
    extension: this.attr.extension,
    item: this.attr.item
});
```



```javascript
// React Example

var { item, extension } = this.props,
    configuration = { ...item.configuration, newStateValue: true };

item = { ...item, configuration }
this.props.configurationChanged({ item, extension })
```



