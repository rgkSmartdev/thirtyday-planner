# Integrating Kafka with Spring Boot (Confluent Cloud)

I'll walk you through integrating Kafka with Spring Boot using your Confluent Cloud cluster, including best practices. 

## 1. Add Dependencies

Add these to your `pom.xml` (Maven):

```xml name=pom.xml
<dependencies>
    <!-- Spring Kafka -->
    <dependency>
        <groupId>org.springframework.kafka</groupId>
        <artifactId>spring-kafka</artifactId>
    </dependency>
    
    <!-- Optional: For JSON serialization -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
    </dependency>
</dependencies>
```

Or `build.gradle` (Gradle):

```gradle name=build.gradle
dependencies {
    implementation 'org. springframework.kafka:spring-kafka'
    implementation 'com.fasterxml.jackson.core:jackson-databind'
}
```

## 2. Configuration

### Get Confluent Cloud Credentials

From your Confluent Cloud console: 
1. Navigate to your cluster
2. Go to "Data Integration" → "API Keys"
3. Create an API Key and Secret
4. Note your Bootstrap Server URL

### Application Configuration

````yaml name=application.yml
spring:
  kafka: 
    bootstrap-servers: ${KAFKA_BOOTSTRAP_SERVERS:pkc-xxxxx.us-east-1.aws.confluent.cloud:9092}
    
    # Producer Configuration
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer
      acks: all  # Wait for all replicas to acknowledge
      retries: 3
      properties:
        enable.idempotence: true  # Prevents duplicate messages
        max.in.flight.requests.per.connection: 5
        compression.type: snappy
        linger.ms: 10  # Batching for better throughput
        
    # Consumer Configuration
    consumer: 
      key-deserializer: org.apache.kafka.common.serialization. StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer. JsonDeserializer
      group-id: ${CONSUMER_GROUP_ID:my-consumer-group}
      auto-offset-reset: earliest  # or 'latest'
      enable-auto-commit: false  # Manual commit for better control
      properties:
        spring.json.trusted.packages: "com.yourcompany.models"  # Your model package
        isolation.level: read_committed
        
    # Confluent Cloud Security Settings
    properties:
      security.protocol: SASL_SSL
      sasl.mechanism: PLAIN
      sasl. jaas.config: org.apache.kafka.common.security.plain.PlainLoginModule required username='${KAFKA_API_KEY}' password='${KAFKA_API_SECRET}';
      session.timeout.ms: 45000

# Topic Configuration
kafka:
  topics:
    user-events: user-events-topic
    order-events: order-events-topic
````

### Environment Variables

Store sensitive data in environment variables or use Spring Cloud Config:

```properties name=. env
KAFKA_BOOTSTRAP_SERVERS=pkc-xxxxx.us-east-1.aws.confluent.cloud:9092
KAFKA_API_KEY=your-api-key
KAFKA_API_SECRET=your-api-secret
CONSUMER_GROUP_ID=my-spring-boot-app
```

## 3. Producer Implementation

### Configuration Class

```java name=KafkaProducerConfig. java
package com.yourcompany.config;

import org.apache.kafka. clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework. kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;
import org.springframework.kafka. support.serializer.JsonSerializer;

import java.util.HashMap;
import java.util.Map;

@Configuration
public class KafkaProducerConfig {

    @Value("${spring.kafka.bootstrap-servers}")
    private String bootstrapServers;

    @Value("${spring. kafka.properties.sasl.jaas.config}")
    private String saslJaasConfig;

    @Bean
    public ProducerFactory<String, Object> producerFactory() {
        Map<String, Object> configProps = new HashMap<>();
        configProps.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        configProps.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        configProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
        
        // Confluent Cloud Security
        configProps.put("security.protocol", "SASL_SSL");
        configProps. put("sasl.mechanism", "PLAIN");
        configProps. put("sasl.jaas. config", saslJaasConfig);
        
        // Best practices
        configProps.put(ProducerConfig. ACKS_CONFIG, "all");
        configProps.put(ProducerConfig.RETRIES_CONFIG, 3);
        configProps.put(ProducerConfig. ENABLE_IDEMPOTENCE_CONFIG, true);
        configProps. put(ProducerConfig. COMPRESSION_TYPE_CONFIG, "snappy");
        configProps.put(ProducerConfig.LINGER_MS_CONFIG, 10);
        configProps.put(ProducerConfig.BATCH_SIZE_CONFIG, 16384);
        
        return new DefaultKafkaProducerFactory<>(configProps);
    }

    @Bean
    public KafkaTemplate<String, Object> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
}
```

### Producer Service

