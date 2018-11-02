# Extension Points

## Extension Points <a id="extension-points"></a>

Extension points are places built into BigConnect's Web Console to define additional behavior. Custom plugins can also define extension points that can be implemented by other plugins.

An extension point is simply a mapping from a string – the namespaced extension point name – to a JavaScript object. What kind of object is defined by the consumer of the extension point.

All registered extension points are viewable in the admin panel, under `UI Extensions`, and in the table of contents in this document.

To register some custom behavior, require the [`public/v1/api`](https://docs.bigconnect.io/~/drafts/-LQEgexO3YP4CDrrELDr/primary/javascript/module-public_v1_api.html) module, and use the [`registry`](https://docs.bigconnect.io/~/drafts/-LQEgexO3YP4CDrrELDr/primary/javascript/module-registry.html) member.

```javascript
require(['public/v1/api'], function(bc) {    var registry = bc.registry;    registry.registerExtension([extension point name], [extension point object])})
```

For example, to add an item to the menu bar, use the Menu bar extension point:

```text
bc.registry.registerExtension('org.bigconnect.menubar', {    title: i18n('org.bigconnect.examples.menubar.title'),    identifier: 'org-bigconnect-examples-menubar',    action: {        type: 'pane',        componentPath: 'org/bigconnect/examples/menubar/Pane'    },    welcomeTemplatePath: 'hbs!org/bigconnect/examples/menubar/welcome',    icon: '../img/glyphicons/white/glyphicons_066_tags@2x.png',    options: {        placementHint: 'top',        placementHintAfter: 'search',    }});
```

Web plugins can also define their own extension points. They do not need to be defined ahead of time, simply ask the registry for all registered extensions using a unique point name:

```text
registry.extensionsForPoint([extension point name]);// Returns array of registered objects
```

It is good practice to define some documentation for your new extension point. Documenting provides a validation function, and a description shown in the admin panel under UI Extensions. Document the extension point using `registry.documentExtensionPoint` before prior to requesting `registry.extensionsForPoint`

```text
registry.documentExtensionPoint('com.example.point',    'Description...',    function () { return true; },    'http://example.com/docs');
```

If you call `documentExtensionPoint` before `extensionsForPoint` all the extensions returned are guaranteed to have passed validation. Invalid extensions are logged as warnings in the JavaScript console. This documentation appears in the admin pane under UI Extensions. Add an external documentation URL using an optional 4th parameter.

![](../../../.gitbook/assets/image%20%2819%29.png)

Invalid extensions are logged as warnings in the browser JavaScript console.

Please see the list of available extension points with descriptions and code examples under the section [Extension Point Reference](extension-point-reference-1/).



