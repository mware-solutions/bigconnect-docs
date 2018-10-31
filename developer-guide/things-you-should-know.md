# Things you should know

Before starting to browse the source code, customize the platform or develop plugins, you should first know how BigConnect actually works. After finishing this section you should have a thorough understanding of the platform's inner-workings and its components.

## Architecture

A high level overview of a production architecture is presented below. This is an ideal deployment scenario, with dedicated machines for each Web Console, Data Workers, Job Workers, Queues and Data Ingestion.

![](../.gitbook/assets/architecture.png)

There are three types of BigConnect nodes that can be run:

* **Data Worker** nodes usually handle data enrichment. 
* **Job Worker** nodes usually handle long running processes \(such as running Machine Learning models or Graph algorithms\) or scheduled tasks.
* **Web Console** nodes serve the Web Console application

Each BigConnect running node can be a combination of one or more types. This means that in a very simple deployment, one running node can be used to serve the Web Console, to run data worker plugins and job worker plugins, whereas in a typical production deployment you will have nodes that handle specific tasks and are also distributed across many machines.

Because nodes need to talk to each-other, each node instance can connect to a WorkQueue to publish and subscribe to messages. The current implementation offers an in-memory work queue for simple deployments and a RabbitMQ work queue for production deployments. Other types of WorkQueues can be easily implemented as well \(eg. Apache Kafka, AWS Simple Queue Service etc.\).

{% hint style="info" %}
All three node types share a common code base that also includes additional useful services like LDAP authentication, SMTP for sending mails \(notifications\), notifications, alerts, JMX metrics, geocoding etc.
{% endhint %}

## The Graph Engine

Handles the persistence of data and provides operations for adding, updating, deleting and querying stored data. It provides a graph-like structure based on **Vertices**, **Edges** and _**multi-valued**_ **Properties** that can be applied to Vertices or Edges. 

Property Graphs have been around for a while and the technology offering has grown consistently in the last years. BigConnect however brings two new important features: **multi-valued properties** and **fine-grained security**.

It makes a clear distinction between storing data and querying data, because not all storage containers can also retrieve the data in a timely manner. This is a common architecture for Property Graphs where a search index is used to perform fast queries.

The current implementation uses Apache Accumulo for storage and ElasticSearch for searching. 

{% hint style="info" %}
The Graph Engine itself is an abstract component and provides a Blueprints-like API, so other implementations can be added for storage and search. The code base clearly separates the interfaces that should be implemented for a new technology.
{% endhint %}

## Security

An important feature of the Graph Engine is that it includes a layer of fine-grained data security: every operation on the data structure is made using a set of Authorizations and each piece of data has a Visibility label attached to it. 

The Visibility is used to determine whether a given user meets the security requirements to read the value.  This enables data of various security levels to be stored in the same element \(vertex or edge\) and users of varying degrees of access to query the data, while preserving data confidentiality.

When changes to the graph are made, users can specify a visibility label for each value. These labels consist of a set of user-defined tokens that are required to read the value the label is associated with.  The set of tokens required can be specified using syntax that supports logical AND `&` and OR `|` combinations of terms, as well as nesting groups `()` of terms together.

Each term is comprised of one to many alpha-numeric characters, hyphens, underscores or periods. Optionally, each term may be wrapped in quotation marks which removes the restriction on valid characters. In quoted terms, quotation marks and backslash characters can be used as characters in the term by escaping them with a backslash.

For example, suppose within our organization we want to label our data values with security labels defined in terms of user roles. We might have tokens such as:

```text
admin
audit
system
```

These can be specified alone or combined using logical operators:

```text
// Users must have admin privileges
admin

// Users must have admin and audit privileges
admin&audit

// Users with either admin or audit privileges
admin|audit

// Users must have audit and one or both of admin or system
(admin|system)&audit
```

When both `|` and `&` operators are used, parentheses must be used to specify precedence of the operators.

When clients attempt to read data from BigConnect,  any security labels present are examined against the set of authorizations passed by the client code.  If the authorizations are determined to be insufficient to satisfy the visibility label, the value is suppressed from the set of results sent back to the client.

Authorizations are specified as a comma-separated list of tokens the user possesses:

```text
Authorization auths = new Authorization("admin","system");
```

