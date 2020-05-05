# Linux Academy - Big Data Essentials

## Introduction to Big Data
### What is Big Data?
 - 4 V's of Big Data
   - Volume - extremely large volumes of data
   - Variety - various forms of dat, structured, semi-structured and unstructured
   - Velocity - real time, batch, streams of data
   - Veracity or variability - inconsistent, sometimes inaccurate, varying data
 - Why is Big Data important?
   - Gives value only when it is analyzed
 - Sources of Big Data
   - Social media
   - Machine-generated data
   - Business transactions
   - IOT
   - Sensors
 - Formats of big data
   - Structured
     - Data that has a defined length and format, such as numbers, words, and dates.
     - Easy to store and analyze
     - Often managed using SQL
   - Semi-structured
     - Does not conform toa  specific format but is self-describing using simple-key value pairs.
     - Examples are EDI, SWIFT, and XML
   - Unstructured
     - Follows no specific format
     - Examples are audio, images, text messages, X-Ray images etc.
 - Big data analytics
   - Basic analytics - reporting, dashboards, simple visualizations, slicing and dicing
   - Advanced analytics - complex analytics models using machine learning, statistics, text analytics, neural networks, and data mining
   - Operationalized analytics - Embed big data aalytics in a business process to streamline and increase efficiency
   - Analytics for business decisions - implemented for better decision-making that drives revenue

### What is IoT?
 - IOT is a growing network of connected devices that communicate with each other
 - Examples include sensors embedded in a wide variety of things that measure parameters and send it across to applications
 - IOT data is characterized by high volume, high velocity, high variety and high veracity
 - IOT sources are expected to reach 20 billion by 2020
 - Like big data, IOT data needs to be processed and analyzed to yield benefits.

### Big Data Explained with Use Cases
### Big Data Trends

## Big Data Architectures and Models
### Big Data Architectures
### Big Data in the Cloud

## Big Data Tools and Technologies
### Overivew of Apache Hadoop
### HDFS
#### Hadoop Distributed File System
 - Structured like a regular Unix like file system with data storage distributed across server machines
 - A data service that sits atop regular file systems, allowing a fault tolerant, resilient, clustered, etc.
 - Detection of faults and quick automatic recovery
 - Tuned to support large files
 - Follows the write once, read multiple teams approach simplifying data coeherency issues and enabling high throughput data access
 - Optimized for throughput rather than latency
   - Suited for long running batch operations on large scale data rather than interactive analysis on streaming data
 - Moving computation near the data reduces networking congestion and increases throughput

#### HDFS Architecture
 - Master-slave architecture, where Namenode is the master and Datanodes are slaves
 - Files are split into blocks and blocks are stored on datanodes
 - Datanodes manage storage attached to the nodes that they run on
 - Namenode controls all metadata, including what blocks make up a file
 - Namenode executs file system operations like opening, closing, and renaming files and directories
 - Datanodes serve read and write requests from clients
 - Datanodes perform block creation, deletion, replication upon instruction from the Namenode
 - A cluster contains a single namenode and multiple datanodes

#### File System and replication in HDFS
 - Supports a traditional hierarchical file system
 - Stores each file as a sequence of blocks, where blocks are replicated for fault tolerance
 - Namenode makes all decisions regarding replication of blocks
 - Checks heartbeats in cluster

### MapReduce
### YARN
### Overview of the Hadoop Ecosystem
### Hive, Pig, and MapReduce

### Scoop, Flume, Kafka, and Storm
#### Apache Sqoop
 - Designed to support bulk import of data into HDFS from structured data stores such as relation databases, enterprise data warehouses, and NoSQL systems
 - Also used to export from Hadoop file system to relation databases
 - Sqoop import
   - Imports individual tables from RDBMS to HDFS
 - Sqoop export
   - Exports from HDFS back to an RDBMS
#### Apache Sqoop Connectors
 - Data transfer between Sqoop and external storage system is made possible with the help of connectors
 - Sqoop has connectors for working with a range of relation databases
 - Generic JDBC connector for connecting to any database that supports Java's JDBC protocol

