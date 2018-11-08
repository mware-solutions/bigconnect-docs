# Starting and stopping

Starting and stopping the platform can be done using the bundled `bigconnect.sh` script available in the root of your `$BIGCONNECT_DIR` folder.

The script handles system life-cycle operations like:

* Initializing the Hadoop, Accumulo, ZooKeeper and ElasticSearch services
* Starting/stopping the services
* Starting/Stopping the BigConnect Web Console
* Starting the BigConnect Shell

## Running for the first time

The first thing you need to do is to initialize the bundles services that BigConnect depends on. This can be done as follows:

```bash
./bigconnect.sh init
```

The initialization procedure runs in the foreground and prints messages to the standard output \(`stdout`\).









