# Creating a plugin

## Prerequisites

There will be some development in this tutorial so we recommend that you at least be familiar with the command line, maven and a java IDE that works with maven \(e.g. IntelliJ\).

## Background

One of the major parts of BigConnect is the system of Data Workers that enhance and analyze the data. Since most organizations are going to have different use-cases and needs for working with the data, we designed the Data Workers to be as pluggable as possible.

## Skeleton

```java
package com.mware.helloworld.worker;

import com.mware.ge.Element;
import com.mware.ge.Property;
import com.mware.core.ingest.dataworker.DataWorkerData;
import com.mware.core.ingest.dataworker.DataWorker;

import java.io.InputStream;

public class HelloWorldDataWorker extends DataWorker {
    @Override
    public boolean isHandled(Element element, Property property) {
        return false;
    }

    @Override
    public void execute(InputStream inputStream, DataWorkerData dataWorkerData) throws Exception {

    }
}
```

In order to load it on the classpath, we need to modify the services file in the resources directory:

Go into the `worker/src/main/resources/META-INF/services` directory and add the `HelloWorldDataWorker` line to the file `com.mware.core.ingest.dataworker.DataWorker`. to look like this:

```text
com.mware.helloworld.worker.ExampleDataWorker
com.mware.helloworld.worker.HelloWorldDataWorker
```

Go back to the root of your project and run `mvn clean package`, then add the generated jar to your classpath. Go back to the admin pane, check the list of plugins, and expand the Data Worker drop down. You will now see your data worker in the list of plugins. Unfortunately, there isn't a nice name for it or a description, so let us add one.  
Add the following annotations to the class \(look at the example data worker for a reference\) and import the correct classes.

```java
import com.mware.core.model.Description;
import com.mware.core.model.Name;

@Name("My Hello World Data Worker")
@Description("Sets the title of every person vertex to Hello World")
```

Run `mvn clean package`, then copy the jar to the classpath again. You will see the name and description update in the admin plugin list.

## Adding Functionality

Right now we have a skeleton of a data worker, but it doesn't do anything. As it stands, it only tells the entire data worker framework that it can't work on anything because it returns false every time the `isHandled` method is called. We want to enable it to do whatever it needs to do. Ctrl+C out of the web server then change the `isHandled` method to return `element instanceof Vertex` and import the correct classes.

Now your code should look like:

```java
// ... imports omitted
@Name("My Hello World Data Worker")
@Description("Sets the title of every person vertex to Hello World")
public class HelloWorldDataWorker extends DataWorker {
    @Override
    public boolean isHandled(Element element, Property property) {
        return element instanceof Vertex;
    }

    @Override
    public void execute(InputStream inputStream, DataWorkerData dataWorkerData) throws Exception {

    }
}
```

But that isn't all. We only want to deal with people vertices, not every vertex is going to have a full name property. To do that, we are going to need put one more boolean statement inside of the `isHandled` method.

```text
"person".equals(BcProperties.CONCEPT_TYPE.getPropertyValue(element));
```

Your HelloWorldDataWorker should now look like this:

```java
@Name("My Hello World Data Worker")
@Description("Sets the title of every person vertex to Hello World")
public class HelloWorldDataWorker extends DataWorker {
    @Override
    public boolean isHandled(Element element, Property property) {
        return element instanceof Vertex && "person".equals(BcProperties.CONCEPT_TYPE.getPropertyValue(element));
    }

    @Override
    public void execute(InputStream inputStream, DataWorkerData dataWorkerData) throws Exception {
    }
}
```

Now we will get only the person vertices in the execute method, but we still aren't doing anything with them. Add the following lines to the execute method to actually do the grunt work of setting the person vertices names to Hello World.

```java
// gets the vertex from the data object that is passed in
Vertex v = (Vertex) dataWorkerData.getElement();

// sets the property on the vertex, using the visibility of the vertex and the authorizations of the graph property worker
v.setProperty("fullName", "Hello World", v.getVisibility(), getAuthorizations());

// flush the changes to the graph
getGraph().flush();

// notify the UI and future workers that there was a change to the data
getWorkQueueRepository().pushGraphPropertyQueue(v, "", "fullName", Priority.NORMAL);
```

Since we now are changing the properties, we need to make sure that the data worker won't change the title to "Hello World" continually. Add the following boolean condition to the `isHandled` method

```java
!"Hello World".equals(element.getPropertyValue("fullName"))
```

Your HelloWorldDataWorker class now looks like:

```java
package com.mware.helloworld.worker;

import com.mware.ge.Element;
import com.mware.ge.Property;
import com.mware.ge.Vertex;

import com.mware.core.ingest.dataworker.DataWorkerData;
import com.mware.core.ingest.dataworker.DataWorker;
import com.mware.core.model.Description;
import com.mware.core.model.Name;
import com.mware.core.model.properties.BcProperties;
import com.mware.core.model.workQueue.Priority;

import java.io.InputStream;

@Name("My Hello World Data Worker")
@Description("Sets the title of every person vertex to Hello World")
public class HelloWorldDataWorker extends DataWorker {
    @Override
    public boolean isHandled(Element element, Property property) {
        return element instanceof Vertex &&
               "person".equals(BcProperties.CONCEPT_TYPE.getPropertyValue(element)) &&
               !"Hello World".equals(element.getPropertyValue("fullName"));
    }

    @Override
    public void execute(InputStream inputStream, DataWorkerData dataWorkerData) throws Exception {
        Vertex v = (Vertex)graphPropertyWorkData.getElement();
        v.setProperty("fullName", "Hello World", v.getVisibility(), getAuthorizations());
        getGraph().flush();
        getWorkQueueRepository().pushGraphPropertyQueue(v, "", "fullName", Priority.NORMAL);
    }
}
```

Go to your project root and run `mvn clean package` and then deploy the jar again. Restart you app.

We need to test out that the data worker actually makes the changes that we intend. To do that, we are going to add an entity to the graph and watch it's name change.

1. Click on Graph in the right hand side of the screen
2. Right click on the graph.
3. Click New Entity...
4. In the concept type text box, type Person and hit enter
5. Click on Create

You will see a person get added to the graph, and their name quickly change from "No Title Available" to "Hello World".

