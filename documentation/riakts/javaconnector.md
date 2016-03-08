# Using Spark Riak Java Connector

To use Riak KV and Riak TS features of Spark Riak connector in Java applications first thing that should be done is to create javaSparkContext:

```java
JavaSparkContext jsc = new JavaSparkContext(sparkConf);
```
## Riak TS

### Read from Riak

To use any of the Riak query functions, initial RiakTSJavaRDD must be created by using SparkContextJavaFunctions.riakTSBucket() method. The resulting RiakTSJavaRDD still needs sql query string to perform range scan:

```java
    SparkJavaUtil.javaFunctions(jsc).riakTSBucket(TABLE_NAME, Row.class).sql(String.format("SELECT * FROM %s WHERE time >= %d AND time <= %d  AND  weather = 'sunny' AND family = 'f'", TABLE_NAME, from, to));
```

### Save to Riak

Existing rdd JavaRDD<org.apache.spark.sql.Row> sqlRdd can be saved to Riak TS as follows

```java
 SparkJavaUtil.javaFunctions(sqlRdd).saveToRiakTS(TABLE_NAME);
```
