# Web Plugins

BigConnect is designed to be a single page application, but requests must be made back to the server to retrieve information, process data, and manage many other back-end services. Since BigConnect was designed to be extensible with many different types of plugins, web plugins fit naturally into BigConnect's architecture.

Web plugins are deployed alongside BigConnect inside of the web server and are used to both add custom functionality and override existing components.

## Extension Points

Extension points are places built into BigConnect's Web Console to define additional behavior. Custom plugins can also define extension points that can be implemented by other plugins.

An extension point is simply a mapping from a string – the namespaced extension point name – to a JavaScript object. What kind of object is defined by the consumer of the extension point.

All registered extension points are viewable in the admin panel, under `UI Extensions`, and in the table of contents in this document.

To register some custom behavior, require the [`public/v1/api`](../../javascript/module-public_v1_api.html) module, and use the [`registry`](../../javascript/module-registry.html) member.

```text
require(['public/v1/api'], function(bc) {
    var registry = bc.registry;
    registry.registerExtension([extension point name], [extension point object])
})
```

For example, to add an item to the menu bar, use the Menu bar extension point:

```javascript
bc.registry.registerExtension('org.bigconnect.menubar', {
    title: i18n('org.bigconnect.examples.menubar.title'),
    identifier: 'org-bigconnect-examples-menubar',
    action: {
        type: 'pane',
        componentPath: 'org/bigconnect/examples/menubar/Pane'
    },
    welcomeTemplatePath: 'hbs!org/bigconnect/examples/menubar/welcome',
    icon: '../img/glyphicons/white/glyphicons_066_tags@2x.png',
    options: {
        placementHint: 'top',
        placementHintAfter: 'search',
    }
});
```

Web plugins can also define their own extension points. They do not need to be defined ahead of time, simply ask the registry for all registered extensions using a unique point name:

```javascript
registry.extensionsForPoint([extension point name]);
// Returns array of registered objects
```

It is good practice to define some documentation for your new extension point. Documenting provides a validation function, and a description shown in the admin panel under UI Extensions. Document the extension point using  `registry.documentExtensionPoint` before prior to requesting `registry.extensionsForPoint`

```javascript
registry.documentExtensionPoint('com.example.point',
    'Description...',
    function () { return true; },
    'http://example.com/docs'
);
```

If you call `documentExtensionPoint` before `extensionsForPoint` all the extensions returned are guaranteed to have passed validation. Invalid extensions are logged as warnings in the JavaScript console.

Please see the list of available extension points with descriptions and code examples under the section [Extension Point Reference](../../extension-point-reference.md).

## Development

Creating a web app plugin can range from creating some JavaScript that can execute inside of BigConnect all the way to overriding the current functionality of an endpoint inside BigConnect. This means that web app plugins will always have some sort of mix of front-end and back-end components that provide functionality. 

To start working with a web plugin, you must implement the interface [`com.mware.web.WebAppPlugin`](https://github.com/mware-solutions/bigconnect-web/blob/master/web-base/src/main/java/com/mware/web/WebAppPlugin.java) and register all of your helper files in there. Since BigConnect has its own web layer framework, please consider reading the  [Web Framework](../../web-framework.md) section to understand more about how it works.

Before diving into web plugin development, there are a few things every developer should know.

### Compiling the front-end

From the `bigconnect-web/war` project directory, assuming that you have the [prerequisites](../../getting-started/setup-for-development.md) installed, the front-end can be compiled with:

```text
mvn clean compile
```

Compiling the front-end is useful if you want to customize the provided Web Console.

### Localization

All strings are loaded from `MessageBundle.properties` property files. Extend/replace strings using a web plugin that defines/overrides strings in another bundle using a web plugin. Use the `registerMessageBundle` Java API.

Translate message keys to current locale value using `i18n` JavaScript function available in global scope:

```javascript
i18n("visibility.label")
```

The translation function also supports interpolation. If a message key is defined as `my.property=The {0} brown fox {1} over the lazy dog` then in the JavaScript code you can do:

```javascript
i18n("my.property", "quick", "jumps");
// returns "The quick brown fox jumps over the lazy dog"
```

### Routing with URL fragments

The Web Console has a built-in set of routing using the URLs fragment identifier.

**Open Entity / Relation details**:

```
Fragment: #v=[(v[vertexId] | e[edgeId])]&w=[workspaceId]
Example: https://localhost:8888/#v=vMY_VERTEX_ID,eMy_EDGE_ID&w=MY_WORKSPACE
```

Opens the full-screen view of one or many entities/relationships. A `v` or `e` must prefix the id to signify the element type.

**Prompt User to Add Entity / Relation**

```text
Fragment: #add=[(v[vertexId] | e[edgeId])]
Example: https://localhost:8888/#add=MY_VERTEX_ID_1,MY_VERTEX_ID_2
```

Opens the Web Console, but prompts the user to add the passed in vertices to their space. Those vertices must be published.

**Open Admin Section**

```text
Fragment: #admin=[section][name]
Example: https://localhost:8888/#admin=plugin:ui+extensions
```

Opens the Web Console and the admin pane \(if the user has admin privilege\) to the admin tool with name `[name]` in section `[section]`.

### Property Info Metadata

Properties have an _info_ icon that opens a metadata popover. The metadata displayed can be configured with configuration property files.

```text
properties.metadata.propertyNames: Lists metadata properties to display in popover
properties.metadata.propertyNameDisplay: Lists metadata display name keys (MessageBundle.properties)
properties.metadata.propertyNamesType: Lists metadata types to format values.
```

Metadata Types: `timezone`, `datetime`, `user`, `sandboxStatus`, `percent`

### Helpful Global Functions

These are some developer helper functions. Run these commands in the browser console.

**LiveReload**

Have the browser auto refresh when changes are made. This is remembered in local storage so, it only needs to be run once to enable. `grunt` must be watching.

```javascript
enableLiveReload(true); // to enable (refresh browser once to start)
enableLiveReload(false); // to disable
```

**Switch Language**

Test changing the language. Sets a localStorage token and reloads the page while loading appropriate resources. Useful for checking the UI with different size text.

```javascript
switchLanguage('de'); // Accepts language or language and country with "_". Ex: en_us
```

**Component Highlighter**

Overlays component name using mouseover events. Useful for checking what component is responsible for what on the page.

```javascript
enableComponentHighlighting(true); // Display component overlays
enableComponentHighlighting(false); // Disable component overlays
```

## Deployment

To ensure that your web plugin is deployed, it needs to be loaded onto the classpath by adding it's fully qualified class name into the `META-INF/services/com.mware.web.WebAppPlugin` file in a jar that is on the classpath.

