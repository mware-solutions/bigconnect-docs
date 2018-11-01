# Creating a standard plugin

## First steps

Lets create a new web app plugin so that we can dive into more detail on how the web app plugins are designed. To get started, first clone the [`bigconnect-plugin-template`](https://github.com/mware-solutions/bigconnect-plugin-template) project:

```text
git clone https://github.com/mware-solutions/bigconnect-plugin-template.git
```

We will be working in the `plugins/web` folder in the cloned project, so create a java file at `plugins/web/src/main/java/org/bigconnect/example/web/SelectedVertexWebAppPlugin.java` with the following content:

```java
package org.bigconnect.example.web;

import com.google.inject.Singleton;
import com.mware.web.framework.Handler;
import com.mware.core.model.Description;
import com.mware.core.model.Name;
import com.mware.web.WebApp;
import com.mware.web.WebAppPlugin;

import javax.servlet.ServletContext;

@Name("Selected Vertex Action Web App Plugin")
@Description("Registers a new menu item which will send back the vertex that it was clicked from and call some action on it")
@Singleton
public class SelectedVertexWebAppPlugin implements WebAppPlugin {
    @Override
    public void init(WebApp app, ServletContext servletContext, Handler authenticationHandler) {

    }
}  
```

This class is responsible for telling the web server exactly which files it will be serving and in which context it will be serving them. Next, we need to load our web plugin into the web server using service loading, so open up`plugins/web/src/main/resources/META-INF/services/com.mware.web.WebAppPlugin` and add the line:

`org.bigconnect.example.web.SelectedVertexWebAppPlugin`

so now that file is going to look like this:

`org.bigconnect.example.web.ExampleWebAppPlugin org.bigconnect.example.web.SelectedVertexWebAppPlugin`

Lets just make sure that the web app plugin appears in the application. At the root of your repository, run `mvn clean package` and then run `./run.sh`. When the application comes up, enter the application using `admin/admin` and look at the list of plugins in the admin pane. Under the webapp directory, you should see that you have an entry for the web plugin that you just made. 

Congratulations! You successfully made a web plugin, but of course, it doesn't do anything.

## **Making the web plugin do something**

There are two possible parts to each web plugin, the back-end code which will handle any web requests and the front-end code which will add to BigConnect's Web Console. We will be adding a little bit of both. To start, let's create the menu item on the front-end.

To start, we need to create the files that will be served by BigConnect and then tell the web server to serve them up. Create a file `plugins/web/src/main/resources/org/bigconnect/example/web/selectedvertexplugin.js` with the following content:

```javascript
require([
    'public/v1/api'
], function (
    api
) {
    'use strict';

    //register the extension to the registry.  
    //BigConnect will query the registry to know what plugins to run.
    //This code will create a new menu in the context menu and, when clicked, 
    // will launch the 'doAction' event through the dom thanks to flight.
    api.registry.registerExtension('org.bigconnect.vertex.menu', {
        label: 'Do Action',
        event: 'doAction'
    });

    //create an handle for the doAction event that gets created from above.
    $(document).on('doAction', function(e, data) {
        //output the fact that there was an event and the parameters to the console.
        console.log("action happened", e, data);
    });
});
```

We are almost done. Now, we need to tell the web server that we want this JavaScript file to be served on every page. Open up the `plugins/web/src/main/java/org/bigconnect/example/web/SelectedVertexWebAppPlugin.java` and add the following line into the init method:

```java
app.registerJavaScript("/org/bigconnect/example/web/selectedvertexplugin.js", true);
```

Now we are telling BigConnect where to load the JavaScript code from. It will be on the classpath since the `selectedvertexplugin.js` file is bundled with the war. By setting the last boolean parameter to true, we are telling it that we want the JavaScript file included in the page when the page loads. Loading the script on the page is the correct thing to do when the page loads so that our plugin can be registered, but you don't typically want too many things to be loaded on the page because it will make startup slower. Try to defer loading of resources as long as possible.

Now your web plugin class should like the following:

```java
package org.bigconnect.example.web;

import com.google.inject.Singleton;
import com.mware.web.framework.Handler;
import com.mware.core.model.Description;
import com.mware.core.model.Name;
import com.mware.web.WebApp;
import com.mware.web.WebAppPlugin;

import javax.servlet.ServletContext;

@Name("Selected Vertex Action Web App Plugin")
@Description("Registers a new menu item which will send back the vertex that it was clicked from and call some action on it")
@Singleton
public class SelectedVertexWebAppPlugin implements WebAppPlugin {
    @Override
    public void init(WebApp app, ServletContext servletContext, Handler authenticationHandler) {
        app.registerJavaScript("/org/bigconnect/example/web/selectedvertexplugin.js", true);
    }
}
```

Go to the root of your project and run `mvn clean package && ./run.sh` again and reload the web browser to see the changes that we made. Create a vertex and right click on it; you should see a menu item that says 'Do Action' inside of it. Make sure your browser JavaScript console is open and then simply click on the menu item. You will see a "`action happened`" message displayed the console.

Congratulations! We now have a front-end-only web plugin which is pretty good. But what we really want to do is be able to communicate from the front-end to the back-end and do something with the response on the front-end. 

In the next steps we will create and use a web-worker to send an ajax request to the server, do something with it, and receive back some data on the front end.

## Communicate with the server

To start, let's create an endpoint that we can have the ajax request hit. For right now, we are just going to `System.out.printf` the information that we get receive from the front end.

To do that, we need to register an endpoint to let the front end hit it, so lets create the callback first. Create a java file at `plugins/web/src/main/java/org/bigconnect/example/web/SelectedVertexAction.java` with the following content:

```java
package org.bigconnect.example.web;

import com.mware.core.model.clientapi.dto.ClientApiObject;
import com.mware.web.framework.ParameterizedHandler;
import com.mware.web.framework.annotations.Handle;
import com.mware.web.framework.annotations.Required;
import com.mware.web.model.ClientApiSuccess;

public class SelectedVertexAction implements ParameterizedHandler {
    @Handle
    public ClientApiObject handle(
            @Required(name = "vertexId") String vertexId
    ) {
        System.out.printf("Received a post with %s as the specified vertexId\n", vertexId);
        return new ClientApiSuccess();
    }
}
```

This class will output the vertex that was clicked on and return a success message. Next, we need to register this class with BigConnect, so open up the `SelectedVertexWebAppPlugin` class and add the following line:

```java
app.post("/selected", authenticationHandler.getClass(), BcCsrfHandler.class, SelectedVertexAction.class);
```

to the `init` method. There are a couple of parameters that you don't have to worry about right now that are a part of the method call, but the most important parameter is the `SelectedVertexAction.class` parameter which references the class that will handle the web request.

Unfortunately we aren't done here. We need to write the web worker that will actually call that endpoint. Create a file `plugins/web/src/main/resources/org/bigconnect/example/web/selectedvertexwebworker.js` with the following content:

```javascript
define('data/web-worker/services/selectedvertex', [
    'public/v1/workerApi'
], function(workerApi) {
    var ajax = workerApi.ajax;
    'use strict';
    return {
        selected: function(vertexId) {
            return ajax('POST', '/selected', { vertexId: vertexId });
        }
    }
})
```

We will also need to register our new web-worker in the `SelectedVertexWebAppPlugin` class, so append the following line at the end of the `init` method.

```java
app.registerWebWorkerJavaScript("/org/bigconnect/example/web/selectedvertexwebworker.js");
```

This web-worker is the data layer between the REST API and JavaScript code. It will run inside of a promise that will allow the code that calls it to use the information that is returned by the AJAX request. Since we are currently returning `ClientApiSuccess`, it will only return a `true` value wrapped in a JSON object, but it will become more important to pass the data back when we are doing more complicated things on the server.

Only one more thing to do. Make the web-worker do the REST API call. Change the code in `plugins/web/src/main/resources/org/bigconnect/example/web/selectedvertexplugin.js` to look like the following:

```javascript
require([
    'public/v1/api'
], function (
    api
) {
    'use strict';

    api.registry.registerExtension('org.bigconnect.vertex.menu', {
        label: 'Do Action',
        event: 'doAction'
    });

    $(document).on('doAction', function(e, data) {
        //require the data object necessary to do the ajax request
        api.connect().then(function(connectApi) {

            //do the ajax request.  Notice how 'selectedvertex' is defined at the top of the web worker and 'selected' is the method name in the web worker
            connectApi
                .dataRequest('selectedvertex', 'selected', data.vertexId)
                .then(function(response) {
                    //just console.log the result
                    console.log("got response back from the server!", response);
                });
        });
    });
});
```

Save that file and run `mvn clean package && ./run.sh` in your project. Right click on a vertex in the graph and click on the 'Do Action' menu. On the server you will see a message that looks like "Received a post with as the specified vertexId", and in your browser console you will see something that looks like `got response back from the server! Object {success: true}`

## More back-end work

Typically, you are going to want to do something on the back-end and send the results to the front-end. To demonstrate this concept, we have to make some changes to what happens in the route that we just made. Change the `SelectedVertexAction.java` file to look like the following:

```java
package org.bigconnect.example.web;

import com.google.inject.Inject;
import com.mware.core.model.clientapi.dto.ClientApiObject;
import com.mware.core.model.workQueue.WorkQueueRepository;
import com.mware.ge.Authorizations;
import com.mware.ge.Graph;
import com.mware.ge.Vertex;
import com.mware.web.framework.ParameterizedHandler;
import com.mware.web.framework.annotations.Handle;
import com.mware.web.framework.annotations.Required;
import com.mware.web.model.ClientApiSuccess;
import com.mware.web.parameterProviders.ActiveWorkspaceId;

import java.text.SimpleDateFormat;
import java.util.Date;

public class SelectedVertexAction implements ParameterizedHandler {
    private Graph graph;
    private WorkQueueRepository repository;

    @Inject
    public SelectedVertexAction(Graph graph, WorkQueueRepository repository) {
        this.graph = graph;
        this.repository = repository;
    }

    @Handle
    public ClientApiObject handle(
            @Required(name = "vertexId") String vertexId,
            @ActiveWorkspaceId(required = false) String workspaceId,
            Authorizations authorizations
    ) {
        //get the vertex that was sent back from the front end
        Vertex v = graph.getVertex(vertexId, authorizations);

        //put the current date and time into the string
        String format = new SimpleDateFormat().format(new Date());

        //make a new title for the vertex
        final String newName = String.format("Action (%s)", format);

        //set the property on the vertex
        v.setProperty("helloworld#fullName", newName, v.getVisibility(), authorizations);

        // make sure that the changes are persisted into the graph
        this.graph.flush();

        //tell the case that the vertex has changed so it needs to be reloaded
        this.repository.broadcastElement(v, workspaceId);

        return new ClientApiSuccess();
    }
}
```

This code will now change the name of the vertex that the menu item was opened on to "Action \(_current time_\)". 

Note that in this example we didn't send the information back to the front-end through the web request. We instead broadcasted a message to the front-end saying the vertex has changed and needs to be reloaded.

Let's send the new title to the front-end and have it create a simple JavaScript alert box. At the end of the `handle` method, instead of returning the `ClientApiSuccess` object return:

```java
return new ClientApiObject() {
    public String getNewName(){
        return newName;
    }
};
```

Now we are passing the message back to the front-end instead of just passing back a success message. Go into the `selectedvertexplugin.js` file and add the following instead of the console message:

```javascript
 alert("Vertex " + data.vertexId + " title changed to " + response.newName);
```

The new name of the element will be passed back to the front-end and a JavaScript alert will be shown to the user.

## Conclusion

This tutorial summarized the basic steps of creating a web plugin. We created a plugin that can exchange data between the front-end and the back-end. Using these simple concepts, it's now possible to build more complicated features.

