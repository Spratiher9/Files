## Introduction

Data Lakes built using Hadoop framework were lacking a very basic functionality i.e. ACID compliance. Databricks (the company behind Spark) came up with a unique solution i.e. Delta Lake. Delta Lake enables ACID transactions over existing Data Lakes. It can seamlessly integrate with many Big Data Frameworks like Spark, Presto, Athena, Redshift, Snowflake etc. Let’s explore this interesting technology more.

## Learning Objectives

1.  What is Delta Lake?
2.  Why to use Delta Lakes?
3.  Parquet v/s Delta Lakes
4.  Delta Lake Architecture
5.  How does Delta Lake work?
6.  How to use Delta Lakes with a working example?

### What is Delta Lake?

It is an Open source storage layer that provides reliability to the data lakes. Delta Lakes provides ACID(Atomicity, Consistency, Integrity and Durability) properties, scalable metadata handling, and unifies streaming and batch data processing. It runs on the top of data lakes and it is fully compatible with Spark APIs. Data in Delta Lake is stored in Parquet format. It enables Delta Lake to leverage the efficient compression and encoding schemes that are native to Parquet.

### Why to use Delta Lakes?

Here are the key reasons for using Delta Lake -

1.  Delta Lake brings ACID transactions to your Data Lakes. The readers will never see inconsistent data.
2.  Delta Lake treats metadata just like data. Delta Lake can handle petabyte-scale tables with billions of partitions and files at ease, hence, it doesn’t suffer from Small file problem like HDFS.
3.  It is both, batch table as well as streaming source & sink.
4.  It provides the ability to specify your schema and enforce it. This helps to prevent the bad data from causing corruption.
5.  Delta Lake transaction log records details about every change made to data providing a full audit trail of the changes.
6.  Anyone can use Delta Lake with their existing data pipelines with minimal change as it is fully compatible with Spark, the commonly used big data processing engine.

### Parquet v/s Delta Lakes

Delta lake is a wrapper over Parquet data format but it offers some additional features on top of Parquet. Here are the differences -

| Parquet      | Delta Lake |
| ----------- | ----------- |
| Columnar Data Storage      | Storage layer with ACID transaction       |
| Type specific encoding   | Scalable metadata handling        |
| No Data Versioning Support   | Data Versioning Available        |
| Available to any project in the Hadoop ecosystem, regardless of the choice of data processing framework   | Available to Spark data processing framework only. It integrates with Presto, Athena etc        |

### Delta Lake Architecture

