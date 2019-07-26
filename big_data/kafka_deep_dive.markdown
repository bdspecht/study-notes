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
