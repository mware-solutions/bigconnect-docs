# First Steps

This section includes information on how to setup BigConnect and get it running, including:

* Downloading
* Installing
* Starting
* Configuring

## Flavors

BigConnect comes in two packaging options:

* **Hadoop** - bundled with single-node Hadoop, Accumulo, ElasticSearch and ZooKeeper. This packaging is only available on Linux and MacOS.
* **Lite** - bundled with a RocksDB database and ElasticSearch for desktop use. This packaging is available on Linux, MacOS and Windows.

{% hint style="warning" %}
Both flavors can be used to test-drive and develop on top of BigConnect and are not intended for production use !   
  
Production deployments should be carefully planned in terms of resiliency, performance, availability and scalability. While you can do this yourself, we highly recommend getting in touch or using a commercially supported version of BigConnect.
{% endhint %}

## Supported Platforms

Installation packages are provided for and tested against Linux, MacOS X, and Windows. Since BigConnect runs on Java, Hadoop, Accumulo and ElasticSearch, we also include the necessary binaries and files for these platforms. Running BigConnect against other versions of the included binaries is not supported.

## Versions

The supplied version of BigConnect is tested and configured to run on top of the following services:

* Apache Hadoop 2.9.1
* Apache Accumulo 1.9.2
* Apache ZooKeeper 3.4.13
* ElasticSearch 5.6.10

Running on earlier versions of Apache Hadoop \(2.3+\) and Accumulo \(1.6+\)  should work, but please check with support to see if your configuration is supported.

{% hint style="success" %}
We also support running on [Cloudera](https://www.cloudera.com/), [HortonWorks](https://hortonworks.com/) and [MapR](https://mapr.com/) distributions of Hadoop.

Running on earlier versions of Apache Hadoop \(2.3+\) and Accumulo \(1.6+\)  should work, but please check with support to see if your configuration is supported.
{% endhint %}





