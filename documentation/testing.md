## Testing

In Spark-Riak connector unit tests are separated from integration tests. 
In case if there is no Riak installation it is still possible to successfully run 
unit tests:

```
mvn clean test
```

If there is Riak installed it is possible to run both unit tests and integration test. Futhermore, KV-specific integration tests are separated from TS-specific ones. To choose which set of tests to run appropriete maven ptofile should be selected: riak_kv(default) or riak_ts.

```
mvn clean verify -P riak_ts
```

Riak host can be provided in com.basho.riak.pbchost variable

```
mvn clean verify -P riak_ts -Dcom.basho.riak.pbchost=myhost:8087
```