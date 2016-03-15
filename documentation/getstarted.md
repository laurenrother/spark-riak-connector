#Spark-Riak Connector

To get started using the Spark-Riak connector, you'll need to

1. [Install](#installing)
2. [Build](#building)
3. Then get [set up for development](#set-up-for-development)


##Installing

In order to use the Spark-Riak connector, you must: 

* Have Java 8 JDK installed; you can find the Java 8 JDK download page [here](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Have Maven installed; go to the [Maven download page](https://maven.apache.org/download.cgi) and follow installation instructions for your OS.


##Building

Clone this (basho/spark-riak-connector) repository, then build Spark-Riak connector by running:

```
mvn clean install
```

Integration tests are executed with maven failsafe plugin so Spark-Riak connector can be built even if there is no Riak node running.

You can skip the tests by running the build command with the `-DskipTests` flag:

```
mvn clean install -DskipTests
```

##Testing

In Spark Riak connector unit tests are separated from integration tests. 
In case if there is no Riak installation it is still possible to successfully run unit tests:
```
mvn clean test
```
If there is Riak installed it is possible to run both unit tests and integration test. Futhermore, KV-specific integration tests are separated from TS-specific ones. To choose which set of tests to run appropriete maven ptofile should be selected: riak_kv(default) or riak_ts.
```
mvn clean verify -P riak_ts
```
Riak host can be provided in com.basho.riak.pbchost variable
``
mvn clean verify -P riak_ts -Dcom.basho.riak.pbchost=myhost:8087
```

##Set Up for Development

**?? I don't quite understand what the flow of To-Dos here is.**

Once the connector is built, several jars are produced. The main connector jar is `spark-riak-connector-1.0.0.jar` and it is contained within `spark-riak-connector/target/`.

If you're planning to develop Spark applications in Java you will need an additional jar, `spark-riak-connector-java-1.0.0.jar`, which you can find in `spark-riak-connector-java/target/`.

The Spark-Riak connector depends on the following libraries:

* guava-14.0.1.jar
* jackson-datatype-joda-2.2.2.jar
* joda-time-2.1.jar
* jackson-module-scala_2.10-2.4.4.jar
* jcommon-1.0.23.jar
* scala-java8-compat_2.10-0.3.0.jar
* dataplatform-riak-client-1.0.0.jar

All of these libraries need to be referenced by your Spark application and accessible through driver program classpath.
 
Please see below code snippets in Scala, or explore the source code of bundled examples in Java and Scala. **?? for what?**
 
The following link can be used to manually download the [dataplatform-riak-client-1.0.0.jar](https://bintray.com/basho/data-platform/com.basho.riak/view)
from the Bintray repository.

To download `dataplatform-riak-client.jar` automatically during the build, the following repository should be added to pom.xml:

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

All of the examples are currently based on the 1.0.0 release of the connector. 
If your spark project uses maven, include the following dependency to enable Spark Riak Connector:

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


###Necessary imports
**?? Also, not sure what the to-do here is.**

```scala
import com.basho.riak.client.core.query.Namespace
import com.basho.riak.spark.rdd.RiakFunctions
import org.apache.spark.{SparkContext, SparkConf}
import com.basho.riak.spark._
```


##Configuration

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