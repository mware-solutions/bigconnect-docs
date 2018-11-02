# External Worker Plugins

BigConnect can ingest any kind of data but sometimes that ingestion process is not as straightforward as dragging data onto BigConnect's UI or creating a simple connector to hit a rest API endpoint. External resource workers run tasks asynchronously using BigConnect's resources so that more complicated tasks or tasks that take a long time to complete will not interfere with other parts of the architecture.

Consider the following example:

You want to ingest all of the data from a specific rest end point into BigConnect, but you are rate-limited by those API calls. It is possible to write a command line tool to handle the data import, but you want a managed solution that works while BigConnect is running and can be monitored from the BigConnect UI.

In that instance, you may want to consider an external resource worker. It can automatically hit the API end-point, run as fast as the rate limit is, and then pause execution as is needed.

## Development

There are only two methods that you must implement when creating an external resource worker, [`run`](http://localhost/java/com/mware/core/externalResource/ExternalResourceWorker.html#run--)and [`stop`](http://localhost/java/com/mware/core/externalResource/ExternalResourceWorker.html#stop--). The abstraction over these external process workers are extremely simple to support many possible use cases. A provided helper class called [`QueueExternalResourceWorker`](https://github.com/mware-solutions/bigconnect/blob/master/bc-common/src/main/java/com/mware/core/externalResource/QueueExternalResourceWorker.java) will support some default functionality that will de-queue data from a queue and process it.

## Deployment

External resource workers are run inside the web server when it is brought online. They are injected through the service loader and then their lifecycle is started when their start method is called. To ensure that an ExternalResourceWorker is loaded on the classpath, it must be on the classpath and be referenced in a file `com.mware.core.externalResource.ExternalResourceWorker` in the `META-INF/services` folder in the jar.

If you do not want to run ExternalResourceWorkers inside of the server, add the following to your configuration.

```text
#disable the External Resource Workers from running inside of the web server
disable.com.mware.core.process.ExternalResourceRunnerProcess=true
```

