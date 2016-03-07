# Using Spark Riak Java Connector

To use Riak KV and Riak TS features of Spark Riak connector in Java applications first thing that should be done is to create javaSparkContext:

```java
JavaSparkContext jsc = new JavaSparkContext(sparkConf);
```

Further steps are different for Riak KV and Riak TS.

## Riak KV

### Read from Riak

To use any of the Riak query functions, initial RiakJavaRDD must be created by using any of SparkContextJavaFunctions.riakBucket(...) method. The resulting RiakJavaRDD still needs query criteria to perform the following operations:

1. Load all data from a bucket - returns all existing data from the bucket:

```java
    SparkJavaUtil.javaFunctions(jsc).riakBucket(NAMESPACE, String.class).queryAll();
```

2. Load data for a range of index values - returns only data that have index value inside a range (inclusive):

```java
    SparkJavaUtil.javaFunctions(jsc).riakBucket(NAMESPACE, String.class).query2iRangeLocal(INDEX_NAME, from, to);
```

3. Load data for a list of index values - returns only data that have index value inside a list:

```java
    SparkJavaUtil.javaFunctions(jsc).riakBucket(NAMESPACE, String.class).query2iKeys(INDEX_NAME, iValue1, iValue2, ...);
```
    
4. Load data by keys - return only data for listed keys:

```java
    SparkJavaUtil.javaFunctions(jsc).riakBucket(NAMESPACE, String.class).queryBucketKeys("key-1", "key-3", "key-6", ...)
```

### Save to Riak

Existing rdd JavaRDD<{UserDefinedType}> rdd can be saved to Riak KV as follows 

```java
 SparkJavaUtil.javaFunctions(rdd).saveToRiak(NAMESPACE);
```

{UserDefinedType} must be serializable and can use annotations from com.basho.riak.client.api.annotations package.

```java
class SimpleUDT implements Serializable {
	@RiakIndex(name = "creationNo")
	private long creationNo;
	private String value;
	@RiakKey
	private String key;
...
}
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
