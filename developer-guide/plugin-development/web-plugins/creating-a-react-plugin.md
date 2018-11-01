# Creating a React plugin

Writing components in [React](https://facebook.github.io/react) is now the preferred way to extend the Web Console with custom features. Most extension points already support React, but check their [documentation](../../extension-point-reference.md) to make sure.

When writing a web plugin, there are two methods to include React components:

1. Use `registerJavaScriptComponent` to include a React `jsx` component from the plugins resource folder.
2. Integrate a build step to your plugins `pom.xml` to transpile `jsx` components and then register them with `registerCompiledJavaScript`.

Each approach as its pros and cons, as we will see below.

## registerJavaScriptComponent

**PROS**:

* Easy to get started, or for components with minimal complexity. Doesn't require separate build step.

**CONS**: 

* Doesn't scale as well with many files. Each file must be registered.
* Each file registered slows server startup as they are compiled at run-time
* Compilation failures will happen at run-time

**Example**

This example will create a plugin that creates a new card that users can add to their dashboard.

Create a java file at `plugins/web/src/main/java/org/bigconnect/example/web/ReactDemoWebAppPlugin.java` with the following content:

```java
package org.bigconnect.example.web;

import com.mware.core.model.Description;
import com.mware.core.model.Name;
import com.mware.web.WebApp;
import com.mware.web.WebAppPlugin;
import com.mware.web.framework.Handler;

import javax.servlet.ServletContext;

@Name("React Web Demo")
@Description("Register a React JSX File")
public class ReactDemoWebAppPlugin implements WebAppPlugin {
    public void init(WebApp app, ServletContext servletContext, Handler authenticationHandler) {
        // Register plugin to use extension registry
        app.registerJavaScript("/org/bigconnect/example/web/react-plugin.js");

        // Register React components
        app.registerJavaScriptComponent("/org/bigconnect/example/web/ReactDemo.jsx");
        app.registerJavaScriptComponent("/org/bigconnect/example/web/ReactDemoConfig.jsx");
    }
}
```

Next, we need to load our web plugin into the web server using service loading, so open up the `plugins/web/src/main/resources/META-INF/services/com.mware.web.WebAppPlugin` and add the line:

`org.bigconnect.example.web.ReactDemoWebAppPlugin`

so now that file is going to look like:

`org.bigconnect.example.web.ExampleWebAppPlugin org.bigconnect.example.web.SelectedVertexWebAppPlugin org.bigconnect.example.web.ReactDemoWebAppPlugin`

Create the following files:

* plugins/web/src/main/resources/org/bigconnect/example/web/react-plugin.js:

```javascript
define(['public/v1/api'], function(api) {
    api.registry.registerExtension('org.bigconnect.web.dashboard.item', {
        title: 'React Demo',
        description: 'React dashboard card demo',
        identifier: 'org-bigconnect-example-web-react',

        // Note: Leave off the file extension as requirejs assumes ".js" which
        // is created at runtime.
        componentPath: 'org/bigconnect/example/web/ReactDemo',
        configurationPath: 'org/bigconnect/example/web/ReactDemoConfig'
    })
})
```

* plugins/web/src/main/resources/org/bigconnect/example/web/ReactDemo.jsx:

```javascript
// 'react', 'create-react-class', and 'prop-types' come from RequireJS.
define(['create-react-class'], function(createReactClass) {
    const ReactDemo = createReactClass({
        render() {
            const { item } = this.props;
            const { configuration } = item;
            const { val = 'Not Set' } = configuration
            return (<div>
                <h1>Hello Dashboard Card with React</h1>
                <h2>Config = {val}</h2>
            </div>);
        }
    })

    return ReactDemo;
})
```

* plugins/web/src/main/resources/com/bigconnect/example/web/ReactDemoConfig.jsx:

```javascript
define(['create-react-class', 'prop-types'], function(createReactClass, PropTypes) {
    const ReactDemoConfig = createReactClass({
        propTypes: {
            item: PropTypes.shape({
                configuration: PropTypes.object.isRequired
            }).isRequired,
            extension: PropTypes.object.isRequired
        },
        render() {
            const { item } = this.props;
            const { configuration } = item;
            const { val = 'Not Set' } = configuration
            return (<button onClick={this.onClick}>Config = {val}</button>);
        },
        onClick() {
            const { item, extension, configurationChanged } = this.props;
            const val = item.configuration.val || 0;
            const newConfig = {
                ...item.configuration,
                val: val + 1
            };
            configurationChanged({ item: { ...item, configuration: newConfig }, extension: extension });
        }
    })

    return ReactDemoConfig;
})
```

All JSX components will be compiled using Babel. `ReactDemo.jsx` will compile to `ReactDemo.js` and create a SourceMap at `ReactDemo.src.js`.





