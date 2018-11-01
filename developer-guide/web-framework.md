# Web Framework

BigConnect has its own minimalistic, lightning-fast web framework. It provides a thin layer of fundamental web application features without adding much overhead. 

The framework provides routing and parameter binding. Routing refers to determining how an application responds to a client request to a particular endpoint, which is a URI \(or path\) and a specific HTTP request method \(GET, POST, and so on\). Each route can have one or more handler functions, which are executed when the route is matched.

Route definition takes the following structure: `app.METHOD (path, handlers)` where:

* `app` in an instance of [`com.mware.web.framework.App`](https://github.com/mware-solutions/bigconnect-web/blob/master/web-base/src/main/java/com/mware/web/framework/App.java)
* `METHOD` is an [HTTP request method](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods), in lowercase.
* `path` is a path on the server
* `handlers` are a list of classes to be called when the route is matched. Each class must implement the [`com.mware.web.framework.Handler`](https://github.com/mware-solutions/bigconnect-web/blob/master/web-base/src/main/java/com/mware/web/framework/Handler.java) or its sub-interfaces, like [`com.mware.web.framework.ParameterizedHandler`](https://github.com/mware-solutions/bigconnect-web/blob/master/web-base/src/main/java/com/mware/web/framework/ParameterizedHandler.java)\`\`

Route handlers need to satisfy one requirement: they need to have a method annotated with the [`Handle`](https://github.com/mware-solutions/bigconnect-web/blob/master/web-base/src/main/java/com/mware/web/framework/annotations/Handle.java) annotation. This method will be called by the framework once the route matches.

Handler methods can also have parameters that will be bound automatically to request parameters, based on their type or name. There are also two annotations available to bind request parameters:

* [`Optional`](https://github.com/mware-solutions/bigconnect-web/blob/master/web-base/src/main/java/com/mware/web/framework/annotations/Optional.java) - the parameter is optional 
* [`Required`](https://github.com/mware-solutions/bigconnect-web/blob/master/web-base/src/main/java/com/mware/web/framework/annotations/Required.java) - the parameter is required and the framework will throw an exception if it's not set in the incoming request





