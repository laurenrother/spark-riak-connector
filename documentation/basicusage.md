
## Set Up for Development

**?? I don't quite understand what the flow of To-Dos here is.**

This document will walk you through setting up your environment for development with the Spark-Riak connector.

If you have not yet downloaded, installed, and built the Spark-Riak connector, go [here](https://github.com/basho/spark-riak-connector/tree/master/documentation/download-and-install.md).

If you want to add the connector as a dependency to your application, go [here](https://github.com/basho/spark-riak-connector/tree/master/documentation/add-as-dependecy.md).

## Libraries 

Several .jars are produced when the Spark-Riak connector is built. 

* `spark-riak-connector-1.0.0.jar` - is the main connector jar, located in spark-riak-connector/target/.
* `spark-riak-connector-java-1.0.0.jar` - is required for developing Spark applications in Java, located in spark-riak-connector/target/.
* `guava-14.0.1.jar` - the connector depends on this library.
* `jackson-datatype-joda-2.2.2.jar` - the connector depends on this library.
* `joda-time-2.1.jar` - the connector depends on this library.
* `jackson-module-scala_2.10-2.4.4.jar` - the connector depends on this library.
* `jcommon-1.0.23.jar` - the connector depends on this library.
* `scala-java8-compat_2.10-0.3.0.jar` - the connector depends on this library.
* `dataplatform-riak-client-1.0.0.jar` - the connector depends on this library.

All of these libraries need to be referenced by your Spark application and accessible through the classpath of the driver program.
 
» Please see below code snippets in Scala, or explore the source code of bundled examples in Java and Scala. **?? for what?** « 
 
The following link can be used to manually download the [dataplatform-riak-client-1.0.0.jar](https://bintray.com/basho/data-platform/com.basho.riak/view)
from the Bintray repository. » **Why would I need to do this?** « 

To download `dataplatform-riak-client.jar` automatically during the build, the following repository should be added to pom.xml: » **If this is true, this chunk needs to be moved to the download/install doc.**

```xml
<repository>
    <id>bintray</id>
    <url>https://dl.bintray.com/basho/data-platform</url>
    <releases>
        <enabled>true</enabled>
    </releases>
    <snapshots>
        <enabled>false</enabled>
    </snapshots>
</repository>
```

All of the examples »**Things have moved around enough that I'm not sure which examples** «  are currently based on the 1.0.0 release of the connector. 
If your spark project uses maven, include the following dependency to enable Spark Riak Connector: » **When? At build? After?** « 

```xml
<dependencies>
    <dependency>
        <groupId>com.basho</groupId>
        <artifactId>spark-riak-connector</artifactId>
        <version>1.0.0</version>
    </dependency>
    ...
</dependencies>
```

If your Spark application is going to be written in Java, add the following dependency in addition to the one above:

```xml
<dependency>
    <groupId>com.basho</groupId>
    <artifactId>spark-riak-connector-java</artifactId>
    <version>1.0.0</version>
</dependency>
```


### Necessary imports
**?? Also, not sure what the to-do here is.**

```scala
import com.basho.riak.client.core.query.Namespace
import com.basho.riak.spark.rdd.RiakFunctions
import org.apache.spark.{SparkContext, SparkConf}
import com.basho.riak.spark._
```


## Configuration  

» **Where does this section come in? After the download/install? Is this preparation for development locally? Does someone adding the connector as a dependency in their app need to know this?** «

You can prepare `SparkContext` to connect to Riak by connecting your Spark application to Riak. You need to set certain options for the `SparkConf` object. These options are prefixed with `spark.` so they can be recognized
from the spark-shell and set within the $SPARK_HOME/conf/spark-default.conf.

You will need to set the below options for the `SparkConf` object:

Property name                                  | Description                                       | Default value      | Riak Type
-----------------------------------------------|---------------------------------------------------|--------------------|-------------
spark.riak.connection.host                     | IP:port of a Riak node protobuf interface         | 127.0.0.1:8087     | KV/TS
spark.riak.connections.min                     | Minimum number of parallel connections to Riak    | 10                 | KV/TS
spark.riak.connections.max                     | Maximum number of parallel connections to Riak    | 30                 | KV/TS
spark.riak.input.fetch-size                    | Number of keys to fetch in a single round-trip to Riak | 1000          | KV
spark.riak.input.split.count                   | Desired minimum number of Spark partitions to divide the data into | 10| KV
spark.riak.output.wquorum                      | Quorum value on write                                              | 1 | KV
spark.riak.connections.inactivity.timeout      | Time to keep connection to Riak alive in milliseconds | 1000 | KV/TS
spark.riakts.bindings.timestamp                | To treat/convert Riak TS timestamp columns either as a Long (UNIX milliseconds) or as a Timestamps during the automatic schema discovery. Valid values are: <ul><li>useLong</li><li>useTimestamp</li><ul> | useTimestamp | TS


Example:

```scala
val conf = new SparkConf()
        .setAppName("My Spark Riak App")
        .set("spark.riak.connection.host", "10.0.4.1:8087")
        .set("spark.riak.connections.min", "20")
        .set("spark.riak.connections.max", "50")

val sc = new SparkContext("spark://10.0.4.1:7077", "test", conf)
```

##Creating Spark Context
» **Same here... is this information for someone developing locally after a download/build or is it equally important for someone adding the connector as a dependency?** « 

The following import statements have to be includes at the top of your Spark application to enable the connector:

```scala
import com.basho.riak.client.core.query.Namespace
import com.basho.spark.connector.rdd.RiakFunctions
import org.apache.spark.{SparkContext, SparkConf}
import com.basho.spark.connector._
```

Now lets create spark context that connects to Riak node at 192.168.1.55:8087.
In this example we also assume that there is a Spark Master running at 192.168.1.100:7077 

```scala
val conf = new SparkConf()
        .set("spark.riak.connection.host", "192.168.1.55:8087")

val sc = new SparkContext("spark://192.168.1.100:7077", "Best Spark App Ever", conf)
```

###Loading Data from Riak 

Once Spark Context is created we can load data stored in Riak buckets into Spark as RDDs.

Lets do a simple but very powerful full bucket read. Yes - we are going to read the content of entire Riak bucket in one command, and it will happen in an efficient partitioned parallel way and get values as Strings:

```scala
 val data = sc.riakBucket[String](new Namespace("bucket-full-of-data"))
      .queryAll()
```

What if you don't want the content of entire bucket, but instead want something specific. Very specific. When you know your keys by name, you can pass them in directly:

```scala
val rdd = sc.riakBucket(new Namespace("FOO"))
      .queryBucketKeys("mister X", "miss Y", "dog Z")
```


What if you want a range of values (say, from 1 to 5000) defined by a numeric 2i index in Riak? Your bucket is "BAR" and your index is "myIndex". 
No problem:

```scala
val rdd = sc.riakBucket(new Namespace("BAR"))
      .query2iRange("myIndex", 1L, 5000L)
```

###Saving into Riak

To be able to save your calculation results from Spark into a Riak bucket you need to first add this import line at the top of your application:

```scala
import com.basho.spark.connector.writer.{WriteDataMapper, WriteDataMapperFactory}
```

Lets assume we want the output persisted into the bucket named "FOO"
Here is how we write data from Spark RDD into the "FOO" bucket:

```scala
rdd.saveToRiak(new Namespace("FOO"))
```

##Examples

Riak Spark connector comes with several sample programs and demos that can be found in the [examples folder](https://github.com/basho/spark-riak-connector/tree/master/examples).