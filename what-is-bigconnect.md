# Feature Highlights

The platform, as a whole, can do a lot: data ingestion, data enrichment, discovery and analysis. It is completely extensible and can be extended to suit any use case, using common development languages like Java, JavaScript, React, CSS/LESS etc. 

Customizations are in the form of plugins that can be developed both for the back-end and the front-end.

## Dynamic Data Model

The core foundation of bigCONNECT is the dynamic, semantic data model. It represents the way you store, correlate and query all information and it’s a conceptual data model that expresses information in a factual way: objects, relations and attributes. 

Typically, the data model is used to provide meaning to the information stored in bigCONNECT, either at ingestion time or later on, during the life-cycle of the data. It can be defined in the beginning or any time later on, to adapt to any data structure, type or meaning. Any information stored in the system can be mapped to an object \(concept\), can have some relations and some attributes. 

Concepts can be anything you can think of: persons, vehicles, bank transactions, phone calls, equipment, companies, locations, events, network packets, log files etc. They are linked to each other using meaningful relations like “works at”, “lives in”, “has friend”, “is brother of”, “sent from IP”, “source file” etc.

Concepts and relations can also have attributes. For a company these can be the company name, formation date, address etc. For a person these can be its first name, last name, birth date, phone number, email address etc. For a “works at” relation we can have the “start date” and “end date” attributes to denote when the person started to work at a company. For a “sent from IP” relation we can have the “timestamp” and the “user” etc

## Ingestion

bigCONNECT can ingest and process any kind of information: databases, office documents, text files, XML files, HTML files, images, audio and video files or streams.

