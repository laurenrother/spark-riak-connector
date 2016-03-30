» **Add instructions + examples for how to add S-R connector as a dependency. (3 languages: java (POM.xml file), scala (build.sbt file), or python (adding it to the PySpark/Spark classpath))** « 

Creating a POM.xml file that includes some xml that looks sort of like this:

```
<dependencyManagement>
	<dependencies>
		<dependency>
		    <groupId>com.basho.riak</groupId>
		    <artifactId>spark-riak-connector</artifactId>
		    <version>1.3.0-beta1<</version>
		</dependency>
	</dependencies>
</dependencyManagement>
        
<repositories>
 <repository>
    <id>bintray</id>
    <url>https://dl.bintray.com/basho/data-platform</url>
    <releases>
        <enabled>true</enabled>
    </releases>
    <snapshots>
        <enabled>false</enabled>
    </snapshots>
    </repository>
</repositories>
```

- if you're using scala/java, add the spark connector jar as a dependency to your build file
- if you're using python, add the jar to your classpath

take a look at the java version of that example
you'll see how dependencies are added to that app

This example will use Maven to compile an application JAR, but any similar build system will work.

We’ll create a very simple Spark application, SimpleApp.java:

```
/* SimpleApp.java */
import org.apache.spark.api.java.*;
import org.apache.spark.SparkConf;
import org.apache.spark.api.java.function.Function;

public class SimpleApp {
  public static void main(String[] args) {
    String logFile = "YOUR_SPARK_HOME/README.md"; // Should be some file on your system
    SparkConf conf = new SparkConf().setAppName("Simple Application");
    JavaSparkContext sc = new JavaSparkContext(conf);
    JavaRDD<String> logData = sc.textFile(logFile).cache();

    long numAs = logData.filter(new Function<String, Boolean>() {
      public Boolean call(String s) { return s.contains("a"); }
    }).count();

    long numBs = logData.filter(new Function<String, Boolean>() {
      public Boolean call(String s) { return s.contains("b"); }
    }).count();

    System.out.println("Lines with a: " + numAs + ", lines with b: " + numBs);
  }
}
```

This program just counts the number of lines containing ‘a’ and the number containing ‘b’ in a text file. Note that you’ll need to replace YOUR_SPARK_HOME with the location where Spark is installed. As with the Scala example, we initialize a SparkContext, though we use the special JavaSparkContext class to get a Java-friendly one. We also create RDDs (represented by JavaRDD) and run transformations on them. Finally, we pass functions to Spark by creating classes that extend spark.api.java.function.Function. The Spark programming guide describes these differences in more detail.

To build the program, we also write a Maven pom.xml file that lists Spark as a dependency. Note that Spark artifacts are tagged with a Scala version.

```
<project>
  <groupId>edu.berkeley</groupId>
  <artifactId>simple-project</artifactId>
  <modelVersion>4.0.0</modelVersion>
  <name>Simple Project</name>
  <packaging>jar</packaging>
  <version>1.0</version>
  <dependencies>
    <dependency> <!-- Spark dependency -->
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-core_2.10</artifactId>
      <version>1.6.1</version>
    </dependency>
  </dependencies>
</project>
```

We lay out these files according to the canonical Maven directory structure:

```
$ find .
./pom.xml
./src
./src/main
./src/main/java
./src/main/java/SimpleApp.java
```
Now, we can package the application using Maven and execute it with ./bin/spark-submit.

```
# Package a JAR containing your application
$ mvn package
...
[INFO] Building jar: {..}/{..}/target/simple-project-1.0.jar

# Use spark-submit to run your application
$ YOUR_SPARK_HOME/bin/spark-submit \
  --class "SimpleApp" \
  --master local[4] \
  target/simple-project-1.0.jar  
...
Lines with a: 46, Lines with b: 23
```
