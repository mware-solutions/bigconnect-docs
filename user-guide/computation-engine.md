# Computation engine

Currently BigConnect uses internal data workers for data ingestion and data processing. The major drawback of the current implementation is that there is no cluster computing support in order to provider faster insights and analytics for the available data.

For solving this issue it has been decided to add  [Apache Spark](http://spark-project.org/) support for short and long running processes. As a quick mention [Apache Spark](http://spark-project.org/) is 100 times faster than Hadoop and 10 times faster than accessing data from disk.

In order to orchestrate the different Spark jobs that are spawned through BigConnect's rich interface it has been aligned to make use of the [Spark Jobserver](https://github.com/spark-jobserver/spark-jobserver). It provides a RESTful interface for submitting and managing [Apache Spark](http://spark-project.org/) jobs, jars, and job contexts and it is widely used by a lot of companies \( e.g. Netflix \). An important configuration setting is that one can choose to have a shared Spark Context or a new one per submitted job. Of course for improving response times one will opt for sharing the Spark Context if possible.

For our demo showcase a small cluster comprised of 1 master and 2 worker nodes is used and in front a single [Spark Jobserver ](https://github.com/spark-jobserver/spark-jobserver)instance. \( We currently use only 3 machines bc-node-1, bc-node2 and bc-node 3 \). bc-node-2 and bc-node-3 are worker nodes and bc-node-1 holds both the Spark master and the Spark Jobserver.

In a few words in order to have a working [Spark Jobserver](https://github.com/spark-jobserver/spark-jobserver) one can opt for the development mode or for the production one.

For development mode simply download the github repository and use the following commands:

**sbt**

**job-server/reStart** \( when the sbt console has finished fetching all dependencies and so on \)

If you install it on a server where you connect via ssh then you will definitely need a tool like tmux in order to have the jobserver running indefinitely. tmux basically can detach the current session and keep it running despite logging off. When coming back one can re-attach to the running session and continue from there.

So, simply wrap the commands above with: _tmux_ \( **before** \) and _CTRL+b d_ \(**after** \).

For production one needs to create a deployment. The steps are quite straightforward as explained in the official documentation.

1. Copy `config/local.sh.template` to `<environment>.sh` and edit as appropriate. NOTE: be sure to set SPARK\_VERSION if you need to compile against a different version.
2. Copy `config/shiro.ini.template` to `shiro.ini` and edit as appropriate. NOTE: only required when `authentication = on`
3. Copy `config/local.conf.template` to `<environment>.conf` and edit as appropriate.
4. `bin/server_deploy.sh <environment>` -- this packages the job server along with config files and pushes it to the remotes you have configured in `<environment>.sh`
5. On the remote server, start it in the deployed directory with `server_start.sh` and stop it with `server_stop.sh`

If you encounter weird exceptions like there is no spark.jobserver.JobServer class found then simply retry step 4 or use `server_package.sh`, manually untar and run from the unarchived folder.

Usually the jobserver github repository will be cloned on a development machine and deployed to the production machines via ssh. \( This is done automatically using the `server_deploy.sh`mentioned above \).

After successfully starting the jobserver one can start triggering spark jobs in 2 steps exemplified below from the official documentation:

#### WordCountExample walk-through

**Package Jar - Send to Server**

First, to package the test jar containing the WordCountExample: `sbt job-server-tests/package`. Then go ahead and start the job server using the instructions above.

Let's upload the jar:

```text
curl -X POST localhost:8090/binaries/test -H "Content-Type: application/java-archive" --data-binary @job-server-tests/target/scala-2.10/job-server-tests-$VER.jar
OK⏎
```

**Ad-hoc Mode - Single, Unrelated Jobs \(Transient Context\)**

The above jar is uploaded as app `test`. Next, let's start an ad-hoc word count job, meaning that the job server will create its own SparkContext, and return a job ID for subsequent querying:

```text
curl -d "input.string = a b c a b see" "localhost:8090/jobs?appName=test&classPath=spark.jobserver.WordCountExample"
{
  "duration": "Job not done yet",
  "classPath": "spark.jobserver.WordCountExample",
  "startTime": "2016-06-19T16:27:12.196+05:30",
  "context": "b7ea0eb5-spark.jobserver.WordCountExample",
  "status": "STARTED",
  "jobId": "5453779a-f004-45fc-a11d-a39dae0f9bf4"
}⏎
```

NOTE: If you want to feed in a text file config and POST using curl, you want the `--data-binary` option, otherwise curl will munge your line separator chars. Like:

```text
curl --data-binary @my-job-config.json "localhost:8090/jobs?appNam=..."
```

Please pay attention to the fact that in the latest version you might stumble upon the following issue with Adhoc contexts: for Java jobs you first have to create a context and only then you can submit a job. Otherwise you will get an error. \( you can track the progress [here](https://github.com/spark-jobserver/spark-jobserver/pull/1124) \)

For creating a context use the following command:

```text
curl -d "" 'localhost:8090/contexts/jcontext?context-factory=spark.jobserver.context.JavaSparkContextFactory'
```



