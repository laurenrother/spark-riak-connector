#Querying TS Buckets vs Non-TS Buckets with Spark Connector

For existing TS-bucket "ts_demo" in riak TS with primary key "time" (e.g. "PRIMARY KEY ((quantum(time, 1, 'h')), time)") it is possible to make a range scan query [from, to] as following

	val rdd = sc.riakTSBucket("ts_demo").sql(s"SELECT * FROM ts_demo WHERE time >= $from AND time <= $to")

In order to make a range scan for non-TS bucket "non_ts_demo", Long index "time" has to be created and then queried using 2i queries

	val rdd = sc.riakBucket[NonTsDemoType](new Namespace("non_ts_demo")).query2iRangeLocal("time", from, to)
    
To load full content of non-TS bucket the approach is to use fullbucket read query as follows:

    	val rdd = sc.riakBucket[NonTsDemoType](new Namespace("non_ts_demo")).queryAll

It is possible to filter TS bucket using query language:

	val rdd = sc.riakTSBucket("ts_demo").sql(s"SELECT * FROM ts_demo WHERE time >= $from AND time <= $to AND user_id=$userId")
    
Same result for non-TS bucket can be achieved using filter over resulting rdd

	val rdd = sc.riakBucket[NonTsDemoType](new Namespace("non_ts_demo")).query2iRangeLocal("time", from, to).filter(_.userId = userId)


