---
description: Add new types of charts
---

# Dashboard Report Renderer

[Example code](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-reportrenderer)

Adds additional output types for dashboard items that define a `report` or `item.configuration.report`

![](../../../../.gitbook/assets/image%20%2841%29.png)

For this tutorial, we'll create a new JSON renderer that simply takes the result, formats it, then prints it.

![](../../../../.gitbook/assets/image%20%2820%29.png)

## Create the Web Plugin

Register the resources to define the extension and the referenced component:

```java
app.registerJavaScript("/com/mware/examples/dashboard_reportrenderer/plugin.js", true);
app.registerJavaScript("/com/mware/examples/dashboard_reportrenderer/renderer.js", false);
app.registerResourceBundle("/com/mware/examples/dashboard_reportrenderer/messages.properties");
```

[DashboardReportrendererWebAppPlugin.java \(lines 16–18\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-reportrenderer/src/main/java/com/mware/examples/dashboard_reportrenderer/DashboardReportrendererWebAppPlugin.java#L16-L18)

## Register the Extension

Register the new report renderer. It will accept any response.

```javascript
bc.registry.registerExtension('org.bigconnect.web.dashboard.reportrenderer', {
    identifier: 'com.mware-examples-dashboard-reportrenderer',
    label: i18n('com.mware.examples.dashboard_reportrenderer.label'),
    supportsResponse: function(data) {
        return true;
    },
    componentPath: 'com/mware/examples/dashboard_reportrenderer/renderer'
});
```

[plugin.js \(lines 3–10\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-reportrenderer/src/main/resources/com/mware/examples/dashboard_reportrenderer/plugin.js#L3-L10)

## Define the Renderer

Create the renderer component and include the mixin.

```javascript
define([
    'public/v1/api',
    'dashboard/reportRenderers/withRenderer'
], function(api, withReportRenderer) {

    return api.defineComponent(JSONStringRenderer, withReportRenderer)

    function JSONStringRenderer() {
```

[renderer.js \(lines 1–8\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-reportrenderer/src/main/resources/com/mware/examples/dashboard_reportrenderer/renderer.js#L1-L8)

Now, implement the `processData` and `render` functions

```javascript
this.processData = function(data) {
    return JSON.stringify(data, null, 2);
}

this.render = function(d3, node, data) {
    d3.select(node)
        .style({
            'white-space': 'pre',
            'font-family': 'menlo',
            'font-size': '8pt'
        })
        .text(data);
}
```

[renderer.js \(lines 11–23\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-dashboard-reportrenderer/src/main/resources/com/mware/examples/dashboard_reportrenderer/renderer.js#L11-L23)

## Renderer Mixin

The custom report renderer provides:

* Automatically requesting data using endpoint configuration
* Handling refresh and reflow events
* Basic click handling if aggregations found
* Error handling

If the renderer uses the mixin, the only function required is `render`. Optionally, a `processData` function can be defined to transform the raw server results. It's better to process the data in `processData`function instead of `render` because it will run once on `refreshData` events, instead of on every `reflow` event.

## Built-In Report Renderers

![](http://localhost/extension-points/front-end/dashboard/renderer-bar-h.png)`org-bigconnect-bar-horizontal`

Horizontal bar chart, also supports stacked bars if two aggregations provided.![](http://localhost/extension-points/front-end/dashboard/renderer-bar-v.png)`org-bigconnect-bar-vertical`

Vertical bar chart, also supports stacked bars if two aggregations provided.![](http://localhost/extension-points/front-end/dashboard/renderer-pie.png)`org-bigconnect-pie`

A classic pie chart.![](http://localhost/extension-points/front-end/dashboard/renderer-text-overview.png)`org-bigconnect-text-overview`

Text cards that show number and text.![](http://localhost/extension-points/front-end/dashboard/renderer-element-list.png)`org-bigconnect-element-list`

Standard list of elements, used in search results.

