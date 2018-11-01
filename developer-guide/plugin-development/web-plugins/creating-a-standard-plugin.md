# Creating a standard plugin

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

Congratulations! You successfully made a web plugin, but of course, it doesn't do anything

