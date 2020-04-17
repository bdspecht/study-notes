# Apache Kafka Deep Dive
## The Basics of Apache Kafka
### At First Glance
#### What is Kafka?
 - Producer
   - Sends messages to Kafka
 - Consumer
   - Retrieves messages from Kafka
 - Stream Processor
   - Producing messages into output streams
 - Connector
   - Connecting topics to existing applications
#### Application Metrics
#### Messages and Schemas
  - Message delivery can take at least one of the following delivery methods
    - At least once
      - A producer can send the same message more than once
      - If the message fails or is not acknowledged, the producer can send the message again
      - The consumer may need to eliminate duplicates
    - At most once
      - Producer sends the message once and never retries
      - If the message fails to send or isn't acknowledged, then the producer will never send the message again
    - Exactly once
      - Even if a producer sends a message more than once, the consumer will only receive the message once
  - Kafka is able to process millions of messages per second
  - Different consumers may access the same messages

#### Topics and Partitions
  - Topics
    - Message streams in Kafka
  - Partitions
    - Topics are divided into partitions, with each message receiving an incremental ID called the offset
  - When a nes message is written:
    - It is kept for one week
    - It can't be altered
    - The ID will increment infinitely
    - It is randomly assigned to a partition, unless a key is provided

#### Producers and Consumers
  - Producer
    - A client that writes data to the Kafka cluster
  - Consumer
    - The application that consumes or reads the messages
    - Subscribes to one or more topics and reads the messages in the order in which they are produced
  - Acks
    - Producers can choose whether to receive a confirmation of delivery
  - Key
    - Indicates that a message will go to the same partition every time
  - Consumer group
    - Ensures multiple consumers aren't reading the same message, consumer groups map reads to consumers

#### Brokers and Clusters
  - Broker
    - Receives messages from the producer, assigns offsets, and sotres the messages on disk
    - Brokers are designed to operate in a cluster
      - One broker is assigned the controller role
    - Brokers will replicate data across brokers
  - Controller
    - Responsible for managing the states of partitions and replicas and for performing administrative tasks like reassigning partitions
  - Leader
    - Each partition has one server which acts as the "leader" and zero or more servers which act as "followers".
    - The leader handles all read and write requests for the partition while the followers passively replicate the leader.
    - If the leader fails, one of the followers will automatically become the new leader. Each server acts as a leader for some of its partitions and a follower for others so load is well balanced within the cluster.

### Installing Apache Kafka
#### Cluster Setup Overview
  - Zookeeper
    - Keeps data in-sync
    - Leader election
    - Seperate install
    - Can be installed indeependently
    - Must be an odd number
  - Kafka
    - Requires Zookeeper
    - Can have hundreds of Brokers
    - Kafka to Zookeeper is not 1 to 1
    - Latency and network I/O is important

#### Breakdown of Kafka commands
  - Getting details for the topics command
    - `bin/kafka-topics.sh`
  - Creating a topic with all the required arguments
    - `bin/kafka-topics.sh --zookeeper zookeeper1:2181/kafka --topic test1 --create --partitions 3 --replication-factor 3`
  - Creating a topic including all of the zookeeper servers (not required)
    - `bin/kafka-topics.sh --zookeeper zookeeper1:2181,zookeeper2:2181,zookeeper3:2181/kafka --topic test1 --create --partitions 3 --replication-factor 3`
  - List all topics
    - `bin/kafka-topics.sh --zookeeper zookeeper1:2181/kafka --list`
  - Describing a topic
    - `bin/kafka-topics.sh --zookeeper zookeeper1:2181/kafka --topic test2 --describe`
  - Delete a topic
    - `bin/kafka-topics.sh --zookeeper zookeeper1:2181/kafka --topic test2 --delete`
  - Detail for the producer command
    - `bin/kafka-console-producer.sh`
  - Detail for the consumer command
    - `bin/kafka-console-consumer.sh`
  - Detail for the consumer groups command
    - `bin/kafka-consumer-groups.sh`

