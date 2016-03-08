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
