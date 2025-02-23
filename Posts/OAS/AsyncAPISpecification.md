---
title: "What is AsyncAPI"
description: "What is AsyncAPI and how it differs from OAS"
author: "Ondrej Sevcak"
date: "2025-02-23"
category: "OAS"
hashtags: "#AsyncAPI #OAS #Backend"
---

## What is AsyncAPI

- open standard for defining and documenting asynchronous APIs
- similar to how OpenAPI defines synchronous RESTful APIs 
- it provides a structured way to describe event-driven architectures (EDAs), such as those using message brokers, event streams, and WebSockets.

**Usage**

It is used for publish-subscribe (Pub/Sub), message queues, and event-driven microservices that communicate asynchronously via brokers like Kafka, RabbitMQ, MQTT, NATS, or WebSockets.

**Example**

- simple AsyncAPI document for a car rental service that publishes car availability events to a Kafka message broker

- The car/available channel sends car availability events.
- Consumers (subscribers) receive real-time updates when a car is available.

```yaml
asyncapi: 2.0.0
info:
  title: Car Rental Event API
  version: 1.0.0
  description: An API to publish car availability events
servers:
  production:
    url: kafka://broker.carrental.com:9092
    protocol: kafka
channels:
  car/available:
    description: Channel for car availability updates
    subscribe:
      summary: Receive notifications when a car becomes available
      message:
        contentType: application/json
        payload:
          type: object
          properties:
            carId:
              type: string
            available:
              type: boolean
            timestamp:
              type: string
              format: date-time
```

## What is Kafka

- a distributed event streaming platform used for high-throughput, fault-tolerant, real-time data streaming. 
- it is widely used for event-driven architectures, message brokering, and real-time analytics.

**Key features**:
- Publishing and subscribing to streams of records (events/messages)
- Storing events reliably in a distributed cluster
- Processing streams of events in real time

**How Kafka Works**

Kafka operates on a Pub/Sub (Publish-Subscribe) model, where producers send messages to topics, and consumers subscribe to these topics to receive messages asynchronously.

**Key Kafka Components**

- **Producer** → Sends (publishes) messages to Kafka topics.

- **Broker** → Kafka servers that store and distribute messages.

- **Topic** → A named channel where messages are organized.

- **Partition** → A topic is split into multiple partitions for scalability.

- **Consumer** → Reads (subscribes to) messages from Kafka topics.

- **Consumer** Group → Multiple consumers can work together, each reading different partitions of a topic for parallel processing.

- **Zookeeper** → Manages metadata and broker coordination (not needed in newer versions with KRaft mode).

### How Messages Flow in Kafka
1. Producer publishes messages to a Kafka topic.
2. Kafka stores messages in partitions, ensuring scalability and parallelism.
3. Consumers subscribe to topics and read messages asynchronously.
4. Messages are retained for a defined period, allowing consumers to process them at their own pace.

<br>
<br>


## How to Use Kafka in a .NET Web Application

### Step 1: Install Required Packages
Use **Confluent.Kafka**, the official Kafka client for .NET.

```sh
dotnet add package Confluent.Kafka
```

---

### Step 2: Configure Kafka Producer in .NET
Create a **Kafka producer** to send messages.

```csharp
using System;
using System.Threading.Tasks;
using Confluent.Kafka;

class KafkaProducer
{
    public static async Task SendMessageAsync(string topic, string message)
    {
        var config = new ProducerConfig
        {
            BootstrapServers = "localhost:9092"
        };

        using var producer = new ProducerBuilder<Null, string>(config).Build();
        
        var result = await producer.ProduceAsync(topic, new Message<Null, string> { Value = message });

        Console.WriteLine($"Message sent to {result.TopicPartitionOffset}");
    }

    static async Task Main()
    {
        await SendMessageAsync("car-rentals", "New rental request received!");
    }
}
```

---

### Step 3: Configure Kafka Consumer in .NET
Create a **Kafka consumer** to read messages.

```csharp
using System;
using System.Threading;
using Confluent.Kafka;

class KafkaConsumer
{
    public static void ConsumeMessages(string topic)
    {
        var config = new ConsumerConfig
        {
            BootstrapServers = "localhost:9092",
            GroupId = "car-rental-group",
            AutoOffsetReset = AutoOffsetReset.Earliest
        };

        using var consumer = new ConsumerBuilder<Ignore, string>(config).Build();
        consumer.Subscribe(topic);

        try
        {
            while (true)
            {
                var consumeResult = consumer.Consume(CancellationToken.None);
                Console.WriteLine($"Received message: {consumeResult.Value}");
            }
        }
        catch (OperationCanceledException)
        {
            consumer.Close();
        }
    }

    static void Main()
    {
        ConsumeMessages("car-rentals");
    }
}
```

---

### Step 4: Run Kafka Locally
To test Kafka locally, use **Docker**:

```sh
docker-compose up -d
```

Or install Kafka manually and start the **Kafka broker**:

```sh
bin/zookeeper-server-start.sh config/zookeeper.properties
bin/kafka-server-start.sh config/server.properties
```

**Use Cases for Kafka in Car Rental Service**  
- **Event-driven updates** – Notify users about car availability.  
- **Real-time analytics** – Track bookings and rental trends.  
- **Microservices communication** – Decouple services for better scalability.  


