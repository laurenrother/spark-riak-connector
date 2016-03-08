## Building Spark DataFrame over RiakRDD
You can use Spark DataFrames on top of your RiakRDD. First you need to create SqlContext from SparkContext.

```scala
	val sqlContext = new org.apache.spark.sql.SQLContext(sc)
```

Then after you import

```scala    
	import sqlContext.implicits._
```
    
you can use toDf() method on your RiakRDD.

```scala    
	val riakRdd = sc.riakBucket[UserData](namespace).queryAll
	val df = riakRdd.toDF
```

You have to specify user defined type

```scala
	case class UserData(user_id: String, name: String, age: Int, category: String)
```

to allow schema inference using reflection.

Once you have your org.apache.spark.sql.DataFrame object you can use its methods for filtering

```scala
	df.where(df("age") >= 50).select("id", "name")
```

or more complex operations like grouping.

```scala
	df.groupBy("category").count
```

Alternatively, you can register a table

```scala
	df.registerTempTable("users")
```

and use standard SQL queries over it.

```scala
	sqlContext.sql("select * from users where age >= 50")
```

Another thing you can use are user defined functions (UDFs). First, you have to register a UDF.

```scala
	sqlContext.udf.register("stringLength", (s: String) => s.length)
```

After that you can use it in SQL queries   

```scala
	sqlContext.sql("select user_id, name, stringLength(name) nameLength from users order by nameLength")
```
    
## Saving DataFrames into Riak    
When you already have a DataFrame you can save it into Riak. To do that make sure you imported com.basho.riak.spark object so that saveToRiak() method is available.

```scala
	import com.basho.riak.spark._
```

Then you can use toJSON() method to save data to riak in json format

```
	dataFrame.toJSON.map {
		line =>
			val obj = RiakObjectConversionUtil.to(line)
			obj.setContentType("application/json")    
			obj
		}.saveToRiak(namespace)
```

Setting content type to application/json will allow automatic conversion to user defined type later when reading from Riak.