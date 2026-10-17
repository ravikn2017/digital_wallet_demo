# Backend Development Rules

## Java/Spring Boot Microservices

### Project Structure
- Follow standard Maven/Gradle project structure
- Organize code by layers: controller, service, repository, entity
- Use separate packages for each microservice
- Implement hexagonal architecture pattern where applicable
- Keep configuration in `application.yml` or `application.properties`

### Spring Boot Best Practices
- Use Spring Boot 3.x with Java 17
- Implement proper dependency injection with @Autowired or constructor injection
- Use @RestController for REST endpoints
- Implement proper exception handling with @ControllerAdvice
- Use @Transactional for database operations

### Security Implementation
- Use Spring Security for authentication and authorization
- Implement JWT token validation in API Gateway
- Use BCrypt for password hashing
- Implement proper CORS configuration
- Use HTTPS for all communications
- Validate all input parameters

### Database Integration
- Use Spring Data JPA for database operations
- Implement proper entity relationships
- Use database migrations with Flyway
- Implement connection pooling with HikariCP
- Use transactions for data consistency

### API Design
- Follow RESTful API principles
- Use proper HTTP status codes
- Implement consistent error response format
- Use OpenAPI 3.0 for API documentation
- Version APIs properly (/api/v1/)

### Microservices Patterns
- Implement circuit breaker pattern with Resilience4j
- Use service discovery with Netflix Eureka
- Implement proper health checks with Spring Actuator
- Use distributed tracing for debugging
- Implement proper logging with structured logs

### Error Handling
- Create custom exception classes
- Implement global exception handler
- Log errors with proper context
- Return meaningful error messages to clients
- Use correlation IDs for request tracking

### Testing
- Write unit tests for service layer
- Use @SpringBootTest for integration tests
- Mock external dependencies in tests
- Use TestContainers for database testing
- Maintain minimum 85% code coverage

### Performance & Scalability
- Implement caching where appropriate
- Use async processing for non-blocking operations
- Implement proper connection pooling
- Use pagination for large data sets
- Monitor performance with metrics

### Configuration Management
- Use Spring Profiles for environment-specific configs
- Externalize configuration using ConfigMaps/environment variables
- Use Spring Cloud Config for centralized configuration
- Implement proper secrets management
- Use feature flags for gradual rollouts