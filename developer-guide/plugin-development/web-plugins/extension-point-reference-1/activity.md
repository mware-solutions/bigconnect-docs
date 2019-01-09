---
description: Add additional items to the activity panel
---

# Activity

[Example code](https://github.com/mware-solutions/doc-examples/tree/master/extension-activity)

Activity extension points allow plugins to add additional items to the activity panel \(opened via the gears menu bar icon.\) These rows can show the progress of a long-running process or a front-end task using start/stop events.

![](../../../../.gitbook/assets/image%20%2858%29.png)

This tutorial will create an example long-running process, and an activity item that shows its progress, along with a custom finished component. For details on creating the back-end long-running process, see the tutorial code link above.

## Create a web plugin

The web plugin registers the resources needed, and creates a route to start the process.

he `POST` route to start the activity includes some filters before the `StartExample` handler. These are run in order and protect the route based on request and session conditions:

* `authenticator`: Will only allow authenticated users
* `csrfProtector`: Prevent cross-site request forgery attacks. Should be placed on all requests
* `ReadPrivilegeFilter`: Will only allow users that have this privilege. [Other filters](https://github.com/mware-solutions/bigconnect-web/tree/master/web-base/src/main/java/com/mware/web/privilegeFilters) available in BigConnect

## Register Extension

Register the activity extension in the `plugin.js` file. The type provided should match the type of the custom `QueueItem`.

```java
app.registerJavaScript("/com/mware/examples/activity/plugin.js", true);
app.registerWebWorkerJavaScript("/com/mware/examples/activity/service.js");
app.registerJavaScriptComponent("/com/mware/examples/activity/Finished.jsx");
app.registerResourceBundle("/com/mware/examples/activity/messages.properties");
app.post("/com/mware/examples/activity/start", authenticator, csrfProtector, ReadPrivilegeFilter.class, StartExample.class);
```

[ActivityWebAppPlugin.java \(lines 21–28\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-activity/src/main/java/com/mware/examples/activity/ActivityWebAppPlugin.java#L21-L28)

## Internationalization

Add a message bundle key for the type of activity

```text
activity.tasks.type.com-mware-examples-activity=Example
```

[messages.properties \(line 1\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-activity/src/main/resources/com/mware/examples/activity/messages.properties#L1)

## **Finished Interface**

Define the component to render when the process is complete, this just calls `alert` with the process JSON. The button will look at home with the dismiss button if it has `btn btn-mini` class names.

```javascript
define(['react'], function(React) {
    const Finished = React.createClass({
        onClick() {
            alert(JSON.stringify(this.props.process, null, 2));
        },
        render() {
            return <button onClick={this.onClick} className="btn btn-mini">Test</button>
        }
    })
    return Finished;
})
```

[Finished.jsx \(lines 1–11\)](https://github.com/mware-solutions/doc-examples/blob/master/extension-activity/src/main/resources/com/mware/examples/activity/Finished.jsx#L1-L11)



