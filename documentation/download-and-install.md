# Download, Build, and Install Spark-Riak Connector

If you want to download the source code of the Spark-Riak connector, build it, and install the results in your local repo, this is the document for you! Keep reading for instructions on downloading, building, and installing the connector.

If you just want to add the connector as a dependency to your application, you'll want to go [here](https://github.com/basho/spark-riak-connector/tree/master/documentation/add-as-dependecy.md) instead.

## Overview

To install and use the Spark-Riak connector from your local repo, you'll need to complete the following steps:

1. Make sure you have all of the [prerequisite](#prerequisites) components.
2. Clone the repo to [download](#download) the connector source code.
3. Then [build](#build) the connector.


## Prerequisites

In order to use the Spark-Riak connector, you must have the following installed: 

* Java 8 JDK; you can find the Java 8 JDK download page [here](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Maven; you can find the Maven download page [here](https://maven.apache.org/download.cgi).
* Spark; the Spark download page is [here](http://spark.apache.org/docs/latest/#downloading).
* Riak [KV](http://docs.basho.com/riak/kv/2.2.0/setup/installing/) or [TS](http://docs.basho.com/riak/ts/latest/installing/).


## Download

Once you've installed all of the prerequisites, you need to clone this (basho/spark-riak-connector) repository. 

Make sure you've navigated to the directory you want the Spark-Riak connector to be in, then run:

```
$ git clone https://github.com/basho/spark-riak-connector.git
```

## Build

If you are using the Spark-Riak connector for a Java or Scala application, please see the "Build for Java/Scala apps" section below. If you are building an app in any other language, you can skip to [Build command](#build-command).


###Build for Java/Scala apps

The examples below demonstrate which dependencies to add and how to add them to your pom.xml so the Spark-Riak connector builds correctly with your Java/Scala application. (Alternatively, you can explore the [source code](https://github.com/basho/spark-riak-connector/tree/documentation/examples/src/main) of bundled examples in Java and Scala.)

#### Automatically download `dataplatform-riak-client.jar`

To download `dataplatform-riak-client.jar`»**This file is listed in the jars that automatically happen during build. Should it be removed from that list?**« automatically during the build, the following repository should be added to pom.xml: 

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

#### Maven dependency

If your Spark application uses Maven, you will need to include the following dependency to enable the Spark-Riak connector:

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

#### Java dependency

If your Spark application is going to be written in Java, add the following dependency in addition to the [Maven dependency](#maven-dependency) above:

```xml
<dependency>
    <groupId>com.basho</groupId>
    <artifactId>spark-riak-connector-java</artifactId>
    <version>1.0.0</version>
</dependency>
```

#### Necessary imports

The following import statements are required to be in the Java or Scala files that use the connector:

```scala
import com.basho.riak.client.core.query.Namespace
import com.basho.riak.spark.rdd.RiakFunctions
import org.apache.spark.{SparkContext, SparkConf}
import com.basho.riak.spark._
```

### Build command

Building the Spark-Riak connector is as easy as running a single command:

```
mvn clean install
```

When you run `install`, integration tests execute with the Maven Failsafe Plugin. This ensures Spark-Riak connector can be built even if there is no Riak node running.

You can skip the integration tests by running the build command with the `-DskipTests` flag:

```
mvn clean install -DskipTests
```