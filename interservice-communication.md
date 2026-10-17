# Interservice Communication Rules

## API Gateway Configuration

### Spring Cloud Gateway
- Use Spring Cloud Gateway for unified entry point
- Implement request routing based on path patterns
- Configure load balancing for backend services
- Implement rate limiting and throttling
- Add request/response logging and monitoring

### Security at Gateway Level
- Validate JWT tokens at gateway level
- Implement CORS configuration
- Add request validation and sanitization
- Configure SSL termination
- Implement API versioning strategy

### Gateway Patterns (Phase 4 uses static URIs; Phase 7 switches to `lb://` via Eureka)
```yaml
# Example route configuration
spring:
  cloud:
    gateway:
      routes:
        - id: auth-service
          uri: http://localhost:8081   # Phase 7: lb://auth-service
          predicates:
            - Path=/api/v1/auth/**
        - id: wallet-service
          uri: http://localhost:8082   # Phase 7: lb://wallet-service
          predicates:
            - Path=/api/v1/wallet/**
```

## Service Discovery

### Netflix Eureka Implementation
- Register all microservices with Eureka server
- Use service names for inter-service communication
- Implement health checks for service registration
- Configure failover and load balancing
- Monitor service registry health

### Service Registration
```java
@SpringBootApplication
@EnableEurekaClient
public class WalletServiceApplication {
    // Service automatically registers with Eureka
}
```

### Load Balancing
- Use client-side load balancing with Ribbon
- Implement proper load balancing algorithms
- Configure retry policies for failed requests
- Monitor service instances health
- Implement graceful service shutdown

## Circuit Breakers

### Resilience4j Implementation
- Implement circuit breakers for external service calls
- Configure failure thresholds and timeout values
- Implement fallback mechanisms
- Monitor circuit breaker metrics
- Use bulkhead pattern for resource isolation

### Circuit Breaker Configuration
```java
@Service
public class PaymentService {
    
    @CircuitBreaker(name = "payment-processor", fallbackMethod = "fallbackPayment")
    @Retry(name = "payment-processor")
    @TimeLimiter(name = "payment-processor")
    public CompletableFuture<PaymentResponse> processPayment(PaymentRequest request) {
        // Payment processing logic
    }
    
    public CompletableFuture<PaymentResponse> fallbackPayment(PaymentRequest request, Exception ex) {
        // Fallback logic
    }
}
```

## Event-Driven Architecture

### Apache Kafka/RabbitMQ
- Use event-driven communication for async operations
- Implement saga pattern for distributed transactions
- Use dead letter queues for failed message processing
- Implement message deduplication
- Monitor message processing metrics

### Event Types
- **Domain Events**: WalletBalanceUpdated, TransactionCompleted
- **Integration Events**: PaymentProcessorCallback, NotificationSent  
- **Command Events**: ProcessPayment, SendNotification
- **Query Events**: BalanceInquiry, TransactionHistory

### Event Schema
```json
{
  "eventId": "uuid",
  "eventType": "WalletBalanceUpdated",
  "aggregateId": "wallet-id",
  "version": 1,
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "userId": "user-id",
    "previousBalance": 100.00,
    "newBalance": 150.00,
    "transactionId": "transaction-id"
  }
}
```

## Orchestration vs Choreography

### Saga Pattern Implementation
- Use orchestration for complex business processes
- Implement choreography for simple event chains
- Handle compensation transactions for failures
- Implement saga state management
- Monitor saga execution progress

### Orchestrator Pattern (for Complex Flows)
```java
@Component
public class PaymentSagaOrchestrator {
    
    public void processPayment(PaymentRequest request) {
        try {
            // Step 1: Validate payment
            // Step 2: Debit wallet
            // Step 3: Process with PSP
            // Step 4: Update ledger
            // Step 5: Send notifications
        } catch (Exception e) {
            // Implement compensation logic
        }
    }
}
```

### Choreography Pattern (for Simple Flows)
- Services react to events independently
- No central coordinator
- Better for loose coupling
- Use for notification workflows

## Monitoring & Observability

### Distributed Tracing
- Use Spring Cloud Sleuth with Zipkin
- Implement correlation IDs across services
- Trace critical business operations
- Monitor service dependencies
- Analyze performance bottlenecks

### Metrics Collection
- Use Micrometer for metrics collection
- Monitor service-level indicators (SLIs)
- Implement service-level objectives (SLOs)
- Track business metrics (transaction volumes, success rates)
- Set up alerting for critical thresholds

### Logging Strategy
- Use structured logging (JSON format)
- Include correlation IDs in all logs
- Centralize logs with ELK stack
- Implement proper log levels
- Monitor log volume and errors

## API Contracts & Documentation

### OpenAPI 3.0 Specifications
- Define API contracts first
- Generate client SDKs from specifications
- Implement contract testing
- Version APIs properly
- Document all error responses

### Service Contracts
```yaml
openapi: 3.0.0
info:
  title: Wallet Service API
  version: 1.0.0
paths:
  /api/v1/wallet/{userId}/balance:
    get:
      summary: Get wallet balance
      parameters:
        - name: userId
          in: path
          required: true
          schema:
            type: string
            format: uuid
      responses:
        200:
          description: Wallet balance
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/WalletBalance'
```

## Error Handling & Resilience

### Timeout Configuration
- Set appropriate timeouts for service calls
- Implement exponential backoff for retries
- Use circuit breakers for failing services
- Implement graceful degradation
- Monitor timeout metrics

### Error Propagation
- Use consistent error response format across services
- Implement proper error mapping at gateway level
- Log errors with sufficient context
- Avoid exposing internal errors to clients
- Implement error correlation across services