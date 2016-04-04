# Spark-Riak Connector

The Spark-Riak connector is a Scala library that enables you to connect your Spark application to Riak KV and Riak TS. 

Whether your Spark app is written in Scala, Java or Python, the Spark-Riak connector allows you to expose data stored in Riak buckets as Spark RDDs and output data from Spark RDDs into Riak buckets. The connector has support for failover if a Riak node goes down, and the connector makes it easy to partition the data you get from Riak so multiple Spark workers can process the data in parallel.

From customer-product recommendation systems to any machine-learning on big datasets, the Spark-Riak connector makes distributed analytics easier.

## Compatibility

* Compatible with Riak TS 1.2+
* Compatible with Apache Spark 1.5.2+
* Compatible with Scala 2.10+
* Compatible with Java 8
* Compatible with the following OSes:
  * CentOS 6
  * CentOS 7
  * RHEL 6
  * RHEL 7
  * Ubuntu 12.04
  * Ubuntu 14.04
  * Debian 7
  * OS X 10.8


## Get Started

First, take a look at our [configuration](https://github.com/basho/spark-riak-connector/tree/master/documentation/download-and-install.md) documentation to check out how to plug Riak-Spark connector into your application.

Then take a look at the various tasks the connector can perform:

* [Querying](https://github.com/basho/spark-riak-connector/blob/documentation/documentation/riakts/querying.md) 
* [Testing](https://github.com/basho/spark-riak-connector/blob/documentation/documentation/riakts/testing.md) 
* [Reading](https://github.com/basho/spark-riak-connector/blob/documentation/documentation/riakts/reading.md)
* [Writing](https://github.com/basho/spark-riak-connector/blob/documentation/documentation/riakts/writing.md)
* [Using dataframes](https://github.com/basho/spark-riak-connector/blob/documentation/documentation/riakts/dataframes.md)
* [Using Java Connector](https://github.com/basho/spark-riak-connector/blob/documentation/documentation/riakts/javaconnector.md)

Once you've set up your files, you're ready to build the connector. You can implement the connector in one of the following ways:

1. Download the source code of the connector, build it, and install the results in your local maven repo. For instructions on how to do that, [go here](https://github.com/basho/spark-riak-connector/tree/master/documentation/download-and-install.md).
2. Or, add the connector as a dependency to your application so it downloads and installs locally as part of your app's build process. For instructions on how to do that, [go here](https://github.com/basho/spark-riak-connector/tree/master/documentation/add-as-dependecy.md).