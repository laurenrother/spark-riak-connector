#Working with Spark Dataframes and Riak TS

To enable DF functionality first steps are 

```scala
val sc = new SparkContext()
val sqlContext = new org.apache.spark.sql.SQLContext(sc)
import sqlContext.implicits._
```

##Read from Riak TS

To read data from existing TS bucket tableName standard SQLContext means can be used providing special “org.apache.spark.sql.riak” data format and using range query expression: 

```scala
val df = sqlContext.read   
 	.option("spark.riak.connection.hosts","myhost:10017")
  	.format("org.apache.spark.sql.riak")
  	.load(tableName)
	.select(“time”, “col1”, “col2”)
  	.filter(s"time >= CAST($from AS TIMESTAMP) AND time <= CAST($to AS TIMESTAMP) AND  col1= $value1 AND col2 =$value2")
```

Schema may or may not be provided using .schema() method. If not provided it will be inferred.
Any of the Spark Connector options can be provided in .option() or .options().

Alternatively, org.apache.spark.sql.riak.RiakSQLContext can be created and then queried with range query using sql() method

```scala
val riakSqlContext = new RiakSQLContext(sc, tableName)
val alternativeDf = riakSqlContext.sql(s"SELECT time, col1, col2 from $tableName WHERE time >= CAST($from AS TIMESTAMP) AND time <= CAST($to AS TIMESTAMP) AND  col1= $value1 AND col2 =$value2")
```

## Save DF to Riak TS

Existing inputDF that has the same schema as TS bucket (column order and types) can be saved to Riak TS as follows: 

```scala
inputDF.write
   .option("spark.riak.connection.hosts","myhost:10017")
   .format("org.apache.spark.sql.riak")
   .mode(SaveMode.Append)
   .save(tableName)
```

So far SaveMode.Append is the only mode available.
Any of the Spark Connector options can be provided in .option() or .options().
