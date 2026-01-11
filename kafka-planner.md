# 🚀 15-Day Kafka Mastery Plan for Tech Lead Interviews

## 📊 Learning Strategy Overview

```
┌─────────────────────────────────────────────────────────────────┐
│              15-Day Kafka Mastery Roadmap                       │
├─────────────────────────────────────────────────────────────────┤
│  Days 1-3:    Fundamentals & Architecture                        │
│  Days 4-6:   Producers & Consumers (Deep Dive)                  │
│  Days 7-9:   Advanced Concepts & Patterns                       │
│  Days 10-12: Production-Ready (Monitoring, Scaling, Security)   │
│  Days 13-14: Real-World Scenarios & System Design               │
│  Day 15:     Interview Prep & Mock Questions                    │
└─────────────────────────────────────────────────────────────────┘
```

---

## **📅 DAYS 1-3: Foundations & Architecture**

### **Day 1: Core Concepts & Setup** ⏰ 4-5 hours

#### Morning (2 hours): Theory
```
Topics to Master:
├── What is Kafka?  (vs RabbitMQ, SQS, traditional messaging)
├── Use Cases:  Event streaming, Log aggregation, Real-time analytics
├── Key Components: 
│   ├── Broker
│   ├── Topic
│   ├── Partition
│   ├── Producer
│   ├── Consumer
│   ├── Consumer Group
│   └── ZooKeeper (and KRaft mode)
└── Message Structure: Key, Value, Headers, Timestamp, Offset
```

**Visual Understanding:**
```
┌─────────────────────────────────────────────────────────┐
│                    Kafka Cluster                        │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐                │
│  │Broker 1 │  │Broker 2 │  │Broker 3 │                │
│  └─────────┘  └─────────┘  └─────────┘                │
│                                                          │
│  Topic:  "orders"                                        │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐               │
│  │Partition0│ │Partition1│ │Partition2│               │
│  │ Msg1     │ │ Msg2     │ │ Msg3     │               │
│  │ Msg4     │ │ Msg5     │ │ Msg6     │               │
│  └──────────┘ └──────────┘ └──────────┘               │
└─────────────────────────────────────────────────────────┘
        ▲                           │
        │                           ▼
   [Producers]              [Consumer Groups]
```

#### Afternoon (2-3 hours): Hands-On

**Setup:**
```bash
# Option 1: Docker Compose (Recommended)
version: '3'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper: latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000

  kafka:
    image: confluentinc/cp-kafka: latest
    depends_on: 
      - zookeeper
    ports:
      - "9092:9092"
    environment: 
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
```

**Practice:**
```bash
# Create topic
kafka-topics --create --topic test-topic \
  --bootstrap-server localhost: 9092 \
  --partitions 3 \
  --replication-factor 1

# Produce messages
kafka-console-producer --topic test-topic \
  --bootstrap-server localhost:9092

# Consume messages
kafka-console-consumer --topic test-topic \
  --bootstrap-server localhost: 9092 \
  --from-beginning
```

