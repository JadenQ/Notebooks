### Spark and Scala

#### 1. Start Spark

```shell
spark-shell --master yarn --num-executors 5 --executor-memory 2G --executor-cores 2 ---conf spark.dynamicAllocation.enabled=false
```

Go to yarn, each has 5 executors, each executor has 2G memories and each executor has 2 CPU cores.

#### 2. Scala

- Object Oriented and Functional Programming

###### 1. SparkContext

```
public SparkContext(java.lang.String master,
            java.lang.String appName,
            java.lang.String sparkHome,
            scala.collection.Seq<java.lang.String> jars,
            scala.collection.Map<java.lang.String,java.lang.String> environment,
            scala.collection.Map<java.lang.String,scala.collection.Set<SplitInfo>> preferredNodeLocationData)

```

Alternative constructor that allows setting common Spark properties directly

- Parameters:
  	master - Cluster URL to connect to (e.g. mesos://host:port, spark://host:port, local[4]).
  	appName - A name for your application, to display on the cluster web UI.
  	sparkHome - Location where Spark is installed on cluster nodes.
  	jars - Collection of JARs to send to the cluster. These can be paths on the local file system or HDFS, HTTP, HTTPS, or FTP URLs.
  	environment - Environment variables to set on worker nodes.
  	preferredNodeLocationData - used in YARN mode to select nodes to launch containers on. Can be generated 	using org.apache.spark.scheduler.InputFormatInfo.computePreferredLocations from a list of input files or InputFormats for the application.

###### 2. textFile

```
public RDD<java.lang.String> textFile(java.lang.String path,
                             int minPartitions)
```

Read a text file from HDFS, a local file system (available on all nodes), or any Hadoop-supported file system URI, and return it as an RDD of Strings.

- **Parameters:**

  `path` - (undocumented)

  `minPartitions` - (undocumented)

- **Returns:**

  (undocumented)



Run Scala script

```

```

