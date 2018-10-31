# Getting started

This guide is intended for those who wish to:

* Contribute code to BigConnect
* Create their own BigConnect plugins
* Create a custom build of BigConnect

In order to work with BigConnect as a developer, it's recommended that you are proficient with the following technologies:

* Java, since the entire back end is written in Java
* JavaScript, jQuery and ReactJS , since the UI is very JavaScript heavy

If that sounds like you, then please keep in mind the following conventions you'll encounter while reading this guide.

* Any reference to `$BIGCONNECT_DIR` refers to the base directory for BigConnect configuration. The most important subdirectory to this is the `config` directory, where BigConnect configuration properties files placed.

While we do recommend reading all the pages in this Developer Guide, the steps below are the fastest path to getting an instance of BigConnect up and running in no time.

## Prerequisites

You need to have Java 1.8 and Apache Maven 3.5.3+ installed.

## Build and run the project

The platform consists of several projects: 

* [bigconnect](https://github.com/mware-solutions/bigconnect)
* [dw-plugins](https://github.com/mware-solutions/dw-plugins)
* [bigconnect-web](https://github.com/mware-solutions/bigconnect-web.git)

First, clone the source code for all three GitHub repositories:

```text
git clone https://github.com/mware-solutions/bigconnect.git
git clone https://github.com/mware-solutions/dw-plugins.git
git clone https://github.com/mware-solutions/bigconnect-web.git
```

Second, build the _bigconnect_ and _dw-plugns_ projects. Go into each folder and do:

```text
mvn -DskipTests install
```

Third, change to the bigconnect-web folder and run the web application:

```text
mvn -Pbuild-webapp,dev-tomcat-run install
```

This will start the embedded Tomcat server with an embedded ElasticSearch node and H2  database. 

Once started, the web console will be available at [http://localhost:8888](http://localhost:8888)

