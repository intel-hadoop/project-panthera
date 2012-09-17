# Project Panthera: Better Analytics with SQL, MapReduce and HBase #

In the last several years, we have been working closely with many users and customers to build their next-gen data analytics platforms using the Hadoop stack. Increasingly, these platforms have transitioned from a throughput-oriented, batch-style system powered by MapReduce and HDFS, to a (semi) realtime analytics system built around HBase, which supports near realtime ingestion of new data, high update-rate accesses and very low latency, online data serving (in addition to high-throughput scan for MapReduce). On the other hand, while the Hadoop/HBase stack has laid a solid foundation for these systems, we are still required to implement many new capabilities in building a flexible and efficient data analytics platform, such as better integration with existing enterprise data analytics infrastructure using SQL, better query processing on HBase (e.g., leveraging relational model and HBase co-processor), and efficiently utilizing new hardware platform technologies.
 
Project Panthera is our open source efforts to contribute these new capabilities we have built to the Apache Hadoop community. Under Project Panthera, we will make our implementations available at the project repo, showcasing these new capabilities; in addition, we will collaborate with the Hadoop community (by going through the standard Apache open source process) to have some of these ideas reviewed and hopefully incorporated in related Apache projects. 

In this initial release of Project Panthera, two new capabilities are made available for better analytical queries support:

#####1. *An analytical SQL engine for MapReduce (built on top of Hive)*#####

Higher level languages or APIs (such as Pig, Hive and Cascading) significantly lower the barrier to MapReduce based data analytics. On the other hand, SQL is still the most important query language in modern business application environment – required by all the existing business users, enterprise analytics applications and third-party tools. Therefore a SQL compatible engine for MapReduce is critical for the data analytics platform. While there are continuous efforts in extending Hive’s SQL support, many widely used SQL constructs (e.g., selecting from multiple tables) are still not supported.

We have taken a different approach to build a SQL engine for MapReduce – it uses an open source SQL parser (<https://github.com/porcelli/plsql-parser>) to generate AST (abstract syntax tree) for the SQL query, then transforms the SQL AST to MapReduce jobs through a series of context-aware analyses and optimizations (reusing the semantic analyzer and optimizer in Hive when appropriate).

Under Project Panthera, we will gradually make our implementation available as an extension to Hive (<https://github.com/intel-hadoop/hive-0.9-panthera>). Specifically, this release provides support for common SQL constructs used in analytic queries by our users and customers, including some important features (e.g., sub-query in WHERE clauses, multiple-table SELECT statement, etc.) that are currently not supported in Hive today; in addition, the SQL engine co-exists with the HiveQL frontend, so that one can even mix SQL and HiveQL statements in their queries. Going forward, we will also use [Hive-3472](https://issues.apache.org/jira/browse/HIVE-3472) as the umbrella JIRA to track our efforts to get the SQL engine idea reviewed and hopefully incorporated into Apache Hive.

#####2. *A document store (built on top of HBase) for better query processing*#####
While HBase already has very effective MapReduce integration with its very good scanning performance, analytical query (no matter SQL or HiveQL) processing with MapReduce on HBase still has significant gaps compared to query processing on HDFS.

* *Space overheads*. To provide very flexible schema support, physically HBase stores its table as a multi-dimensional map, where each cell (except the row key) is stored on disk as a key-value pair: (row_id, family:column, timestamp)  cell. On the other hand, each table in SQL or HiveQL has a fixed relational model, and consequently HBase can introduce large space overheads (sometimes as large as 3x) compared to storing the same table in HDFS.

* *Query performance*. Query processing on HBase can be much (sometimes 3~5x) slower than that on HDFS due to various reasons. One of the reason is related to how HBase handles data accesses – HBase provides very good support for high concurrent read/write accesses; consequently, one needs to pay some amount of overheads (e.g., concurrency control) for each column read. On the other hand, data accesses in analytical query processing are predominantly read and append, and should preferably avoid the column read overheads.

To address these issues, we have implemented a document-oriented store on HBase for better query processing. Each document is encoded using a serialization framework (such as Avro or protocol Buffers), and its schema is stored separately (just once); consequently, each column in a relational table can be mapped to a field in some document without additional storage overheads. Internally, each document is mapped to a HBase column and is the unit for update; consequently, the associated read overheads can be amortized across different fields in a document.

Under Project Panthera, we will gradually make our implementation available as an extension to HBase (<https://github.com/intel-hadoop/hbase-0.94-panthera>). Specifically, today’s release provides document store support in HBase by utilizing co-processors, which brings up-to 3x reduction in storage space and up-to 1.8x speedup in query processing. Going forward, we will also use [HBase-6800](https://issues.apache.org/jira/browse/HBASE-6800) as the umbrella JIRA to track our efforts to get the document store idea reviewed and hopefully incorporated into Apache HBase.