Data ingestion can be manual using the UI or automated using scalable ETL and streaming tools. We currently provide plugins for [Pentaho Data Integration](https://sourceforge.net/projects/pentaho/files/Data%20Integration/) \(now Hitachi Vantra\) and [Apache Nifi](https://nifi.apache.org/) in the open and plugins for [Oracle Data Integrator](https://www.oracle.com/middleware/technologies/data-integrator.html), [IBM DataStage](https://www.ibm.com/ro-en/marketplace/datastage), [Informatica PowerCenter](https://www.informatica.com/products/data-integration/powercenter.html) and [Microsoft SSIS](https://docs.microsoft.com/en-us/sql/integration-services/sql-server-integration-services?view=sql-server-2017) in the [Enterprise ](http://bigconnect.io)version.

Data ingestion is a visual process. Either by using BigConnect’s user interface or another 3rd party ETL tool, data ingestion and data model mapping will be done in an easy, visual way. This ensures lower error rates and high process maintainability.

![](.gitbook/assets/image%20%2811%29.png)

## Enrichment

Information enrichment is done using pluggable Data Workers. They receive objects, attributes and relations and can do further processing: compute additional attributes, create or alter the data model, call external services etc. Data Workers are plugins and don’t run in a particular order. Instead, they are all notified when something changes in the data model and can choose to take appropriate action. Some examples are: extracting text from documents, extracting entities from text, computing other attributes based on existing ones, running audio, image or video processing algorithms, applying machine learning or neural network models etc.

## Visual Interface

BigConnect provides an extensible integrated visual console where users can query and analyze the data. The console is collaborative, and all actions are taken inside a space. This means that all data that is added, modified, queried or analyzed can be contained to that space alone. Data changes that happen inside a space can be published to be available for everybody.

Spaces are collaborative, and users can be invited to a space and have different access rights within that space. Spaces can be created for particular needs, use cases or logical groupings.

### Search

[ElasticSearch ](www.elastic.co)is used behind the scenes to search through the data model and compute aggregations. This means that search results are delivered near-real-time and search queries can be as complex as you can imagine.

![](.gitbook/assets/image%20%2873%29.png)

The UI provides a Google-like search experience, enriched with filters, sorting, refinements and spatial queries. The search field supports the Lucene query syntax so you can use special things like wildcard \(\*\), question mark \(?\), keyword matching, phrase matching, grouping, boolean queries \(AND, OR\), fuzzy queries \(~\), proximity matching \(~4\), range searches \(amount:\[500 TO 800\]\) and boosts \(^\). 

Example:

```text
("foo bar" AND "quic*")^1.5 OR "q?ry" –"john"
```

A query builder is also provided to build complex boolean queries using a few clicks. Spatial queries can use the map to pinpoint the center and area of interest:  

![](.gitbook/assets/image%20%2852%29.png)

On top of classic faceted full-text search, more advanced queries can be performed using the Cypher query language, using the integrated Cypher query editor with autocomplete features:

```text
MATCH (nicole:Actor {name: 'Nicole Kidman'})-[:ACTED_IN]->(movie:Movie)
WHERE movie.year < 2015
RETURN movie
```

Search queries together with their filters can be saved for later reference or they can be used in aggregations.

### Items

Data items like concepts and relations can have a lot of information associated using model-defined attributes. The UI provides a unified way of managing the attributes of an item or a group of items in what we call the “Detail view”.

The Detail view contains all information about a selected object or a group of selected objects. You can change the attributes, attach an image, see assigned tags, navigate through its relations and execute relevant actions for the object \(ex. Delete, Add Watch, Export etc.\)

![](.gitbook/assets/image%20%2814%29.png)

If a group of objects is selected, the Detail view will display statistics about the common attributes of the selected objects.

![](.gitbook/assets/image%20%2851%29.png)

The Detail view is completely extendable to fit any needs using the UI plugin mechanism.

Besides the various objects that can be loaded into the platform, bigCONNECT has special support for some common item types: **text**, **image**, **audio** and **video**.

**Text items** have by definition some textual content that can be annotated by selecting pieces of text and marking them as new objects or relations. This is handy if you need to infer new relations from textual content such as mentioned persons, companies, locations, IP addresses or items found in a dictionary, detected by an NLP engine or that satisfy a certain regex pattern.

![](.gitbook/assets/image%20%2841%29.png)

  
**Images** have by definition an associated image. They can be text items as well, so they can have text attached to as well, from an OCR engine for example. In the same way as text items, the image can be annotated to mark relevant existing or objects. This can be done manually or automatically using Face Recognition or Object Detection software. Sample plugins are provided for Azure Cloud Computer Vision.

![](.gitbook/assets/image%20%284%29.png)

  
**Audio items** have by definition an associated audio track. bigCONNECT will show a player for these types of items to play the contents. Audio items can be text items as well, so they can have text attached, from a speech-to-text engine for example. Sample plugins are provided for speech-to-text using Google Cloud Speech API.

![](.gitbook/assets/image%20%2827%29.png)

**Video items** have by definition associated video content and can also be text items. Since a video can optionally have an audio track, a Data Worker can be used to split and OCR the video frames and the audio track to extract all relevant information from the video, such as text, found objects, people etc.

![](.gitbook/assets/image%20%2839%29.png)

### Dashboard

The visual console includes a dashboard where users are being taken upon login. Various widgets can be added or removed from the dashboard to bring to focus any relevant information.

![](.gitbook/assets/image%20%2837%29.png)

Saved searches can be displayed on the dashboard as visual aggregations, lists or tag clouds. The provided widgets also include different chart types and aggregations, alerts and found behaviors.

Widgets can be created by developers using the UI plugin mechanism.

### Data analysis

Data analysis is performed using analysis products. Out of the box, three products are provided: Graph, Map and Timeline, but one can create any kind of analysis product using the SDK.

Any number of analysis product instances can be added inside a space to facilitate analysis from multiple perspectives.

#### Graph

For analyzing the data in its core connected form, we provide an advanced graph tool that can be used to create connections, see how objects are related, find connections between objects and more.

![](.gitbook/assets/image%20%2867%29.png)

The graph is interactive, you can drag objects around, create new objects and relations and explore the entire data model.

Objects can be copied and pasted from anywhere onto the graph, external files can be dragged & dropped in to create new objects and various layout and ranking algorithms can be applied on the displayed data such as Page Rank, Betweenness Centrality, Closeness Centrality and Degree Centrality.

Finding how two objects are connected is quite easy as well. Just use the Find Path feature to find out.

![](.gitbook/assets/image%20%2834%29.png)

#### Map

Data with geo-spatial information associated \(latitude, longitude\) can be displayed on an interactive map with support for layers, annotations and aggregations.

![](.gitbook/assets/image%20%2857%29.png)

New objects can be created at specific points on the map and will automatically receive location information for future reference. Practical use cases can include defining POIs, locating events, cars, phone calls, network packets etc.

The map can be annotated using points, lines and polygons that can become themselves objects inside the system.

#### Timeline

The timeline is present on every screen and can display the distribution of items over time, if items have date/time attributes. Multiple date/time attributes can be displayed on the timeline and you can zoom-in/zoom-out to specific time periods and select the objects of interest.

![](.gitbook/assets/image%20%2838%29.png)

### Administration

The console includes administration capabilities for editing the semantic model, managing users and roles, defining behaviors and ingesting data from various data sources. The admin console is customizable and can be extended by creating 3rd party plugins.

## Behaviors

Multiple saved searches \(normal and Cypher queries\) can be grouped together to define a behavior. A score is provided for each query and a threshold for the entire behavior. All objects that satisfy the queries and are over the threshold will be compliant with the defined behavior.

## Alerting

Items of interest can be added to a watch list by choosing which attributes/relations to listed for changes. You will be notified once a change has been made to the respective items via email or the dashboard.

## Security

Security is enforced at the UI level and at the data level. At the UI level, security is achieved by users, roles and privileges that control what users can do in the console, both at a global level and at the space level.

At the data level, fine-grained access can be controlled up to the attribute level. Labels can be applied to items, attributes and relations to restrict access to sensitive information. Only users with the required authorizations can see the data items that have security labels associated. The authorizations and security labels can be user-defined to provide maximum flexibility and extensibility.

## Extensibility

BigConnect is designed to be highly extensible and customizable at all levels. Being an open-source platform, the code can be altered as-is or can be extended using plugins, which is the recommended way.

Please see the [Developer Guide]() for details on how to build the source code, what are the extension points and have a look at the sample plugins.

## Scalability

The platform is built on top of the latest Big Data technologies and can scale massively, from small clusters of three nodes to clusters of thousand of nodes.

The amount of data that can be ingested is unlimited and directly related to the number of employed cluster nodes. Performance benchmarks are available where a cluster of 1000 nodes can ingest 1 terabyte of simple records with speeds of up to 100 million records per second.

## Licensing

BigConnect is free to use and customize and will always be so. While you can use the open-source releases to build and run your systems, we generally don’t recommend you do so. The open-source code base is in continuous development and bugs can always sneak in at any level. This can be disastrous in a production environment as it can lead to data corruption or data loss. While we do provide recommendations for production deployments, we don’t provide any guarantee whatsoever.

We do instead provide highly tested, enterprise grade releases, with included premium support, that are being used in sensitive production environments. These releases are based on an annual subscription model.

Please contact us for more details.

