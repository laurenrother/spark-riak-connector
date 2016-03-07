[![Build Status](https://travis-ci.org/basho/spark-riak-connector.svg?branch=develop)](https://travis-ci.org/basho/spark-riak-connector)

#Spark-Riak Connector

The Spark-Riak connector allows you to expose data stored in Riak buckets as Spark RDDs, as well as output data from Spark RDDs into Riak buckets. 

##Compatibility

* Compatible with Riak KV **??** version what? bundled inside BDP 1.x
* Compatible with Apache Spark 1.5.2 or later
* Compatible with Scala 2.10 or later
* Compatible with Java 8


## Features
* Exposes data in Riak KV bucket as Spark RDD
* Provides ability to construct an RDD from a given set of keys
* Provides ability to construct an RDD using an enhanced 2i query (a.k.a. full bucket read) 
* Allows parallel full bucket reads into multiple partitions
* Allows saving of an RDD into a specified Riak bucket and indexing results with 2i indexes
* Provides mapping and data conversion for JSON formatted values
* Provides ability to construct an RDD by using a 2i string index or a set of indexes
* Provides ability to construct an RDD by using a 2i range query or a set of ranges 
* Provides ability to perform range queries over Riak TS bucket
* Provides ability to construct Dataframes from Riak TS bucket

