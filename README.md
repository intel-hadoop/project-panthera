# Project Panthera: Better Analytics with SQL and Hadoop #

We have worked closely with many enterprise users over the past few years to enhance their new data analytics platforms using the Hadoop stack. Increasingly, these platforms have evolved from a batch-style, custom-built system for unstructured data, to become an integral component of the enterprise application framework. While the Hadoop stack provides a solid foundation for these platforms, gaps remain; in particular, enterprises are looking for full SQL support to seamlessly integrate these new platforms into their existing enterprise data analytics infrastructure. Project Panthera is our open source efforts to provide efficient support of standard SQL features on Hadoop, so as to enable many important, advanced use cases not supported by Hadoop today, including:

* Exploring data with complex and sophisticated SQL queries (such as nested subqueries with aggregation functions) – for instance, about half of the queries in TPC-H (a standard decision support benchmark) use subqueries

* Efficient storage engine for high-update rate SQL query workloads – while HBase is often used to support such workloads, query processing (e.g., Hive) on HBase can incur significant overheads as the storage engine completely ignores the SQL relational model

*	Utilizations of new hardware platform technologies (e.g., new flash technologies and large RAM capacities available in modern servers) for efficient SQL query processing

The objective of Project Panthera is to collaborate with the larger Hadoop community in enhancing the SQL support of the platform for a broader set of use cases.  We are building these new capabilities on top of the Hadoop stack, and contributing necessary improvements of the underlying stack back to the existing Apache Hadoop projects. Our initial goals are:

* An analytical SQL engine (ASE) for Hadoop (<https://github.com/intel-hadoop/project-panthera-ase>), which provides full SQL support for Hadoop-based OLAP applications and currently uses Hive as the execution backend

*	Document-oriented table (DOT) for HBase (<https://github.com/intel-hadoop/project-panthera-dot>), which provides an efficient storage engine for relational SQL query with high-update rate and document semantics, and is currently implemented as an HBase co-processor application
