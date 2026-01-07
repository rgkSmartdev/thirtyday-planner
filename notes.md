Spring Boot/ MicroService development

1. Build REST API - microservices
   Structure - Controller/Handler which takes the HTTP request.
   Middleware to validate the request payloads
   Middleware to throw errors
   Controller (req validation , constructing succesful & error responses) -> Service (business logic)-> Data Layer( which connects with database ORM layer)
    Cache                                                                         cache                   cache
   Configuration (secrets, env variables )
   Security ( Authentication & Authorization )
   Architecture -> Dependency Injection, MVC. Model represents a db table.
   Services like Logging, Cache, third party integrations etc.

   Spring Boot - Spring Boot starters, Auto Configuration, Production Ready features. Actuators - Actuator module provides all of spring boots production ready features.

   SpringBoot Annotations
   @RestController -> @Controller + @ResponseBody
   @SpringBootApplication -> @Configuration + @EnableAutoConfiguration + @ComponentScan
Best Practices
   Use DTOs: Don't expose entities directly in REST APIs
   Lazy Loading: Use FetchType.LAZY for relationships to avoid N+1 queries
   Transactions: Always use @Transactional for write operations
   Indexing: Add database indexes on frequently queried columns
   Validation: Use Bean Validation (@NotNull, @Size, etc.)
   Pagination: Use Pageable for large datasets
   Projections: Use Spring Data projections for read-only queries
