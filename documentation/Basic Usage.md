1. Reading data from Riak bucket
2. Reading data from Riak TS bucket
3. Creating a Spark Context
4. Further Examples


##Reading data from Riak bucket

First we need to specify which Riak bucket (i.e. bucket named `test-data`):

```scala

val SOURCE_DATA = new Namespace("test-data")
```

Then we have several options to construct an RDD:

1. Query by an explicit set of keys

```scala

val rdd = sc.riakBucket(SOURCE_DATA).queryBucketKeys("key-1", "key-2", "key-2")
```

2. Query by a given 2i numeric range

```scala
val idxName = "myIndex"
val from = 0L
val to = 1000L

val rdd = sc.riakBucket(SOURCE_DATA).query2iRange(idxName, from, to)
```

   or a set of ranges

```scala
val idxName = "myIndex"

val rdd = sc.riakBucket(SOURCE_DATA).partitionBy2iRanges(idxName, 1->3, 4->6, 7->12)
```

3. Query by a given 2i string tag or set of tags

```scala

val rdd = sc.riakBucket(SOURCE_DATA).query2iKeys("mon_data", "wed_data", "fri_data")
```

###Doing something useful with the data

Once the RDD is constructed all standard Scala Spark transformations and actions can be applied.

The simplest action counts all elements and prints out the count:

```scala

println(s"Element count: ${rdd.count()}")
```

###Saving results into Riak KV

To be able to write data out from an RDD into a Riak bucket the following import for a writer is needed:

```scala

import com.basho.riak.spark.writer.{WriteDataMapper, WriteDataMapperFactory}
```

Define the output bucket and issue `saveToRiak` method on an RDD:

```scala
val MY_OUTPUT_BUCKET = new Namespace("output-data")

rdd.saveToRiak(MY_OUTPUT_BUCKET)
```

##Reading data from Riak TS bucket

Riak TS buckets can be queried using sql() function:

```scala
val rdd = sc.riakTSBucket(tableName).sql(s"SELECT * FROM $tableName WHERE time >= $from AND time <= $to")
```

###Saving rdd to Riak TS bucket

Existing rdd of org.apache.spark.sql.Row> can be saved to Riak TS as follows

```scala
rdd.saveToRiakTS(TABLE_NAME);
```


##Creating Spark Context

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

Riak Spark connector comes with several sample programs and demos that can be found in the [**examples** folder](./examples)