
1. Creating a Spark Context
2. Further Examples



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