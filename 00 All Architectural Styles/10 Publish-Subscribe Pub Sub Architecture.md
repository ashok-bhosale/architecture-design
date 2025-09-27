### **.NET Core Application with Publish-Subscribe (Pub/Sub) Architecture using Kafka**

In a **Publish-Subscribe (Pub/Sub) architecture**, publishers send messages to a broker without knowing who the subscribers are. Subscribers receive messages based on topics they subscribe to. This pattern is useful for event-driven and distributed applications.

### **Technology Stack**

- **Backend**: .NET Core Web API
- **Broker**: Apache Kafka
- **Producer**: .NET Core application publishing messages
- **Consumer**: .NET Core application consuming messages

---

## **1. Install Kafka Locally (Optional)**

### **Using Docker**

```sh
docker-compose up -d
```

Create a `docker-compose.yml` file:

```yaml
version: '2'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    environment:
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
```

---

## **2. Create a .NET Core Kafka Producer (Publisher)**

1. Create a **.NET Core Web API Project**:
    
    ```sh
    dotnet new webapi -n KafkaPublisher
    cd KafkaPublisher
    ```
    
2. Install the Kafka client library:
    
    ```sh
    dotnet add package Confluent.Kafka
    ```
    
3. Modify `appsettings.json`:
    
    ```json
    {
      "Kafka": {
        "BootstrapServers": "localhost:9092",
        "Topic": "order-events"
      }
    }
    ```
    
4. Create `KafkaProducerService.cs`:
    
    ```csharp
    using System;
    using System.Text.Json;
    using System.Threading.Tasks;
    using Confluent.Kafka;
    using Microsoft.Extensions.Configuration;
    
    namespace KafkaPublisher.Services
    {
        public class KafkaProducerService
        {
            private readonly string _topic;
            private readonly IProducer<Null, string> _producer;
    
            public KafkaProducerService(IConfiguration configuration)
            {
                var config = new ProducerConfig
                {
                    BootstrapServers = configuration["Kafka:BootstrapServers"]
                };
    
                _producer = new ProducerBuilder<Null, string>(config).Build();
                _topic = configuration["Kafka:Topic"];
            }
    
            public async Task PublishAsync(object message)
            {
                var messageValue = JsonSerializer.Serialize(message);
                await _producer.ProduceAsync(_topic, new Message<Null, string> { Value = messageValue });
            }
        }
    }
    ```
    
5. Modify `Program.cs` to register KafkaProducerService:
    
    ```csharp
    using KafkaPublisher.Services;
    
    var builder = WebApplication.CreateBuilder(args);
    
    builder.Services.AddSingleton<KafkaProducerService>();
    var app = builder.Build();
    
    app.UseRouting();
    app.MapPost("/publish", async (KafkaProducerService producer, object message) =>
    {
        await producer.PublishAsync(message);
        return Results.Ok("Message Published");
    });
    
    app.Run();
    ```
    
6. Run the API:
    
    ```sh
    dotnet run
    ```
    
    Test it with:
    
    ```sh
    curl -X POST http://localhost:5000/publish -H "Content-Type: application/json" -d '{"orderId": 123, "status": "Created"}'
    ```
    

---

## **3. Create a .NET Core Kafka Consumer (Subscriber)**

1. Create a new **.NET Core Console App**:
    
    ```sh
    dotnet new console -n KafkaConsumer
    cd KafkaConsumer
    ```
    
2. Install Kafka client library:
    
    ```sh
    dotnet add package Confluent.Kafka
    ```
    
3. Modify `Program.cs`:
    
    ```csharp
    using System;
    using System.Threading;
    using Confluent.Kafka;
    
    class Program
    {
        static void Main(string[] args)
        {
            var config = new ConsumerConfig
            {
                BootstrapServers = "localhost:9092",
                GroupId = "order-consumer-group",
                AutoOffsetReset = AutoOffsetReset.Earliest
            };
    
            using var consumer = new ConsumerBuilder<Ignore, string>(config).Build();
            consumer.Subscribe("order-events");
    
            Console.WriteLine("Listening for messages...");
            while (true)
            {
                var consumeResult = consumer.Consume(CancellationToken.None);
                Console.WriteLine($"Received: {consumeResult.Message.Value}");
            }
        }
    }
    ```
    
4. Run the Consumer:
    
    ```sh
    dotnet run
    ```
    

---

## **4. How Pub/Sub Works**

- **Publisher (Producer)**: Sends event messages to Kafka (`order-events` topic).
- **Broker (Kafka)**: Stores and distributes messages.
- **Subscriber (Consumer)**: Reads messages from the topic asynchronously.

---

## **5. Deploying Kafka with Azure Event Hub**

For cloud-based Kafka, use **Azure Event Hubs** with Kafka support.

1. **Create an Event Hub** in Azure.
2. **Modify Producer Configuration**:
    
    ```csharp
    var config = new ProducerConfig
    {
        BootstrapServers = "YOUR_EVENT_HUB_NAMESPACE.servicebus.windows.net:9093",
        SecurityProtocol = SecurityProtocol.SaslSsl,
        SaslMechanism = SaslMechanism.Plain,
        SaslUsername = "$ConnectionString",
        SaslPassword = "YOUR_EVENT_HUB_CONNECTION_STRING"
    };
    ```
    

---

## **6. Summary**

✅ **Publish-Subscribe (Pub/Sub) pattern** ensures **decoupled communication**.  
✅ **Kafka** acts as the central broker for event-driven architecture.  
✅ **.NET Core Producer** sends events to Kafka.  
✅ **.NET Core Consumer** listens and processes events.

Would you like to add **retry mechanisms, dead-letter queues, or integrate this with microservices?** 🚀