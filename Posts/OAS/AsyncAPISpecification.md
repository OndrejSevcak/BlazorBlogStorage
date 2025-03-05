---
title: "Co je AsyncAPI a jak se liší od OpenAPI"
description: "Co je AsyncAPI a jak se liší od OpenAPI"
author: "Ondrej Sevcak"
date: "2025-02-23"
category: "OAS"
hashtags: "#AsyncAPI #OAS #Backend"
publish: true
---

#### Co je AsyncAPI

- otevřený standard pro definování a dokumentování asynchronních API
- podobně jako OpenAPI definuje synchronní RESTful API
- poskytuje strukturovaný způsob popisu architektur řízených událostmi (EDA), jako jsou ty, které používají message brokery, event streamy a WebSockets.

**Použití**

Používá se pro publish-subscribe (Pub/Sub), message queues a event-driven mikroslužby, které komunikují asynchronně prostřednictvím brokerů jako Kafka, RabbitMQ, MQTT, NATS nebo WebSockets.

**Příklad**

- jednoduchý AsyncAPI dokument pro službu půjčovny aut, která publikuje události o dostupnosti aut do Kafka message brokeru

- Kanál car/available odesílá události o dostupnosti aut.
- Konzumenti (odběratelé) dostávají aktualizace v reálném čase, když je auto k dispozici.

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

#### Co je Kafka

- distribuovaná platforma pro event streaming používaná pro vysokou propustnost, odolnost proti chybám a streamování dat v reálném čase.
- je široce používána pro architektury řízené událostmi, message brokering a analýzy v reálném čase.

**Klíčové vlastnosti**:

- Publikování a odběr streamů záznamů (událostí/zpráv)
- Spolehlivé ukládání událostí v distribuovaném clusteru
- Zpracování streamů událostí v reálném čase

**Jak Kafka funguje**

Kafka funguje na modelu Pub/Sub (Publish-Subscribe), kde producenti posílají zprávy do témat a konzumenti se přihlašují k těmto tématům, aby přijímali zprávy asynchronně.

**Klíčové komponenty Kafka**

- **Producer** → Odesílá (publikuje) zprávy do Kafka témat.

- **Broker** → Kafka servery, které ukládají a distribuují zprávy.

- **Topic** → Pojmenovaný kanál, kde jsou zprávy organizovány.

- **Partition** → Téma je rozděleno do více partition pro škálovatelnost.

- **Consumer** → Čte (přihlašuje se k) zprávy z Kafka témat.

- **Consumer Group** → Více konzumentů může pracovat společně, každý čte různé partition tématu pro paralelní zpracování.

- **Zookeeper** → Spravuje metadata a koordinaci brokerů (není potřeba v novějších verzích s KRaft mode).

#### Jak zprávy proudí v Kafka
1. Producent publikuje zprávy do Kafka topicu.
2. Kafka ukládá zprávy do partition, což zajišťuje škálovatelnost a paralelismus.
3. Konzumenti se přihlašují k topicům a čtou zprávy asynchronně.
4. Zprávy jsou uchovávány po definovanou dobu, což umožňuje konzumentům je zpracovávat vlastním tempem.

<br>
<br>

#### Jak používat Kafku v .NET webové aplikaci

- Krok 1: Instalace potřebných balíčků

Použijte **Confluent.Kafka**, oficiální Kafka klient pro .NET.

```sh
dotnet add package Confluent.Kafka
```

---

 - Krok 2: Konfigurace Kafka producenta v .NET

Vytvořte **Kafka producenta** pro odesílání zpráv.

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

 - Krok 3: Konfigurace Kafka konzumenta v .NET

Vytvořte **Kafka konzumenta** pro čtení zpráv.

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

 - Krok 4: Spuštění Kafka lokálně
Pro testování Kafka lokálně použijte **Docker**:

```sh
docker-compose up -d
```

Nebo nainstalujte Kafka ručně a spusťte **Kafka broker**:

```sh
bin/zookeeper-server-start.sh config/zookeeper.properties
bin/kafka-server-start.sh config/server.properties
```

**Případy použití Kafka ve službě půjčovny aut**  
- **Aktualizace řízené událostmi** – Upozornění uživatelů na dostupnost aut.  
- **Analýzy v reálném čase** – Sledování rezervací a trendů půjčování.  
- **Komunikace mikroslužeb** – Oddělení služeb pro lepší škálovatelnost.


