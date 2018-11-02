# Creating a React plugin

Writing components in [React](https://facebook.github.io/react) is now the preferred way to extend the Web Console with custom features. Most extension points already support React, but check their [documentation](extension-point-reference/) to make sure.

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

## registerCompiledJavaScript

Recommended for complex interface plugins that have deeper component hierarchy.

**PROS**

* Files are compiled at build-time, so no server startup delay
* Allows use of custom transpile / babel settings
* Better performance, only one request is needed to load all dependencies
* Easier to include other build steps like linting, testing, etc.
* Identify compilation errors at build time

**CONS**

* Adds complexity to the build process, must configure Maven and WebPack

**Example**

This example will create a plugin that creates a new card that users can add to their dashboard.

All these files remain the same as previous example: `ReactDemo.jsx`, and `ReactDemoConfig.jsx`, but now we change `pom.xml`, `react-plugin.js`, and `ReactDemoWebAppPlugin.java`

First, lets create a `package.json` file to manage our plugins dependencies in our `plugins/web/src/main/resources/org/bigconnect/example/web` directory:

```javascript
{
  "name": "web",
  "version": "1.0.0",
  "main": "js/react-plugin.js",
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-plugin-transform-object-rest-spread": "^6.26.0",
    "babel-plugin-transform-react-display-name": "^6.25.0",
    "babel-plugin-transform-react-jsx": "^6.24.1",
    "babel-preset-es2015": "^6.24.1",
    "react": "^16.2.0",
    "webpack": "^3.10.0"
  },
  "dependencies": {
    "babel-loader": "^7.1.2"
  }
}
```

Run `yarn install`  in the `plugins/web/src/main/resources/org/bigconnect/example/web` directory.

Now create a  `.babelrc`file in the `plugins/web/src/main/resources/org/bigconnect/example/web` directory, with the following contents:

```javascript
{
  "plugins": [
    "transform-react-jsx",
    "transform-react-display-name",
    "transform-object-rest-spread",

    // Iterate all preset-2015 except commonjs
    "check-es2015-constants",
    "transform-es2015-arrow-functions",
    "transform-es2015-block-scoped-functions",
    "transform-es2015-block-scoping",
    "transform-es2015-classes",
    "transform-es2015-computed-properties",
    "transform-es2015-destructuring",
    "transform-es2015-duplicate-keys",
    "transform-es2015-for-of",
    "transform-es2015-function-name",
    "transform-es2015-literals",

    // Removed transform-es2015-modules-commonjs
    // Breaks files because of top-level this, breaks amd on front-end

    "transform-es2015-object-super",
    "transform-es2015-parameters",
    "transform-es2015-shorthand-properties",
    "transform-es2015-spread",
    "transform-es2015-sticky-regex",
    "transform-es2015-template-literals",
    "transform-es2015-typeof-symbol",
    "transform-es2015-unicode-regex",
    "transform-regenerator"
  ]
}
```

Create a webpack configuration file: `plugins/web/src/main/resources/org/bigconnect/example/web/webpack.config.js` with the following contents:

```javascript
// webpack.config.js
var path = require('path');
var webpack = require('webpack');
var BcAmdExternals = [
    'public/v1/api',
    'create-react-class'
].map(path => ({ [path]: { amd: path, commonjs2: false, commonjs: false }}));

module.exports = {
  entry: {
    ReactDemo: './ReactDemo.jsx',
    ReactDemoConfig: './ReactDemoConfig.jsx'
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js',
    library: '[name]',
    libraryTarget: 'umd',
  },
  externals: BcAmdExternals,
  resolve: {
    extensions: ['.js', '.jsx']
  },
  module: {
    rules: [
        {
            test: /\.jsx?$/,
            exclude: /(node_modules)/,
            use: [
              { loader: 'babel-loader' }
            ]
        }
    ]
  },
  devtool: 'source-map',
  plugins: [
    new webpack.optimize.UglifyJsPlugin({
        mangle: process.env.NODE_ENV !== 'development',
        compress: {
            drop_debugger: false,
            warnings: true
        }
    })
  ]
};
```

Try a build by running webpack from the `plugins/web/src/main/resources/org/bigconnect/example/web` directory:

```bash
node ./node_modules/webpack/bin/webpack.js
```

Now, lets change the plugin to register the compiled files. Edit the ReactDemoWebAppPlugin.java file to match the contents below:

```java
@Name("React Web Demo")
@Description("Register a React JSX File")
public class ReactDemoWebAppPlugin implements WebAppPlugin {
    public void init(WebApp app, ServletContext servletContext, Handler authenticationHandler) {
        // Register plugin to use extension registry
        // We don't use webpack for this file
        app.registerJavaScript("/org/bigconnect/example/web/react-plugin.js");

        // Register React components by pointing to the webpack compiled versions in dist folder
        app.registerCompiledJavaScript("/org/bigconnect/example/web/dist/ReactDemo.js");
        app.registerCompiledJavaScript("/org/bigconnect/example/web/dist/ReactDemoConfig.js");
    }
}
```

Change the `react-plugin.js` file to use compiled files:

```text
define(['public/v1/api'], function(api) {
    api.registry.registerExtension('org.bigconnect.web.dashboard.item', {
        title: 'React Demo',
        description: 'React dashboard card demo',
        identifier: 'org-bigconnect-example-web-react',

        // Note: Leave off the file extension as requirejs assumes ".js" which
        // is created at runtime.
        componentPath: 'org/bigconnect/example/web/dist/ReactDemo',
        configurationPath: 'org/bigconnect/example/web/dist/ReactDemoConfig'
    })
})
```

Finally, we need to integrate yarn and webpack into the maven build. In your plugin's `pom.xml`, add the following:

```markup
<build>
    <plugins>
        <plugin>
            <groupId>com.github.eirslett</groupId>
            <artifactId>frontend-maven-plugin</artifactId>
            <version>${plugin.frontend}</version>
            <configuration>
                <workingDirectory>src/main/resources/org/bigconnect/example/web</workingDirectory>
                <installDirectory>${frontend.installDirectory}</installDirectory>
            </configuration>
            <executions>
               <execution>
                   <id>yarn install</id>
                   <goals>
                       <goal>yarn</goal>
                   </goals>
                   <configuration>
                       <arguments>install --production=false</arguments>
                   </configuration>
               </execution>
               <execution>
                   <id>webpack build</id>
                   <goals>
                       <goal>webpack</goal>
                   </goals>
                   <phase>generate-resources</phase>
               </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

Now run `mvn compile` to test the build and you are ready to go.