```java name=KafkaProducerService.java
package com.yourcompany.service;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.support.SendResult;
import org.springframework.stereotype.Service;

import java.util.concurrent. CompletableFuture;

@Service
@RequiredArgsConstructor
@Slf4j
public class KafkaProducerService {

    private final KafkaTemplate<String, Object> kafkaTemplate;

    public void sendMessage(String topic, String key, Object message) {
        CompletableFuture<SendResult<String, Object>> future = 
            kafkaTemplate.send(topic, key, message);

        future.whenComplete((result, ex) -> {
            if (ex == null) {
                log. info("Sent message=[{}] with key=[{}] to topic=[{}] with offset=[{}]",
                    message, key, topic, result.getRecordMetadata().offset());
            } else {
                log.error("Unable to send message=[{}] to topic=[{}] due to:  {}",
                    message, topic, ex.getMessage());
            }
        });
    }

    // Synchronous send (use sparingly)
    public SendResult<String, Object> sendMessageSync(String topic, String key, Object message) 
            throws Exception {
        return kafkaTemplate. send(topic, key, message).get();
    }
}
```

### Event Model

```java name=UserEvent.java
package com.yourcompany.models;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.time. Instant;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class UserEvent {
    private String userId;
    private String eventType;
    private String eventData;
    private Instant timestamp;
}
```

## 4. Consumer Implementation

### Configuration Class

```java name=KafkaConsumerConfig.java
package com.yourcompany.config;

import org. apache.kafka.clients.consumer. ConsumerConfig;
import org. apache.kafka.common.serialization.StringDeserializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.annotation.EnableKafka;
import org.springframework.kafka.config. ConcurrentKafkaListenerContainerFactory;
import org. springframework.kafka.core.ConsumerFactory;
import org.springframework.kafka.core.DefaultKafkaConsumerFactory;
import org.springframework.kafka.listener.ContainerProperties;
import org.springframework.kafka.support.serializer.JsonDeserializer;

import java.util. HashMap;
import java.util. Map;

@EnableKafka
@Configuration
public class KafkaConsumerConfig {

    @Value("${spring. kafka.bootstrap-servers}")
    private String bootstrapServers;

    @Value("${spring.kafka.consumer.group-id}")
    private String groupId;

    @Value("${spring.kafka.properties.sasl.jaas.config}")
    private String saslJaasConfig;

    @Bean
    public ConsumerFactory<String, Object> consumerFactory() {
        Map<String, Object> props = new HashMap<>();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        props.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer. class);
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, JsonDeserializer.class);
        
        // Confluent Cloud Security
        props.put("security.protocol", "SASL_SSL");
        props.put("sasl.mechanism", "PLAIN");
        props.put("sasl. jaas.config", saslJaasConfig);
        
        // Best practices
        props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
        props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);
        props.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, 100);
        props.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, 45000);
        props.put(ConsumerConfig.HEARTBEAT_INTERVAL_MS_CONFIG, 3000);
        
        // JSON Deserializer settings
        props.put(JsonDeserializer. TRUSTED_PACKAGES, "com. yourcompany.models");
        props.put(JsonDeserializer.USE_TYPE_INFO_HEADERS, false);
        props.put(JsonDeserializer.VALUE_DEFAULT_TYPE, "com.yourcompany.models. UserEvent");
        
        return new DefaultKafkaConsumerFactory<>(props);
    }

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, Object> kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, Object> factory = 
            new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        
        // Manual commit
        factory.getContainerProperties().setAckMode(ContainerProperties.AckMode.MANUAL_IMMEDIATE);
        
        // Concurrency (number of threads)
        factory.setConcurrency(3);
        
        // Error handling
        factory.setCommonErrorHandler(new DefaultErrorHandler());
        
        return factory;
    }
}
```

### Consumer Service

```java name=KafkaConsumerService. java
package com.yourcompany.service;

import com.yourcompany.models.UserEvent;
import lombok.extern.slf4j.Slf4j;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework. kafka.support. Acknowledgment;
import org.springframework.kafka.support.KafkaHeaders;
import org.springframework.messaging. handler.annotation.Header;
import org.springframework.messaging.handler.annotation.Payload;
import org.springframework.stereotype. Service;

@Service
@Slf4j
public class KafkaConsumerService {

    @KafkaListener(
        topics = "${kafka.topics.user-events}",
        groupId = "${spring.kafka.consumer.group-id}",
        containerFactory = "kafkaListenerContainerFactory"
    )
    public void consumeUserEvents(
            @Payload UserEvent event,
            @Header(KafkaHeaders.RECEIVED_KEY) String key,
            @Header(KafkaHeaders.RECEIVED_PARTITION) int partition,
            @Header(KafkaHeaders.OFFSET) long offset,
            Acknowledgment acknowledgment) {
        
        try {
            log.info("Received message:  key={}, partition={}, offset={}, event={}",
                key, partition, offset, event);
            
            // Process the message
            processUserEvent(event);
            
            // Manual commit after successful processing
            acknowledgment.acknowledge();
            
        } catch (Exception e) {
            log.error("Error processing message: key={}, offset={}, error={}",
                key, offset, e.getMessage(), e);
            // Don't acknowledge - message will be retried
        }
    }

    private void processUserEvent(UserEvent event) {
        // Your business logic here
        log.info("Processing user event: userId={}, eventType={}",
            event.getUserId(), event.getEventType());
    }
}
```

## 5. Error Handling

