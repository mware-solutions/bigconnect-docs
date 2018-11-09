# Starting and stopping

Starting and stopping the platform can be done using the bundled `bigconnect.sh` script available in the root of your `$BIGCONNECT_DIR` folder.

## BigConnect Hadoop package

The `bigconnect.sh` script handles system life-cycle operations like:

* Initializing the Hadoop, Accumulo, ZooKeeper and ElasticSearch services
* Starting/stopping the services
* Starting/Stopping the BigConnect Web Console
* Starting the BigConnect Shell

### Running for the first time

The first thing you need to do is to initialize the bundled services that BigConnect depends on. This can be done as follows:

```bash
./bigconnect.sh init
```

The initialization procedure runs in the foreground and does some important things:

* Clears the data folders
* Formats the HDFS NameNode
* Initializes a fresh Accumulo instance
* Clears any ZooKeeper Accumulo entries

Basically the `init` command restores the system to its initial state, deleting any existing data.

### Starting BigConnect

BigConnect can be started from the command line as follows:

```bash
./bigconnect.sh start-services
./bigconnect.sh start
```

The `start-services` command will start the infrastructure services: Hadoop NameNode, Hadoop DataNode, Accumulo services, ZooKeeper and ElasticSearch.

The second command `start` will start the Web Console, Data Worker and Process Worker threads. 

If BigConnect fails to start for any reason, please check the log files in the `$BIGCONNECT_DIR/logs` folder

### Stopping BigConnect

In order to stop BigConnect from the command line, run:

```bash
./bigconnect.sh stop
./bigconnect.sh stop-services
```

{% hint style="danger" %}
The `stop-services` command will gracefully stop all supporting services. Abruptly interrupting the command while it's running or manually killing the processes may lead to data corruption !
{% endhint %}

### Resetting the data

To completely wipe-out the data and start fresh, please run:

```bash
./bigconnect.sh init
```

{% hint style="danger" %}
This command will completely delete all BigConnect saved data !
{% endhint %}

## BigConnect SQL package

The `bigconnect.sh` script handles system life-cycle operations like:

* Starting/stopping the services
* Starting the BigConnect Shell

There are no first-time tasks to do for the SQL package.

### Starting BigConnect

BigConnect can be started from the command line as follows:

```bash
./bigconnect.sh start
```

The `start` command will also start in-process services for H2 and ElasticSearch.

If BigConnect fails to start for any reason, please check the log files in the `$BIGCONNECT_DIR/logs` folder

### Stopping BigConnect

In order to stop BigConnect from the command line, run:

```bash
./bigconnect.sh stop
```

### Resetting the data

To completely wipe-out the data and start fresh, please run:

```bash
./bigconnect.sh init
```

{% hint style="danger" %}
This command will completely delete all BigConnect saved data !
{% endhint %}