![](https://www.filepicker.io/api/file/CRBDE8RhGDq15xdld7Ig)

Overall Delta Lake Architecture has been divided into 3 zones. Here **Bronze** tables serve as the prototypical lake of data, where massive amounts of data trickle in continuously. When it arrives, it’s dirty because it comes from different sources, some of which are not so clean.

From there, data flows constantly into **Silver** tables, like the headwaters of a stream connected to the lake, rapidly moving and constantly flowing. As Data flows downstream, it is cleaned and filtered by the twists and turns of the various functions, filters and queries, becoming purer as it moves.

By the time it reaches the data processing downstream i.e. our **Gold** tables, it receives some final purification and stringent testing to make it ready, because consumers i.e. ML algorithms, Data Analysis, etc. are very picky and will not tolerate contaminated data.

### How does Delta Lake Work?

For understanding the working of the Delta Lakes, there is a need to understand the working of Transactional Logs. As the transactional log is the common thread that runs through many of its most important features, including ACID transactions, scalable metadata handling, time travel, etc.

Whenever a user executes any modified command, Delta Lakes breaks it down into a series of steps composed of one or more actions.

The list of these actions are:

-   **Add File:** It adds the data file
-   **Remove File:** It removes the data file
-   **Update Metadata:** It updates the table metadata.
-   **Set Transaction:** It records that a structure streaming job created a micro-batch with ID
-   **Change Protocol:** Makes more secure by transferring Delta Lakes to the latest securing protocol.
-   **Commit Info:** It contains the information about the Commits.

### How to use Delta Lakes with a working example?

Delta Lakes require Apache Spark **2.4.2** or above. You can run it in two ways:

-   **Run interactively:** Start the Spark shell (Scala or Python) with Delta Lake and run the code snippets interactively in the shell.
-   **Run as a project:** Set up a Maven or SBT project (Scala or Java) with Delta Lake, copy the code snippets into a source file, and run the project.

#### Run Interactively

For running the Delta lake on interactive pyspark or scala spark-shell. You just required Apache Spark on your machine.

Run Pyspark with delta package:
```bash
pyspark --packages io.delta:delta-core_2.12:1.1.0
```

Run spark-shell with delta package: 
```bash
spark-shell --packages io.delta:delta-core_2.12:1.1.0
```

Before executing this commands we need the required jars to be present in our spark folder inside `spark/jars/`.
For that we have to execute the following commands (shown for linux system):
    
```shell
> cd /usr/lib/spark/jars/
> sudo wget https://repo1.maven.org/maven2/io/delta/delta-core_2.12/1.0.0/delta-core_2.12-1.0.0.jar
```

#### Run as a Project

-   If you are creating a maven project then you have to add the following dependencies in POM file:

```xml
<!-- https://mvnrepository.com/artifact/io.delta/delta-core -->
<dependency>
    <groupId>io.delta</groupId>
    <artifactId>delta-core_2.12</artifactId>
    <version>1.0.0</version>
</dependency>

```

-   You can include Delta Lakes in SBT projects by adding the following line to your build.sbt file: 
```scala 
libraryDependencies += "io.delta" %% "delta-core" % "1.1.0"
```
-   In python projects, if you include Delta Lakes then add the following lines:

```python
spark = pyspark.sql.SparkSession.builder.appName("MyApp")\  
.config("spark.jars.packages", "io.delta:delta-core_2.12:1.1.0")\  
.getOrCreate()  

from delta.tables import *
```

### Create Table:

**Pyspark:**

data = spark.range(0, 10)  
data.write.format("delta").save("/tmp/sample-table")

![](https://www.filepicker.io/api/file/uZaNmY87SASIQzjLqUZR)

**Scala:**

val data = spark.range(0, 10)  
data.write.format("delta").save("/tmp/sample-table")

![](https://www.filepicker.io/api/file/DimVnIKSYy4TtJIXxvma)

### Read Table:

**Pyspark:**

df =spark.read.format("delta").load("/tmp/sample-table")  
df.show()

![](https://www.filepicker.io/api/file/Ihk2wzYATqyXhcmtiTLz)

**Scala:**

val df = spark.read.format("delta").load("/tmp/sample-table")  
df.show()

![](https://www.filepicker.io/api/file/lIBKE18RQ9epptSFo1kS)

### Update Table:

**Pyspark:**

data = spark.range(10, 20)  
data.write.format("delta").mode("overwrite").save("/tmp/sample-table")

![](https://www.filepicker.io/api/file/wzu8EvwRRmGrz2hY7k54)

![](https://www.filepicker.io/api/file/lO2XbFPwR9GJkq2VNUlW)

**Scala:**

val data = spark.range(10, 20)  
data.write.format("delta").mode("overwrite").save("/tmp/sample-table")  
df.show()

![](https://www.filepicker.io/api/file/pNSuRl04QA2Cfl5QWa3X)

Want to learn more about Delta Lake experience? Check out this session recording here:

[![Watch the video](https://img.youtube.com/vi/a18C8kJfNrE/maxresdefault.jpg)](https://www.youtube.com/watch?v=a18C8kJfNrE)

## Conclusion

Delta Lakes are the open-source data storage layer, which is compatible with Spark APIs to provide ACID transactions, scalable metadata handling, and unifies streaming and batch data processing. For using Delta Lakes, your system must have Apache Spark version 2.4.2 or above.

## Reference

[https://docs.delta.io/latest/delta-intro.html](https://docs.delta.io/latest/delta-intro.html)