**Resources:**
- ✅ [Kafka Official Docs - Introduction](https://kafka.apache.org/intro)
- ✅ Watch:  "Apache Kafka in 5 Minutes" (YouTube)

---

### **Day 2: Deep Dive - Topics, Partitions & Replication** ⏰ 5-6 hours

#### Theory (3 hours):

```
┌─────────────────────────────────────────────────────────┐
│             Partition & Replication                     │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  Topic:  "payments" (3 partitions, RF=3)                │
│                                                          │
│  Broker 1         Broker 2         Broker 3            │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐         │
│  │ P0(L)    │    │ P0(F)    │    │ P0(F)    │         │
│  │ P1(F)    │    │ P1(L)    │    │ P1(F)    │         │
│  │ P2(F)    │    │ P2(F)    │    │ P2(L)    │         │
│  └──────────┘    └──────────┘    └──────────┘         │
│                                                          │
│  L = Leader, F = Follower                               │
│  ISR (In-Sync Replicas) tracks healthy followers       │
└─────────────────────────────────────────────────────────┘
```

**Key Concepts:**
1. **Partitioning Strategy**
   - Hash-based (by key)
   - Round-robin
   - Custom partitioner

2. **Replication**
   - Leader/Follower model
   - ISR (In-Sync Replica)
   - min.insync.replicas
   - acks configuration

3. **Ordering Guarantees**
   - Per-partition ordering
   - Global ordering (single partition)

#### Hands-On (2-3 hours):

```java
// Create Spring Boot project with Kafka
// pom.xml
<dependency>
    <groupId>org. springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>

// application.yml
spring:
  kafka: 
    bootstrap-servers: localhost:9092
    producer:
      key-serializer:  org.apache.kafka.common. serialization.StringSerializer
      value-serializer: org.springframework. kafka.support.serializer.JsonSerializer
    consumer:
      key-deserializer: org.apache.kafka.common.serialization. StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer. JsonDeserializer
      group-id: my-consumer-group
      auto-offset-reset: earliest

// Simple Producer
@Service
public class OrderProducer {
    @Autowired
    private KafkaTemplate<String, Order> kafkaTemplate;
    
    public void sendOrder(Order order) {
        kafkaTemplate. send("orders", order. getId(), order);
    }
}

// Simple Consumer
@Service
public class OrderConsumer {
    @KafkaListener(topics = "orders", groupId = "order-processing-group")
    public void consume(Order order) {
        System.out.println("Received:  " + order);
    }
}
```

**Practice Tasks:**
- ✅ Create topics with different partition counts
- ✅ Send messages with and without keys
- ✅ Observe partition distribution
- ✅ Test replication factor

---

### **Day 3: Consumer Groups & Offset Management** ⏰ 5-6 hours

#### Theory (2 hours):

```
┌─────────────────────────────────────────────────────────────┐
│              Consumer Group Rebalancing                     │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Topic: "events" (4 partitions)                             │
│  ┌────┐ ┌────┐ ┌────┐ ┌────┐                               │
│  │ P0 │ │ P1 │ │ P2 │ │ P3 │                               │
│  └────┘ └────┘ └────┘ └────┘                               │
│                                                              │
│  Consumer Group "processors" (2 consumers)                  │
│  ┌──────────────┐         ┌──────────────┐                 │
│  │ Consumer 1   │         │ Consumer 2   │                 │
│  │  reads P0,P1 │         │  reads P2,P3 │                 │
│  └──────────────┘         └──────────────┘                 │
│                                                              │
│  Add Consumer 3 → Rebalance!                                 │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐                    │
│  │ C1: P0  │  │ C2: P1  │  │ C3: P2  │  (P3 idle)         │
│  └─────────┘  └─────────┘  └─────────┘                    │
└─────────────────────────────────────────────────────────────┘
```

**Offset Management Strategies:**
```
1. Auto-commit (enable. auto.commit=true)
   ✓ Simple
   ✗ Risk of data loss or duplication

2. Manual commit (after processing)
   ✓ More control
   ✓ At-least-once delivery

3. Transactional (exactly-once semantics)
   ✓ Strongest guarantee
   ✗ More complex
```

#### Hands-On (3-4 hours):

```java
// Manual Offset Management
@Service
public class ManualCommitConsumer {
    
    @KafkaListener(topics = "orders", groupId = "manual-commit-group")
    public void consume(
            ConsumerRecord<String, Order> record,
            Acknowledgment acknowledgment) {
        
        try {
            // Process message
            processOrder(record.value());
            
            // Manual commit after successful processing
            acknowledgment.acknowledge();
            
        } catch (Exception e) {
            // Don't commit - message will be reprocessed
            log.error("Failed to process order", e);
        }
    }
}

// Batch Processing
@KafkaListener(topics = "events", groupId = "batch-processor")
public void consumeBatch(List<Order> orders) {
    orders.forEach(order -> process(order));
}

// Seeking to Specific Offset
@Service
public class OffsetSeekingConsumer {
    
    @KafkaListener(topics = "logs", groupId = "log-processor")
    public void consume(ConsumerRecord<String, String> record) {
        // Process
    }
    
    // Seek to beginning
    @EventListener
    public void seekToBeginning(ConsumerStartedEvent event) {
        event.getConsumer().seekToBeginning(
            event.getConsumer().assignment()
        );
    }
}
```

**Practice:**
- ✅ Create multiple consumers in same group
- ✅ Test rebalancing (add/remove consumers)
- ✅ Implement manual commit
- ✅ Test offset reset scenarios

**Day 3 Interview Questions to Master:**
1. What happens when consumers > partitions? 
2. Explain rebalancing strategies (Eager vs Cooperative)
3. How to prevent message duplication? 

---

## **📅 DAYS 4-6: Producers & Consumers Deep Dive**

### **Day 4: Producer Internals & Configuration** ⏰ 6 hours

#### Theory (2 hours):

```
┌─────────────────────────────────────────────────────────────┐
│                Producer Architecture                         │
├────────────────────────────────────────────────────────────��┤
│                                                              │
│  Application                                                 │
│       │                                                      │
│       ▼                                                      │
│  ┌──────────────────┐                                       │
│  │ Serializer       │  (Key & Value)                        │
│  └────────┬─────────┘                                       │
│           ▼                                                  │
│  ┌──────────────────┐                                       │
│  │ Partitioner      │  (Decides which partition)            │
│  └────────┬─────────┘                                       │
│           ▼                                                  │
│  ┌──────────────────┐                                       │
│  │ Record Accumulator│  (Batching)                          │
│  │  ┌────┐ ┌────┐   │                                       │
│  │  │Bat1│ │Bat2│   │  buffer.memory                        │
│  │  └────┘ └────┘   │  linger.ms                            │
│  └────────┬─────────┘  batch.size                           │
│           ▼                                                  │
│  ┌──────────────────┐                                       │
│  │ Sender (IO Thread)│                                       │
│  └────────┬─────────┘                                       │
│           ▼                                                  │
│     Kafka Broker                                             │
└─────────────────────────────────────────────────────────────┘
```

**Critical Configurations:**

| Config | Purpose | Values | Impact |
|--------|---------|--------|--------|
| `acks` | Durability | 0, 1, all | Performance vs reliability |
| `retries` | Fault tolerance | 0 to MAX_INT | Prevents data loss |
| `batch.size` | Throughput | bytes | Latency vs throughput |
| `linger.ms` | Batching delay | milliseconds | Latency vs efficiency |
| `compression.type` | Reduce size | none, gzip, snappy, lz4, zstd | Network usage |
| `max.in.flight.requests` | Pipelining | 1-5 | Ordering vs performance |

#### Hands-On (4 hours):

```java
// Advanced Producer Configuration
@Configuration
public class KafkaProducerConfig {
    
    @Bean
    public ProducerFactory<String, Order> producerFactory() {
        Map<String, Object> config = new HashMap<>();
        
        config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        
        // Serialization
        config.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, 
                   StringSerializer.class);
        config.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, 
                   JsonSerializer. class);
        
        // Durability (for critical data)
        config.put(ProducerConfig.ACKS_CONFIG, "all");
        config.put(ProducerConfig.RETRIES_CONFIG, Integer.MAX_VALUE);
        config.put(ProducerConfig. MAX_IN_FLIGHT_REQUESTS_PER_CONNECTION, 5);
        config.put(ProducerConfig. ENABLE_IDEMPOTENCE_CONFIG, true); // Exactly-once
        
        // Performance tuning
        config.put(ProducerConfig.BATCH_SIZE_CONFIG, 32768); // 32KB
        config.put(ProducerConfig.LINGER_MS_CONFIG, 10); // Wait 10ms
        config.put(ProducerConfig.COMPRESSION_TYPE_CONFIG, "snappy");
        config.put(ProducerConfig. BUFFER_MEMORY_CONFIG, 67108864); // 64MB
        
        return new DefaultKafkaProducerFactory<>(config);
    }
    
    @Bean
    public KafkaTemplate<String, Order> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
}

// Producer with Callbacks
@Service
public class RobustOrderProducer {
    
    @Autowired
    private KafkaTemplate<String, Order> kafkaTemplate;
    
    public void sendOrderWithCallback(Order order) {
        ProducerRecord<String, Order> record = new ProducerRecord<>(
            "orders", 
            order.getId(), 
            order
        );
        
        // Add headers
        record.headers().add("source", "order-service". getBytes());
        record.headers().add("version", "v1".getBytes());
        
        kafkaTemplate.send(record).addCallback(
            result -> {
                RecordMetadata metadata = result.getRecordMetadata();
                log.info("Sent to partition {} with offset {}", 
                         metadata.partition(), metadata.offset());
            },
            ex -> {
                log.error("Failed to send message", ex);
                // Implement retry logic or dead letter queue
            }
        );
    }
    
    // Custom Partitioner
    public static class OrderPartitioner implements Partitioner {
        @Override
        public int partition(String topic, Object key, byte[] keyBytes,
                           Object value, byte[] valueBytes, Cluster cluster) {
            // VIP orders go to partition 0
            Order order = (Order) value;
            if (order.isVip()) {
                return 0;
            }
            // Others hash-based
            return Math.abs(key.hashCode()) % cluster.partitionCountForTopic(topic);
        }
    }
}
```

**Practice Tasks:**
- ✅ Test different `acks` settings and measure throughput
- ✅ Implement custom partitioner
- ✅ Test idempotent producer
- ✅ Measure compression impact

---

### **Day 5: Consumer Internals & Patterns** ⏰ 6 hours

#### Theory (2 hours):

```
┌─────────────────────────────────────────────────────────────┐
│                Consumer Poll Loop                            │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  while(true) {                                               │
│    ┌────────────────────────────────────┐                   │
│    │ 1. Join consumer group             │                   │
│    │    (if not already member)         │                   │
│    └────────────────────────────────────┘                   │
│              ▼                                               │
│    ┌────────────────────────────────────┐                   │
│    │ 2. Fetch records from broker       │                   │
│    │    (max. poll.records)              │                   │
│    └────────────────────────────────────┘                   │
│              ▼                                               │
│    ┌────────────────────────────────────┐                   │
│    │ 3. Deserialize                     │                   │
│    └────────────────────────────────────┘                   │
│              ▼                                               │
│    ┌────────────────────────────────────┐                   │
│    │ 4. Process records                 │                   │
│    │    (must complete within           │                   │
│    │     max.poll.interval.ms)          │                   │
│    └────────────────────────────────────┘                   │
│              ▼                                               │
│    ┌────────────────────────────────────┐                   │
│    │ 5. Commit offsets                  │                   │
│    └────────────────────────────────────┘                   │
│  }                                                           │
└─────────────────────────────────────────────────────────────┘
```

**Consumer Patterns:**

1. **At-Most-Once** (may lose messages)
```java
// Commit before processing
consumer.commitSync();
processRecords(records);
```

2. **At-Least-Once** (may duplicate)
```java
// Process then commit
processRecords(records);
consumer.commitSync();
```

3. **Exactly-Once** (requires transactions)
```java
// Read-Process-Write pattern with transactions
```

#### Hands-On (4 hours):

```java
// Advanced Consumer Configuration
@Configuration
public class KafkaConsumerConfig {
    
    @Bean
    public ConsumerFactory<String, Order> consumerFactory() {
        Map<String, Object> config = new HashMap<>();
        
        config.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        config.put(ConsumerConfig.GROUP_ID_CONFIG, "order-processor");
        
        // Deserialization
        config.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, 
                   StringDeserializer.class);
        config.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, 
                   JsonDeserializer.class);
        config.put(JsonDeserializer.TRUSTED_PACKAGES, "*");
        
        // Offset management
        config.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);
        config.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
        
        // Performance tuning
        config.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, 500);
        config.put(ConsumerConfig.MAX_POLL_INTERVAL_MS_CONFIG, 300000); // 5 min
        config.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, 10000); // 10 sec
        config.put(ConsumerConfig. HEARTBEAT_INTERVAL_MS_CONFIG, 3000); // 3 sec
        
        // Fetch settings
        config.put(ConsumerConfig. FETCH_MIN_BYTES_CONFIG, 1024); // 1KB
        config. put(ConsumerConfig.FETCH_MAX_WAIT_MS_CONFIG, 500); // 0.5 sec
        
        return new DefaultKafkaConsumerFactory<>(config);
    }
}

// Error Handling Consumer
@Service
public class ResilientConsumer {
    
    @Autowired
    private KafkaTemplate<String, Order> kafkaTemplate;
    
    @KafkaListener(topics = "orders", groupId = "resilient-processor")
    public void consume(ConsumerRecord<String, Order> record,
                       Acknowledgment acknowledgment) {
        try {
            processOrder(record. value());
            acknowledgment.acknowledge();
            
        } catch (RetryableException e) {
            // Send to retry topic
            kafkaTemplate.send("orders-retry", record.key(), record.value());
            acknowledgment.acknowledge(); // Don't block main topic
            
        } catch (NonRetryableException e) {
            // Send to dead letter queue
            kafkaTemplate.send("orders-dlq", record.key(), record.value());
            acknowledgment. acknowledge();
            
        } catch (Exception e) {
            log.error("Unexpected error", e);
            // Don't acknowledge - will be reprocessed
        }
    }
    
    // Retry topic consumer (with delay)
    @KafkaListener(topics = "orders-retry", groupId = "retry-processor")
    public void consumeRetry(Order order) throws InterruptedException {
        Thread.sleep(5000); // Wait 5 seconds before retry
        processOrder(order);
    }
}

// Concurrent Consumer
@Configuration
@EnableKafka
public class ConcurrentConsumerConfig {
    
    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, Order>
            kafkaListenerContainerFactory() {
        
        ConcurrentKafkaListenerContainerFactory<String, Order> factory =
            new ConcurrentKafkaListenerContainerFactory<>();
        
        factory.setConsumerFactory(consumerFactory());
        factory.setConcurrency(3); // 3 consumer threads
        factory.getContainerProperties()
               .setAckMode(ContainerProperties.AckMode.MANUAL);
        
        return factory;
    }
}
```

**Practice:**
- ✅ Implement retry mechanism
- ✅ Create dead letter queue pattern
- ✅ Test rebalancing with concurrent consumers
- ✅ Measure processing throughput

---

### **Day 6: Serialization & Schema Management** ⏰ 5 hours

#### Theory (2 hours):

**Serialization Options:**
```
┌──────────────────────────────────────────────────────────┐
│              Serialization Formats                       │
├──────────────────────────────────────────────────────────┤
│ 1. String/JSON                                           │
│    ✓ Human-readable                                      │
│    ✗ No schema validation, larger size                  │
│                                                          │
│ 2. Avro (with Schema Registry)                          │
│    ✓ Compact, schema evolution support                  │
│    ✓ Backward/forward compatibility                     │
│    ✗ Requires Schema Registry                           │
│                                                          │
│ 3. Protobuf                                             │
│    ✓ Efficient, language-agnostic                       │
│    ✓ Schema evolution                                   │
│                                                          │
│ 4. Custom (e.g., Kryo, Thrift)                          │
└──────────────────────────────────────────────────────────┘
```

**Schema Registry Flow:**
```
Producer                Schema Registry           Broker
   │                           │                     │
   ├─1. Register schema────────►                    │
   │   (returns schema ID)     │                     │
   │                           │                     │
   ├─2. Serialize with ID──────┼──────────────────────►
   │   [SchemaID][Data]        │     [Topic]         │
   │                           │                     │
   
Consumer                                          Broker
   │                           │                     │
   │◄─3. Fetch message─────────┼─────────────────────┤
   │   [SchemaID][Data]        │                     │
   │                           │                     │
   ├─4. Get schema by ID───────►                    │
   │◄─ Returns schema──────────┤                     │
   │                           │                     │
   └─5. Deserialize using schema                    │
```

#### Hands-On (3 hours):

```java
// 1. Add dependencies
<dependency>
    <groupId>io.confluent</groupId>
    <artifactId>kafka-avro-serializer</artifactId>
    <version>7.5.0</version>
</dependency>

// 2. Define Avro schema (order.avsc)
{
  "type": "record",
  "name": "Order",
  "namespace": "com.example. model",
  "fields": [
    {"name": "id", "type": "string"},
    {"name": "customerId", "type": "string"},
    {"name": "amount", "type": "double"},
    {"name": "timestamp", "type": "long"},
    {"name": "status", "type": {
      "type": "enum",
      "name": "OrderStatus",
      "symbols": ["PENDING", "CONFIRMED", "SHIPPED", "DELIVERED"]
    }}
  ]
}

// 3. Producer with Avro
@Configuration
public class AvroProducerConfig {
    
    @Bean
    public ProducerFactory<String, Order> avroProducerFactory() {
        Map<String, Object> config = new HashMap<>();
        config.put(ProducerConfig. BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        config.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, 
                   StringSerializer. class);
        config.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, 
                   KafkaAvroSerializer.class);
        config.put("schema.registry.url", "http://localhost:8081");
        return new DefaultKafkaProducerFactory<>(config);
    }
}

// 4. Custom Serializer Example
public class OrderSerializer implements Serializer<Order> {
    
    private final ObjectMapper objectMapper = new ObjectMapper();
    
    @Override
    public byte[] serialize(String topic, Order order) {
        try {
            return objectMapper.writeValueAsBytes(order);
        } catch (Exception e) {
            throw new SerializationException("Error serializing order", e);
        }
    }
}
```

**Practice:**
- ✅ Setup Schema Registry (Docker)
- ✅ Create and evolve schemas
- ✅ Test backward/forward compatibility
- ✅ Compare serialization sizes (JSON vs Avro vs Protobuf)

---

## **📅 DAYS 7-9: Advanced Concepts**

### **Day 7: Kafka Streams** ⏰ 6 hours

```java
// Stream Processing Example
@Configuration
@EnableKafkaStreams
public class KafkaStreamsConfig {
    
    @Bean
    public KStream<String, Order> processOrders(StreamsBuilder builder) {
        
        // 1. Read from topic
        KStream<String, Order> orders = builder.stream("orders");
        
        // 2. Filter
        KStream<String, Order> highValueOrders = orders
            .filter((key, order) -> order.getAmount() > 1000.0);
        
        // 3. Transform
        KStream<String, OrderProcessed> processed = highValueOrders
            . mapValues(order -> new OrderProcessed(
                order.getId(),
                order.getAmount() * 1.1, // Add tax
                "PROCESSED"
            ));
        
        // 4. Group and aggregate
        KTable<String, Double> salesByCustomer = orders
            .groupBy((key, order) -> order.getCustomerId())
            .aggregate(
                () -> 0.0,
                (customerId, order, total) -> total + order.getAmount(),
                Materialized.with(Serdes.String(), Serdes.Double())
            );
        
        // 5. Join streams
        KStream<String, Order> payments = builder.stream("payments");
        KStream<String, EnrichedOrder> enriched = orders
            .join(
                payments,
                (order, payment) -> new EnrichedOrder(order, payment),
                JoinWindows.of(Duration. ofMinutes(5))
            );
        
        // 6. Write to output topic
        processed.to("orders-processed");
        
        return orders;
    }
}
```

**Practice:**
- ✅ Build a real-time aggregation pipeline
- ✅ Implement windowing operations
- ✅ Test stateful transformations

---

### **Day 8: Transactions & Exactly-Once Semantics** ⏰ 5 hours

```java
// Transactional Producer
@Configuration
public class TransactionalProducerConfig {
    
    @Bean
    public ProducerFactory<String, Order> transactionalProducerFactory() {
        Map<String, Object> config = new HashMap<>();
        config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        config.put(ProducerConfig. TRANSACTIONAL_ID_CONFIG, "order-processor-tx");
        config.put(ProducerConfig. ENABLE_IDEMPOTENCE_CONFIG, true);
        config.put(ProducerConfig. ACKS_CONFIG, "all");
        
        return new DefaultKafkaProducerFactory<>(config);
    }
}

// Read-Process-Write with Transactions
@Service
public class TransactionalProcessor {
    
    @Autowired
    private KafkaTemplate<String, Order> kafkaTemplate;
    
    @KafkaListener(topics = "input-orders", groupId = "tx-processor")
    @Transactional("kafkaTransactionManager")
    public void processTransactionally(Order order) {
        // 1. Process
        OrderProcessed processed = processOrder(order);
        
        // 2. Write to multiple topics (atomic)
        kafkaTemplate.send("processed-orders", processed);
        kafkaTemplate.send("audit-log", new AuditLog(order.getId()));
        
        // 3. If exception occurs, entire transaction rolls back
        // No messages sent, offset not committed
    }
}
```

**Learn:**
- ✅ Transaction guarantees
- ✅ Consumer isolation levels
- ✅ Performance implications

---

### **Day 9: Kafka Connect & Integration Patterns** ⏰ 5 hours

```json
// Source Connector (Database → Kafka)
{
  "name": "mysql-source-connector",
  "config": {
    "connector.class": "io.debezium.connector. mysql.MySqlConnector",
    "database.hostname": "localhost",
    "database.port": "3306",
    "database.user":  "root",
    "database. password": "password",
    "database.server.id": "1",
    "database.server. name": "mydb",
    "table.include.list": "orders",
    "database.history.kafka.bootstrap.servers": "localhost:9092",
    "database. history.kafka.topic": "schema-changes"
  }
}

// Sink Connector (Kafka → Elasticsearch)
{
  "name": "elasticsearch-sink-connector",
  "config": {
    "connector.class": "io.confluent.connect.elasticsearch.ElasticsearchSinkConnector",
    "topics": "orders",
    "connection.url": "http://localhost:9200",
    "type.name": "_doc",
    "key.ignore": "false"
  }
}
```

**Practice:**
- ✅ Setup Kafka Connect
- ✅ Create custom connector
- ✅ Test Change Data Capture (CDC)

---

## **📅 DAYS 10-12: Production-Ready Skills**

### **Day 10: Monitoring & Observability** ⏰ 6 hours

**Key Metrics:**
```
Producer Metrics:
├── record-send-rate
├── record-error-rate
├── request-latency-avg
├── buffer-available-bytes
└── batch-size-avg

Consumer Metrics:
├── records-consumed-rate
├── records-lag (CRITICAL!)
├── fetch-latency-avg
├── commit-latency-avg
└── rebalance-rate

Broker Metrics:
├── UnderReplicatedPartitions (CRITICAL!)
├── OfflinePartitionsCount (CRITICAL!)
├── ActiveControllerCount
├── RequestHandlerAvgIdlePercent
└── NetworkProcessorAvgIdlePercent
```

**Setup Monitoring:**
```yaml
# docker-compose.yml (add to existing)
  prometheus:
    image: prom/prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"

  grafana:
    image:  grafana/grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin

  kafka-exporter:
    image: danielqsj/kafka-exporter
    command: --kafka.server=kafka:9092
    ports:
      - "9308:9308"
```

```java
// Application metrics with Micrometer
@Service
public class MonitoredProducer {
    
    private final Counter messagesSent;
    private final Timer sendLatency;
    
    public MonitoredProducer(MeterRegistry registry) {
        this.messagesSent = Counter.builder("kafka.messages.sent")
            .tag("topic", "orders")
            .register(registry);
            
        this.sendLatency = Timer.builder("kafka.send. latency")
            .register(registry);
    }
    
    public void send(Order order) {
        sendLatency.record(() -> {
            kafkaTemplate.send("orders", order);
            messagesSent. increment();
        });
    }
}
```

**Practice:**
- ✅ Setup Prometheus + Grafana
- ✅ Create dashboards for key metrics
- ✅ Set up alerts for lag, under-replicated partitions

---

### **Day 11: Performance Tuning & Scalability** ⏰ 6 hours

**Tuning Checklist:**

```
┌─────────────────────────────────────────────────────────┐
│          Performance Optimization Matrix                │
├─────────────────────────────────────────────────────────┤
│  Priority:  Throughput                                   │
│  ├── Increase batch. size (32KB → 128KB)                │
│  ├── Increase linger.ms (0 → 10-100ms)                 │
│  ├── Enable compression (snappy/lz4)                   │
│  ├── Increase num.network.threads (broker)             │
│  └── Use async send                                     │
│                                                          │
│  Priority: Latency                                      │
│  ├── Decrease batch.size                                │
│  ├── linger.ms = 0                                      │
│  ├── Disable compression                                │
│  ├── acks = 1 (instead of all)                         │
│  └── Increase partitions                                │
│                                                          │
│  Priority:  Reliability                                  │
│  ├── acks = all                                         │
│  ├── replication.factor = 3                            │
│  ├── min.insync.replicas = 2                           │
│  ├── Enable idempotence                                 │
│  └── unclean.leader.election.enable = false            │
└─────────────────────────────────────────────────────────┘
```

**Scaling Strategies:**
```
Horizontal Scaling:
├── Add more brokers (rebalance partitions)
├── Add more partitions (increases parallelism)
├── Add more consumer instances
└── Use multiple consumer groups

Vertical Scaling: 
├── Increase heap size (broker JVM)
├── More CPU cores
├── Faster disks (SSD)
└── More RAM for page cache
```

**Practice:**
- ✅ Benchmark different configurations
- ✅ Stress test with load generation tool
- ✅ Measure impact of partition count

---

### **Day 12: Security & Multi-Tenancy** ⏰ 5 hours

**Security Layers:**
```java
// SSL/TLS Configuration
@Configuration
public class SecureKafkaConfig {
    
    @Bean
    public ProducerFactory<String, Order> secureProducerFactory() {
        Map<String, Object> config = new HashMap<>();
        
        // SSL
        config.put("security.protocol", "SSL");
        config.put("ssl.truststore.location", "/path/to/truststore.jks");
        config.put("ssl.truststore.password", "password");
        config.put("ssl.keystore.location", "/path/to/keystore.jks");
        config.put("ssl.keystore.password", "password");
        config.put("ssl.key.password", "password");
        
        // SASL
        config.put("sasl.mechanism", "SCRAM-SHA-512");
        config.put("sasl.jaas.config", 
            "org.apache.kafka.common.security.scram.ScramLoginModule required " +
            "username=\"user\" password=\"password\";");
        
        return new DefaultKafkaProducerFactory<>(config);
    }
}

// ACLs (Access Control Lists)
// kafka-acls --bootstrap-server localhost:9092 \
//   --add --allow-principal User:Alice \
//   --operation Read --operation Write \
//   --topic orders
```

**Practice:**
- ✅ Setup SSL encryption
- ✅ Configure SASL authentication
- ✅ Implement ACLs

---

## **📅 DAYS 13-14: Real-World Scenarios & System Design**

### **Day 13: System Design Problems** ⏰ 6-8 hours

**Practice these scenarios:**

#### **Scenario 1: E-commerce Order Processing**
```
Requirements:
- Process 10,000 orders/second
- Ensure no order is lost
- Maintain order sequence per customer
- Generate real-time analytics

Design: 
┌──────────┐     ┌───────────────┐     ┌─────────────┐
│ Order    │────►│ Kafka         │────►│ Order       │
│ Service  │     │ Topic: orders  │     │ Processor   │
└──────────┘     │ 20 partitions │     └─────────────┘
                 │ RF=3          │            │
                 └───────────────┘            ▼
                        │              ┌──────────────┐
                        │              │ PostgreSQL   │
                        │              └──────────────┘
                        ▼
                 ┌───────────────┐
                 │ Analytics     │
                 │ (Kafka        │
                 │  Streams)     │
                 └───────────────┘
```

#### **Scenario 2: Log Aggregation**
```
Requirements:
- Collect logs from 1000 services
- Process 100GB/day
- Retain for 30 days
- Enable real-time search

Your turn:  Design the architecture! 
```

#### **Scenario 3: Microservices Event Bus**
```
Requirements:
- 50 microservices
- Event-driven communication
- Schema evolution support
- Distributed tracing

Your turn: Design the architecture!
```

---

### **Day 14: Architecture Patterns** ⏰ 6-8 hours

**Master These Patterns:**

1. **Event Sourcing**
```java
// All state changes as events
@Service
public class EventSourcingExample {
    
    public void processOrder(Order order) {
        // Don't update database directly
        // Emit events instead
        
        emit(new OrderCreatedEvent(order));
        emit(new PaymentProcessedEvent(order. getId()));
        emit(new OrderShippedEvent(order.getId()));
        
        // Rebuild state from events
    }
}
```

2. **CQRS (Command Query Responsibility Segregation)**
```
Write Side:                  Read Side:
Command → Kafka → DB        Kafka → Materialized View
                                  → Elasticsearch
                                  → Redis Cache
```

3. **Saga Pattern (Distributed Transactions)**
```java
// Choreography-based Saga
Order Service → [OrderCreated] → Kafka
                                   ↓
Payment Service → [PaymentProcessed] or [PaymentFailed]
                                   ↓
Inventory Service → [InventoryReserved] or [Compensate]
```

4. **Outbox Pattern**
```java
@Transactional
public void createOrder(Order order) {
    // Single database transaction
    orderRepository.save(order);
    
    // Write to outbox table (not directly to Kafka)
    outboxRepository.save(new OutboxEvent(
        "OrderCreated",
        order.toJson()
    ));
    
    // CDC connector reads outbox → Kafka
}
```

---

## **📅 DAY 15: Interview Prep & Mock Questions** ⏰ 8 hours

### **Common Interview Questions** (Prepare answers for all):

#### **Fundamentals:**
1. Explain Kafka architecture
2. What is a partition?  Why is partitioning important?
3. How does replication work?
4. Explain consumer groups
5. What happens during rebalancing?

#### **Design Questions:**
6. Design a real-time analytics system
7. How would you handle millions of events/second?
8. Design a notification system using Kafka
9. How to ensure exactly-once processing? 
10. Design for multi-region deployment

#### **Troubleshooting:**
11. Consumer lag is increasing - how to debug?
12. Messages are being duplicated - why?
13. A broker goes down - what happens?
14. How to handle poison messages?
15. Performance degradation - where to look?

#### **Scenarios:**
16. When to use Kafka vs RabbitMQ vs SQS?
17. How to migrate from monolith to event-driven?
18. Handling schema evolution in production
19. Data retention and compaction strategies
20. Security considerations in multi-tenant setup

---

### **Mock Interview Practice:**

**Coding Challenge:**
```
Build a mini-project (4-6 hours):

"Real-time Order Processing System"
├── Producer: REST API to create orders
├── Consumer 1: Process orders, update database
├── Consumer 2: Send notifications
├── Kafka Streams: Real-time analytics
├── Monitoring: Metrics dashboard
└── Error handling: DLQ, retries

Requirements: 
✓ Spring Boot
✓ Manual offset management
✓ Error handling
✓ Metrics
✓ Docker Compose setup
```

---

## **📚 Resources Compilation:**

### **Must-Read:**
- ✅ "Kafka:  The Definitive Guide" (Chapters 1-8)
- ✅ Confluent Blog (kafka. apache.org)
- ✅ Spring Kafka Documentation

### **Video Courses:**
- ✅ "Apache Kafka Series" by Stephane Maarek (Udemy)
- ✅ Confluent Developer YouTube Channel

### **Hands-On:**
- ✅ GitHub:  kafka-tutorials
- ✅ Confluent Kafka Tutorials

### **Practice Platforms:**
- ✅ Build 3-5 mini-projects
- ✅ Contribute to Kafka open source

---

## **✅ Daily Progress Tracker:**

```
Day  │ Topic                              │ Hours │ Status
─────┼────────────────────────────────────┼───────┼────────
 1   │ Fundamentals & Setup               │ 4-5   │ ☐
 2   │ Topics, Partitions, Replication    │ 5-6   │ ☐
 3   │ Consumer Groups & Offsets          │ 5-6   │ ☐
 4   │ Producer Deep Dive                 │ 6     │ ☐
 5   │ Consumer Deep Dive                 │ 6     │ ☐
 6   │ Serialization & Schema             │ 5     │ ☐
 7   │ Kafka Streams                      │ 6     │ ☐
 8   │ Transactions & Exactly-Once        │ 5     │ ☐
 9   │ Kafka Connect                      │ 5     │ ☐
10   │ Monitoring & Observability         │ 6     │ ☐
11   │ Performance Tuning                 │ 6     │ ☐
12   │ Security                           │ 5     │ ☐
13   │ System Design                      │ 6-8   │ ☐
14   │ Architecture Patterns              │ 6-8   │ ☐
15   │ Mock Interviews                    │ 8     │ ☐
```

---

## **🎯 Success Criteria:**

By Day 15, you should be able to:
- ✅ Explain Kafka architecture confidently
- ✅ Design event-driven systems
- ✅ Implement production-ready Kafka applications
- ✅ Troubleshoot common issues
- ✅ Answer 90% of tech lead interview questions
- ✅ Make architectural trade-off decisions

---

**🚀 Ready to start?  Let me know if you want me to:**
1. Deep dive into any specific day
2. Provide more code examples
3. Give you mock interview questions right now
4. Create a GitHub repo template for practice projects

**Good luck with your Kafka journey!   You got this!  💪**
