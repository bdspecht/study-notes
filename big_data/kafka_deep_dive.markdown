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
  - Topic configs - https://kafka.apache.org/documentation/#topicconfigs
  - `__consumer_offsets` is a topic used internally to store the offsets
  - List topics without a leader
    - bin/kafka-topics.sh --zookeeper zookeeper1:2181/kafka --describe --unavailable-partitions

### Topic Configurations
  - Kafka Documentation - Managing Consumer Groups
    - `https://kafka.apache.org/documentation/#basic_ops_consumer_group`
  - List all the consumer groups
    - `bin/kafka-consumer-groups.sh --bootstrap-server kafka1:9092 --list`
  - Reset offsets for a consumer group
    - `bin/kafka-consumer-groups.sh --bootstrap-server kafka:9092 --reset-offsets --group application1 --topic topic-1 --to-latest`

## Storage Administration


