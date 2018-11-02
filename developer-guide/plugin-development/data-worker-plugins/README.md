# Data Worker Plugins

**Data Workers** are designed for data enhancement and individual scoring analytics on each element or property of the graph. Data Workers will get notified of every change made to elements and properties in the system and allow other Data Workers to act on those changes. For example, the [PhoneNumberExtractorWorker](https://github.com/mware-solutions/dw-plugins/blob/master/phone-number-extractor/src/main/java/com/mware/gpw/phone/PhoneNumberExtractorWorker.java) analyzes each property of every element in the system and tries to determine if there is a phone number in the text. It then proposes that the phone number it found should be resolved to a concept that is defined in the ontology and broadcasts the changes to the UI.

The Data Workers follow the [blackboard design pattern](https://en.wikipedia.org/wiki/Blackboard_%28design_pattern%29) model. Each Data Worker notifies the thread that is running it that it can work on an element and an optional property. If the worker returns true from its `isHandled` method is called, then that Data Worker's `execute` method is called with additional data. The Data Worker is then able to contribute data or run operations on that specific element or property.

### Development <a id="development"></a>

For a bare-bone Data Worker you must implement two methods: the `execute` method and the `isHandled` method.

#### `isHandled` <a id="ishandled"></a>

This method must return true if the specific DataWorker can handle the element or property that is passed into that method. Otherwise the method return false and the execute method on the DataWorker will not be called.

#### `execute` <a id="execute"></a>

All work should be done inside of this method for every Data Worker. The InputStream parameter is only populated if the value that is retrieved from the vertex is a StreamingPropertyValue. The DataWorkerData object that gets passed in is a data object that encapsulates all of the information about the element and the context in which the Data Worker may need to consider. 

## Use Cases

### Data Enhancement

Since Data Workers look at the data on a per-element and per-property basis, data enhancement can be easily applied to each element. As an example, consider a video that is loaded into BigConnect by someone who is trying to discover if a specific person is in the video. The following steps can happen if the correct Data Workers are running and an appropriate ontology is created.

1. The file is uploaded to the server.
2. The server creates a vertex with the following properties set:
   * Raw property set to the bytes of the video file
   * FileName property set to the name of the video file
   * Concept type set to the raw concept
3. The vertex is queued and submitted to the Data Workers.
4. Every Data Worker sees the new vertex after it is de-queued by the runner and the MimeTypeExtractor Data Worker sees that it is a raw vertex and it can handle
5. The MimeTypeExtractor Data Worker sees that the raw bytes are a video and sets the concept type to video.
6. After the MimeTypeExtractor is done, it re-queues the vertex so that other Data Workers can see the changes that were made.
7. Every Data Worker sees the updated vertex from the queue and a Data Worker splits the video into separate images and re-queues the vertex.
8. Again, every data worker sees that video frames are now images on the vertex and a facial recognition Data Worker pulls out the people in each image and checks to see if the specific person is in the video.

### Analytics

Imagine we wanted to update a fraud score on a person vertex. We could write a Data  Worker which listens for any changes made to any person vertex and update that fraud score. Below is some pseudo code on how you might do that.

```java
public class PersonFraudScoreDW extends DataWorker {
  public boolean isHandled(Element element, Property property) {
    return isPersonVertex(element);
  }

  public void execute(InputStream in, DataWorkerData data) throws Exception {
    double fraudScore = calculateFraudScore(data.getElement(), data.getElement().getEdges());
    data.getElement().setProperty("fraudScore", fraudScore);
  }
}
```

## Deployment

Data Workers are deployed inside of the web server by default. This configuration works well for development and installations that do not need to scale up further than their web server. On large installations it may be required to move the Data Workers out of the web server. When each Data Worker starts up, they are all started inside of their own threads and a DataWorkerRunner coordinates each of them together. It is possible to run more than one set of Data Workers in the server which can be valuable if you are running on multi-core hardware. To add more Data Worker threads, ensure that the following is in your configuration:

```text
#Set number of graph property worker threads to 4
com.mware.core.process.DataWorkerRunnerProcess.threadcount=4
```

If you do not want the graph property workers running inside of the web server, add the following to your configuration.

```text
#disable the graph property workers running inside of the web server
disable.com.mware.core.process.DataWorkerRunnerProcess=true
```



