# Intro

- distributed event streaming platform used for building real-time data pipelines, stream processing, and decoupled microservices communication
- high-throughput, fault-tolerant, distributed publish-subscribe messaging system

Event streaming- events (changes in state or actions) are continuously captured, stored, processed, and reacted to in real time using an event broker like Apache Kafka

Components

- Producer- Sends (publishes) messages to a topic
- Consumer- Reads (subscribes to) messages from a topic
- Topic- A logical stream/category of messages (e.g., orders, payments)
- Partition- A horizontal scaling unit of a topic, messages are split into partitions
- Broker- A single Kafka server, manages topics and partitions
- Consumer Group- A group of consumers that divide up consumption of a topic
- Offset- Position of a message in a partition
- ZooKeeper- coordinate and manage the Kafka cluster

Commands

```
# Create a topic named 'orders' with 3 partitions and replication factor 1
kafka-topics.sh --create \
  --topic orders \
  --bootstrap-server localhost:9092 \
  --partitions 3 \
  --replication-factor 1

# List all topics in the cluster
kafka-topics.sh --list \
  --bootstrap-server localhost:9092

# Delete the 'orders' topic (must have delete enabled in broker config)
kafka-topics.sh --delete \
  --topic orders \
  --bootstrap-server localhost:9092

# Start a Kafka producer and send messages to 'orders' topic
kafka-console-producer.sh \
  --topic orders \
  --bootstrap-server localhost:9092

# Send messages with key to 'orders' topic
kafka-console-producer.sh \
  --topic orders \
  --bootstrap-server localhost:9092 \
  --property "parse.key=true" \
  --property "key.separator=:"

# Start a consumer to read messages from 'orders' topic from beginning
kafka-console-consumer.sh \
  --topic orders \
  --bootstrap-server localhost:9092 \
  --from-beginning
```

| Kafka | RabbitMQ |
| --- | --- |
| Messages are stored on disk and can be replayed using offsets. | Messages are removed after being consumed (unless manually requeued or persisted). |
| High throughput and horizontal scalability with partitioned topics | Optimized for low latency, but not designed for handling huge volumes at Kafka’s scale |

Example

```
import { Injectable, OnModuleInit, OnModuleDestroy } from '@nestjs/common';
import { Kafka, Consumer, Producer } from 'kafkajs';

@Injectable()
export class KafkaService implements OnModuleInit, OnModuleDestroy {
  private kafka: Kafka;
  private producer: Producer;
  private consumer: Consumer;

  constructor() {
    // Create Kafka client instance
    this.kafka = new Kafka({
      clientId: 'nestjs-app',
      brokers: ['localhost:9092'], // Kafka broker
    });

    // Create producer and consumer instances
    this.producer = this.kafka.producer();
    this.consumer = this.kafka.consumer({ groupId: 'nestjs-consumer-group' });
  }

  // Runs when app starts
  async onModuleInit() {
    await this.producer.connect(); // Connect the producer
    await this.consumer.connect(); // Connect the consumer

    // Subscribe to topic
    await this.consumer.subscribe({ topic: 'test-topic', fromBeginning: true });

    // Start consuming messages
    await this.consumer.run({
      eachMessage: async ({ topic, partition, message }) => {
        console.log(`✅ Received message: ${message.value?.toString()}`);
      },
    });

    console.log('Kafka connected and consumer listening...');
  }

  // Runs when app stops
  async onModuleDestroy() {
    await this.consumer.disconnect();
    await this.producer.disconnect();
  }

  // Send message to Kafka topic
  async sendMessage(message: string) {
    await this.producer.send({
      topic: 'test-topic',
      messages: [{ value: message }],
    });

    console.log(`📤 Sent message: ${message}`);
  }
}
```

Dead Letter Queue (DLQ)- special queue (or topic) where failed or rejected messages are sent instead of being lost, so they can be reviewed, retried, or logged later

Message Acknowledgments

- Kafka producers can be configured to require acknowledgments when sending messages.
- There are three settings
  - acks=0: No acknowledgment (fire and forget)
    - The producer doesn't wait for any acknowledgment from the broker.
    - This option has the lowest latency but the weakest durability guarantees since the message may be lost if the broker goes down.
  - acks=1: Leader acknowledgment only
    - The producer waits for the leader to acknowledge the message.
    - This provides better durability than acks=0, but there's still a risk of message loss if the leader fails immediately after acknowledging but before the followers have replicated the message.
  - acks=all: Full ISR (In-Sync Replica) acknowledgment
    - The producer waits for the message to be acknowledged by all in-sync replicas.
    - This setting provides the strongest durability guarantee but has the highest latency.
