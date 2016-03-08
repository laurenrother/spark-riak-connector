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