### Taking a closer look
#### Brokers
 - Each broker is assigned an ID
 - When there are multiple brokers in a cluster, one is elected as the Controller.
 - Controller is responsible for electing the leader of the partition
    - Set `auto.create.topics.enabled` to `false` in production
    - Set `unclean.leader.election.enabled` to `false` in production
      - Ensures broker is healthy before assigning partition leader

#### Replicas
 - Provides guarantees for the availability of data
 - The replication factor provides a way for the Kafka admin to control the availability of that data in the event of failure
 - Goals for placement of replicas
   - Spread evenly amongst brokers
   - Each partition is on a different broker
   - Put replicas on different racks
    - Use `broker.rack` in server.properties to spread data evenly

#### Handling requests
 - Breakdown of a request
   - Acceptor thread - Creates the connection from client to Broker
   - Processor thread - Takes requests from clients and places into request queue
   - IO thread - Processes the requests in tehe requests queue
 - Request queue
   - Requests waiting to be processed
 - Response queue
   - Requests waiting to be sent back to the clients

#### Partitions
 - Parititons within a topic are split between brokers
 - The partitions themselves will never be split up
 - The partitions will continue to get larger as the number of messages grow
 - Messages will never be removed frmo the partition, only appended
 - Messages are stored in the directory specified in the `server.properties` file (`log.dirs`)
 - Commands
   - Output the data from the partition log
     `./bin/kafka-run-class.sh kafka.tools.DumpLogSegments --print-data-log --files log_file
   - Rotate the logs
     `./bin/kafka-configs.sh --zookeeper zookeeper1:2181/kafka --alter --entity-type topics --entity-name test --add-config segment.ms=60000`

### Data Delivery
#### Reliability
  - Kafka provides a guarantee that the message is "committed"
  - The admin can turn off this guarantee to increase throughput, but at the risk of lossing messages
  - Guarantees
    - `acks=0` No ack, guarantees are not made, retries are not attempted
    - `acks=1` Leader ack, guarantee for the leader only. Messages lost if leader is corrupted
    - `acks=all` All replicas ack, the leader will wait until all replicas acknowledge they have received the message

#### Integrity
  - Getting the messages all the way through from the producer to consumer
  - Committed offset
    - Sent to Kafka by the consumer to acknowledge that it received and processed all messages in the partition up to that offset
  - Committed messages
    - An ack sent by the leader replica indicating to the broker that the message has been committed and is ready to be consumed
  - Offset read
    - A consumer can start reading an offset but never finish, which is considered a lost message
  - Offset processed
    - To completely process the message, the consumer must committ the message

#### Security
  - All disabled by default
  - SSL and SASL auth is supported between
    - Clients (producers and consumers) and Brokers
    - Brokers
    - Brokers and Tools
  - Application is supported from
    - Brokers to Zookeeper servers
  - Authorization is supported for
    - Read/write operations by clients
    - External authorization services
  - Supported Auth
    - SASL/PLAIN username/pass
    - SASL/GSSAPI kerberos windows AD
    - SASL/SCRAM-SHA-256 more secure user/pass
    - SASL/OAUTHBEARER json web tokens
  - Encryption of data is supported in-transit between
    - Brokers and clients
    - Brokers
    - Brokers and tools using SSL
      - Performance degradation occurs when SSL is enabled

#### Data types
  - Schemas
    - Avro
      - Commonly used to deal with alter statements
      - Makes sure that the data is in a format that the client understands
      - Handles the schemas
      - Handles the storage of the schemas
      - Requires adding the dependency and plugin
    - Producer serializes the data, and consumer deserializes the data

### Producers and Consumers
#### Kafka Connect
  - An API that interacts with the Kafka cluster, connects other datasources to your Kafka cluster
  - Useful in cases where you don't have control of the producers or consumers
  - Connectors
    - Start the tasks to move large amounts of data in parallel to and from brokers
  - Tasks
    - Get data in and out of the Kafka cluster
    - Receives context from the worker
  - Workers
    - Processes that run the container and tasks
    - Handle HTTP requests to define connectors

#### File Source and File Sink Connectors
  - Sink Connector
    - A sink connector delivers data from Kafka topics into secondary indexes (like Elasticsearch) or batch systems such as Hadoop for offline analysis
  - Getting data out of a Kafka cluster is easy with Connect.
    - It has the ability to output to a file, Hadoop, Elasticsearch, and more

## Kafka Administration
### Topic Administration
#### Topic tools
  - Topic configs - https://kafka.apache.org/documentation/#topicconfigs
  - `__consumer_offsets` is a topic used internally to store the offsets
  - List topics without a leader
    - bin/kafka-topics.sh --zookeeper zookeeper1:2181/kafka --describe --unavailable-partitions

#### Topic Configurations
  - Kafka Documentation - Managing Consumer Groups
    - `https://kafka.apache.org/documentation/#basic_ops_consumer_group`
  - List all the consumer groups
    - `bin/kafka-consumer-groups.sh --bootstrap-server kafka1:9092 --list`
  - Reset offsets for a consumer group
    - `bin/kafka-consumer-groups.sh --bootstrap-server kafka:9092 --reset-offsets --group application1 --topic topic-1 --to-latest`

