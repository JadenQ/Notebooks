### Spark and Scala

#### 1. Start Spark

```shell
spark-shell --master yarn --num-executors 5 --executor-memory 2G --executor-cores 2 ---conf spark.dynamicAllocation.enabled=false
```

Go to yarn, each has 5 executors, each executor has 2G memories and each executor has 2 CPU cores.

#### 2. Scala

- Object Oriented and Functional Programming

##### 1. SparkContext

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

Stop SparkContext: 

```
sc.stop()
```

##### 2. textFile

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

##### 3. Print few lines in RDD

```
myRDD.take(n).foreach(println)
```

##### 4. Drop duplicates

Duplicate rows could be remove or drop from Spark SQL DataFrame **using distinct() and dropDuplicates() functions**, distinct() can be used to remove rows that have the same values on all columns whereas dropDuplicates() can be used to remove rows that have the same values on multiple selected columns.

##### 5. Run Scala Script

###### 1. Install sbt

```shell
sudo apt-get update
sudo apt-get install apt-transport-https curl gnupg -yqq
echo "deb https://repo.scala-sbt.org/scalasbt/debian all main" | sudo tee /etc/apt/sources.list.d/sbt.list
echo "deb https://repo.scala-sbt.org/scalasbt/debian /" | sudo tee /etc/apt/sources.list.d/sbt_old.list
curl -sL "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x2EE0EA64E40A89B84B2DF73499E82A75642AC823" | sudo -H gpg --no-default-keyring --keyring gnupg-ring:/etc/apt/trusted.gpg.d/scalasbt-release.gpg --import
sudo chmod 644 /etc/apt/trusted.gpg.d/scalasbt-release.gpg
sudo apt-get update
sudo apt-get install sbt
```

###### 2. [Compile Jar file](https://www.scala-sbt.org/1.x/docs/Installing-sbt-on-Linux.html)

Submit to Spark cluster: it can only submit jar file, I use sbt to get the jar file using my VM.

`build.sbt`

```shell
name := "PageRank"

version := "1.2-yarn"

scalaVersion := "2.11.8"

libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0"
```

```shell
sbt package
```

###### 3. Run on Spark

Run Spark on cluster.

```shell
spark-submit \
--master yarn \
--deploy-mode cluster \
--class pagerank pagerank_2.11-1.2-yarn.jar
```

`DEBUG MODE` To easily debug, I use the following script to run Scala script on Spark shell.

```shell
cat pagerank.scala | spark-shell
```