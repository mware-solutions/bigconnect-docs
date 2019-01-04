# Starting and stopping

Starting and stopping the platform can be done using the bundled `bigconnect.sh` script available in the root of your `$BIGCONNECT_DIR` folder.

## BigConnect Lite package

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

To completely wipe-out the data and start fresh, please delete the `datastore` folder.

{% hint style="danger" %}
By removing the `datastore` folder will lose all you BigConnect data !
{% endhint %}





