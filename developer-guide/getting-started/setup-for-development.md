# Setup for development

Before getting into how to develop on your machine, there are a few dependencies that need to be installed:

* JDK 1.8
* [Apache Maven](https://maven.apache.org/download.cgi) 3.5.3+
* [NodeJS ](https://nodejs.org/download/release/v8.12.0/)8.12+ \(only for web development\)
* [Yarn](https://yarnpkg.com/en/docs/install) package manager 1.10+ \(only for web development\)
* [ElasticSearch](https://www.elastic.co/downloads/past-releases/elasticsearch-5-6-10) 5.6.10

For working with BigConnect's code base, and IDE is highly recommended. We perform a lot of our development using the [IntelliJ IDEA](https://www.jetbrains.com/idea/) IDE, but other editors like Eclipse can be used as well.

Since all repositories are Maven projects, importing the project in your IDE should be straightforward. Just open the repository folder and the IDE should handle the rest.

## Setup

1. Install JDK 1.8 and Apache Maven. Optionally, make sure that the `mvn`command is added to your path.
2. Install NodeJS and the Yarn package manager \(optional\)
3. Install and run ElasticSearch using its default configuration
4. Install Grunt globally: `npm install -g grunt-cli` \(optional\)
5. Clone a GitHub repository

{% hint style="info" %}
NodeJS, Yarn and Grunt are required if you intent to do bigconnect-web frontend development or you want to create your custom web plugin.
{% endhint %}

To run the web console from your IDE, you need to open the bigconnect-web project and create a Run configuration. 

A Run configuration for IntelliJ IDEA is detailed in bigconnect-web's repository [README.md](https://github.com/mware-solutions/bigconnect-web/blob/master/README.md) file.