```java name=KafkaErrorHandler.java
package com.yourcompany.config;

import lombok.extern.slf4j.Slf4j;
import org.apache.kafka.clients.consumer.Consumer;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.springframework.kafka.listener.CommonErrorHandler;
import org.springframework.kafka.listener.MessageListenerContainer;
import org. springframework.stereotype.Component;
import org.springframework.util.backoff.FixedBackOff;

@Slf4j
@Component
public class KafkaErrorHandler implements CommonErrorHandler {

    @Override
    public void handleRecord(Exception thrownException, ConsumerRecord<?, ?> record,
                           Consumer<?, ?> consumer, MessageListenerContainer container) {
        log.error("Error processing record: topic={}, partition={}, offset={}, error={}",
            record. topic(), record.partition(), record.offset(), thrownException.getMessage());
        
        // Send to DLQ (Dead Letter Queue)
        sendToDeadLetterQueue(record, thrownException);
    }

    private void sendToDeadLetterQueue(ConsumerRecord<?, ?> record, Exception exception) {
        // Implement DLQ logic
        log.warn("Sending record to DLQ: topic={}, offset={}",
            record.topic(), record.offset());
    }
}
```

## 6. Best Practices

### 6.1 Health Check

```java name=KafkaHealthIndicator.java
package com.yourcompany.health;

import lombok.RequiredArgsConstructor;
import org.springframework.boot.actuate.health.Health;
import org.springframework.boot. actuate.health.HealthIndicator;
import org.springframework. kafka.core.KafkaTemplate;
import org.springframework.stereotype.Component;

@Component
@RequiredArgsConstructor
public class KafkaHealthIndicator implements HealthIndicator {

    private final KafkaTemplate<String, Object> kafkaTemplate;

    @Override
    public Health health() {
        try {
            // Check if we can connect to Kafka
            kafkaTemplate.execute((callback) -> {
                return callback.partitionsFor("test-topic");
            });
            return Health.up().withDetail("kafka", "Available").build();
        } catch (Exception e) {
            return Health.down()
                .withDetail("kafka", "Unavailable")
                .withDetail("error", e.getMessage())
                .build();
        }
    }
}
```

### 6.2 Metrics Configuration

```yaml name=application-monitoring.yml
management:
  endpoints:
    web:
      exposure:
        include: health,metrics,prometheus
  metrics:
    tags:
      application: ${spring.application.name}
    export:
      prometheus:
        enabled: true
```

### 6.3 Topic Management

```java name=KafkaTopicConfig.java
package com.yourcompany.config;

import org. apache.kafka.clients.admin. NewTopic;
import org. springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.config.TopicBuilder;

@Configuration
public class KafkaTopicConfig {

    @Value("${kafka.topics.user-events}")
    private String userEventsTopic;

    @Bean
    public NewTopic userEventsTopic() {
        return TopicBuilder.name(userEventsTopic)
            .partitions(3)  // Adjust based on throughput needs
            .replicas(3)    // Confluent Cloud default
            .config("retention.ms", "604800000")  // 7 days
            .config("compression.type", "snappy")
            .build();
    }
}
```

## 7. Testing

```java name=KafkaIntegrationTest.java
package com.yourcompany;

import com.yourcompany.models.UserEvent;
import com.yourcompany.service.KafkaProducerService;
import org. junit.jupiter.api.Test;
import org.springframework.beans. factory.annotation.Autowired;
import org.springframework.boot. test.context.SpringBootTest;
import org.springframework.kafka. test.context.EmbeddedKafka;
import org.springframework.test.annotation.DirtiesContext;

import java.time. Instant;

@SpringBootTest
@DirtiesContext
@EmbeddedKafka(partitions = 1, topics = {"test-topic"})
class KafkaIntegrationTest {

    @Autowired
    private KafkaProducerService producerService;

    @Test
    void testSendMessage() {
        UserEvent event = new UserEvent(
            "user123",
            "USER_CREATED",
            "Test data",
            Instant.now()
        );

        producerService.sendMessage("test-topic", "user123", event);
        
        // Add assertions
    }
}
```

## 8. Security Best Practices

1. **Never commit credentials** - Use environment variables or secret managers
2. **Use ACLs** - Configure topic-level access control in Confluent Cloud
3. **Enable SSL** - Always use SASL_SSL for production
4. **Rotate API keys** - Regularly rotate your Confluent Cloud API keys
5. **Audit logging** - Enable audit logs in Confluent Cloud

## 9. Performance Tuning

1. **Producer batching** - Use `linger.ms` and `batch.size`
2. **Compression** - Enable compression (snappy/lz4)
3. **Partitioning strategy** - Use meaningful keys for even distribution
4. **Consumer concurrency** - Adjust based on partition count
5. **Connection pooling** - Reuse connections (Spring handles this)

## 10. Monitoring

Monitor these metrics:
- **Producer**:  send rate, error rate, latency
- **Consumer**:  lag, throughput, error rate
- **Broker**: disk usage, network I/O (via Confluent Cloud UI)

Would you like me to help you implement any specific part or create a sample project structure? 