### Storage Administration
#### File Formats and Indexes
  - We'll see segment files accrue as messages are produced.
    - You can set file compaction to maintain the over time.
  - segment files
    - Index file
      - /data/kafa/foo/00000.index
      - You can wipe out .index files and they'll be automatically recreated
    - Log segment
      - /data/kafka/foo/00000.log
    - Compaction
      - /data/kafka/foo/001.timeindex.deleted
      - By default, messages are kept for one week and further compaction can occur

#### File management
  - Time retention
    - `log.retention.hours`
  - Size retention
    - `log.retention.bytes`
  - The segment we are currently writing to is called the active segment.
    - The segment is deleted when it reaches a size of 1GB or has been inactive for 1 week, whichever comes first
  - The Broker has an open file handle to each segment in every partition, even inactive segments
      - Ensure the OS has appropriate open file limits

#### Storage Structures
  - Batch processing and operational workloads
    - Lambda Architecture
      - View real-time and historical data
      - This adds difficulty due to the many simultaneous interfaces
  - Version 1 and Version 2 in Parallel
    - Kappa Architecture
      - Switch your view as a cut-over
      - This helps with migration or transitions to coninuous jobs
  - Multi-Cluster
    - Multiple Consumption
      - Replicate clusters for scale
      - Ingesting data from different Kafka clusters
    - The potential upside is bandwidth savings
  - Using EBS in AWS to move block volume if broker fails

### Stream Processing
#### How Streams Work
  - A stream is nothing but a sequence of events
  - Types of streams
    - Credit card transactions
    - Stock trades
    - Package deliveries
    - Network events
    - Sensor events in manufacturing equipment
    - Emails sent
    - Moves in a game
  - Brokers should be using the same timezone
  - Two types of state
    - Local (very fast)
    - External (ie Cassandra DB)
      - Unlimited size, access from different apps but latency issues
  - Contain a history of changes
  - Converting table to stream
    - Turn insert, update, delete into streams

#### Design Patterns
  - Single event processing
    - Filter pattern
  - Local state processing
  - Multiphase processing
  - External processing
  - Windowed join
  - Out of sequence events

#### Frameworks
  - Ingest
    - Gets data from one system to other
    - Ingest problem, try Kafka Connect
  - Low millisecond
    - Works for applications requiring quick response
    - Request-response
  - Real-time data analytics
    - Supports performing complex aggregation of data to gain insight
  - Asynchronous microservices
    - Performs a single action to serve a single need
    - Requires local state caching events

### Data Replication
#### Multi-Cluster Architectures
  - Never setup brokers in multiple regions
  - Hub and spoke
    - Multiple clusters (London, New York, AWS) send back to a central Kafka cluster
  - Active-Active
    - Apps write to a specific cluster, and data is synced between clusters
  - Active-Standby
    - All apps write to a single instance, and data is synced to slave

