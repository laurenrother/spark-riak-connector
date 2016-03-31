# Spark-Riak Connector

The Spark-Riak connector is a Scala library that enables you to connect your Spark application to Riak KV and Riak TS. 

Whether your Spark app is written in Scala, Java or Python, the Spark-Riak connector allows you to expose data stored in Riak buckets as Spark RDDs and output data from Spark RDDs into Riak buckets. The connector has support for failover if a Riak node goes down, and the connector makes it easy to partition the data you get from Riak so multiple Spark workers can process the data in parallel.

**Whether you are looking to build out a session, shopping cart, advertisement or other dynamically-rendered copy, RRA helps reduce read pressure on your persistent store (Riak KV).**

## Compatibility

* Compatible with Riak TS
* Compatible with Apache Spark 1.5.2+
* Compatible with Scala 2.10+
* Compatible with Java 8
* Compatible with the following OSes:
  * OS 

## Get Started

You will likely want to implement the Spark-Riak connector in one of the following ways:

1. Download the source code of the connector, build it, and install the results in your local maven repo. For instructions on how to do that, [go here](https://github.com/basho/spark-riak-connector/tree/master/documentation/download-and-install.md).
2. Or, add the connector as a dependency to your application so it downloads and installs locally as part of your app's build process. For instructions on how to do that, [go here](https://github.com/basho/spark-riak-connector/tree/master/documentation/add-as-dependecy.md).