#### Apache Flume
 - Provides data ingestion mechanism for collecting aggregating and transporting large amounts of streaming data such as log files, events from various sources to a centralized data store.
 - Flume supports large set of source and destination multiple sources
   1. tail - pipes data from local file and write into HDFS via Flume
   2. System logs - Apache log4j
   3. Social networking sites
   4. Destinations include HDFS and HBase
 - Transactions in Flume are channel-based where two transactions are maintained for each message (one sender and one reciever.
   - Gurantees reliable message delivery
 - When the rate of incoming data exceeds the rate at which data can be written to the destination, Flume acts as a mediator between data producers and the centralized stores and provides a steady flow of data between them.

#### Apache Flume architecture
 - Events from external source are consumed by Flume Data Source. The external source sends events to Flume source in a format that is recognized by the target source
 - Flume Source receives an event and store it into one or more channels. The channel acts as a store which keeps the event until it is consumed by the flume sink.
 - Flume sink removes the event from the channel and stores it into an external repository like HDFS.
   - There could be multiple flume agents, in which case flume sink forwards the event tot he flume source of the next flume agent in the flow
 - Flexible design based upon streaming data
 - Flume has its own query processing engine which makes it easy to transform each new batch of data

#### Apache Kafka
 - Developed to address shortcomings of Flume
 - Distributed streaming flatform
   - Let's you publish and subscribe to streams of records
   - Let's you store streams of records in a fault-tolerant way
   - Let's you process streams of records as they occur
 - Run as a cluster on one or more servers
 - Stores streams of records in categories called *topics*
 - Each records consists of a key, a value, and a timestamp
 - Has four core APIs
   - Producer API
     - Allows an application to publish a stream of records to one or more Kafka topics
   - Consumer API
     - Allows an application to subscribe to one or more topics and process the stream of records produced to them
   - Stream API
     - Allows an application to act as a stream processor, consuming an input stream from one or more topics and producing an output stream for topics. Transforming input streams to output streams.
   - Connector API
     - Allows building and running reusable producers or consumers that connect Kafka topics to existing applications

#### Apache Storm
 - Real time message computation system for processing fast, large streams of data
   - Not a queue
   - Consumes streams of data nad processes them in complex ways
 - Spout
   - Source of streams of in a computation.
   - Can ready from a queueing broker such as Kafka
 - Bolt
   - Processes any number of input streams and produces any number of new output streams
   - Much of the logic goes into bolts
 - Topology
   - A network of spouts and bolts
   - Run indefinitely when deployed

#### When to use which tool?
 - Sqoop
   - Used when data is sitting in data stores like RDBMS
 - Storm
   - Real time message computation system for processing fast, large streams of data but not a queue
 - Flume
   - Moving bulk streaming data from various sources like web servers and social media
   - Pros
     - Tightly integrated with Hadoop and HDFS security
     - Supported by a number of enterprise Hadoop providers
     - Supports built-in sources and sinks out of the box
     - Makes event filtering and transforming very easy
   - Cons
     - Not as scalable for adding additional consumers
     - Lesser message durability than Kafka
 - Kafka
   - Consumers pull data from the topics, different consumers can consum the messages at different pace
   - Flume sink supports push model, which does not scale

### Ambari, Oozie and Zookeeper

#### Ambari
 - Makes Hadoop management simpler by providing software for provisioning, managing, and monitoring of Hadoop clusters

#### Oozie
 - Serves as a workflow engine that schedules, runs and manages jobs on Hadoop
 - Can combine multiple complex jobs to be run in a sequential order to achieve a bigger task.
 - Tightly integrated with Hadoop stack supporting various Hadoop jobs like MapReduce, Hive, Pig, Sqoop as well as system-specific jobs like Java and Shell

#### Zookeeper
  - Provides cordination and operational services between distributed processes on a Hadoop cluster
  - Naming service
    - Identify nodes in a cluster by name
  - Configuration management
    - Synchronizes configuration between nodes, ensuring consistent configuration
  - Process synchronization
    - coordinates the starting and stopping of multiple nodes int he cluster
  - Self election
    - Assigns a leader role to on of the nodes. This leader/master handles all client requests on behalf of the cluster
  - Reliable messaging
    - Fulfills ned for communication between and among the nodes in the cluster specific to the distributed application
  - Offers a publish/subscribe  capability that allows the creation of a queue
    - This queue guarantees message delivery even in the case of a node failure.

### Overview of NoSQL Databases
### Overview of Apache Spark

## Analytics
### Analyzing Big Data