#### MirrorMaker
  - Tool to replicate your data to a different cluster
    - Collection of consumers in a consumer group.
      - The group reads data from the set of topics you specify and Mirromaker creastes the thread and sends it to the target cluster
      - One thread per consumer
  - Comes with the Kafka binaries and requires a lot of configuration for tuning the throughput
  - Ensure there is a very low latency to produce the synchronization you need
  - Configuration
    - Typically installed as  a service
    - *Always* runs at the destination datacenter
    - Biggest task will be monitoring lag to ensure the destination cluster is not falling behind the source

### Monitoring
#### Cluster and Broker Monitoring
  - Metrics for the application can be obtained from the JMX
  - Kafka uses Yammer Metrics for metrics reporting

#### Broker Metrics
  - Active controller count
    - Is the broker the controller?
  - Request handler idle ratio
    - How much load is the broker under?
  - All topics bytes in
    - Do I need to scale up the number of brokers?
  - All topics bytes out
    - How high is consumer traffic?
  - All topics messages in
    - How many messages per second?
  - Partition count
    - How many partitions are assigned to a broker?
  - Leader count
    - How many partitions is this broker a leader for?
  - Offline partitions
    - How many brokers have no leader?
  - Request metrics
    - How many requests are going to the broker?

#### Java monitoring
  - It's important to monitor the producers and consumers as well
  - Important beans to include in your monitoring suite
    - java.lang:type=GarbageCollector,name=G1 Old Generation
    - java.lang:type=GarbageCollector,name=G1 Younug Generation
      - # of GC cycles
        - CollectionCount
      - Time spent in GC cycle
        - CollectionTime
  - JVM can provide some OS information through the `java.lang:type=OperatingSystem` bean
    - OpenFileDescriptorCount
    - MaxFileDescriptorCount

## Kafka Advanced Configuration
### Advanced Producers
  - To eliminate the possibility of duplicate messages, you can set `enable.idempotencea` to `true` and the consumer will delete duplicate messages
    - You can use this to ensure your messages arrive in their entirety
  - Producer configuration
    - Kafka documentation indicates whether certain configurations are high importance or low
    - Idempotence is listed as low importance due its effect on efficiency

### Batch Compression
  - Sending a batch as opposed to invidual messages is very important in Kafka
  - Sending larger batches with compression improves throughput
  - Batch size and timing
    - When multiple records are sent to the same partition, the producer can batch them together
    - `batch.size` configuration option controls the amount of memory used for each batch
    - `linger.ms` will help increase the batch size to get the best compression and throughput

### Serializer
  - Custom serialization can be sued to translave your data in a format that can be stored, transmitted, and retrieved
  - It's recommended to use a generic serialization libraries
    - Avro

### Producer Buffer Memory
 - If a producer is producing messages faster than the broker can recieve, the messages will be sent to a buffer in memory on the producer
 - `max.block.ms` If the producer is sending messages to the broker and the broker is not responding for 60 seconds, either because its buffer is filled, or broker is down, you will get an exception

### Advanced Consumers
#### Reading duplicate messages
  - A unique ID can be inserted into the code for the consumer, so that if a duplicate message is read, it is skipped and not committed twice.

#### Tracking offsets
  - Consumers work in a group to better coordinate the subscription of messages
  - Tracking offsets is done by a coordinartor
  - *commit*
   - When a consumer updates its current position in the partition
   - Produces a message to the *_consumer_offsets* topic
  - If a consumer crashes, it will trigger a re-balance and the consumer amy be assigned a new set of partitions

#### Partition rebalancing
  - A rebalance occurs when a consumer is reassigned because it's etiher dead or added to a new consumer group
  - Also occurs when a topic is modified

### Advanced Topics
#### Topic Design
  - Design considerations
    - Data accuracy
      - Making sure that events that should be ordered should end up in the same topic/partition
    - Popularity of events
    - Amount of data to process

