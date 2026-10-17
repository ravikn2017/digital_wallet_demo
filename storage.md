# Storage Development Rules

## PostgreSQL Database Design

### Schema Design
- Use UUID for primary keys for better distribution
- Implement proper foreign key constraints
- Use appropriate data types (DECIMAL for money, TIMESTAMP WITH TIME ZONE)
- Follow PostgreSQL naming conventions (snake_case)
- Create indexes on frequently queried columns

### Financial Data Handling
- Use DECIMAL type for all monetary values
- Implement double-entry bookkeeping principles
- Ensure ACID compliance for all financial transactions
- Use database-level constraints for data integrity
- Implement audit trails for all financial operations

### Table Structure Standards
```sql
-- Standard fields for all tables
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
version INTEGER DEFAULT 1 -- for optimistic locking
```

### Migration Management
- Use Flyway for database migrations
- Version migrations sequentially (V001__, V002__)
- Never modify existing migrations
- Include rollback scripts for complex changes
- Test migrations on representative data sets

### Indexing Strategy
- Create indexes on foreign keys
- Use composite indexes for multi-column queries
- Implement partial indexes for filtered queries
- Monitor index usage and remove unused indexes
- Use EXPLAIN ANALYZE to optimize query performance

### Data Integrity
- Implement CHECK constraints for business rules
- Use NOT NULL where appropriate
- Implement unique constraints for business keys
- Use triggers for complex validation when necessary
- Implement soft deletes for audit requirements

### Performance Optimization
- Use connection pooling (HikariCP recommended)
- Implement read replicas for read-heavy operations
- Use materialized views for complex reporting
- Partition large tables by date/time where appropriate
- Monitor slow queries and optimize regularly

### Backup and Recovery
- Implement automated daily backups
- Test backup restoration procedures regularly
- Use point-in-time recovery capabilities
- Implement backup retention policies
- Monitor backup success and failures

### Security
- Use least privilege principle for database users
- Encrypt sensitive data at rest
- Use SSL/TLS for database connections
- Implement row-level security where needed
- Audit database access and changes

## Caching Strategy (Redis - Future Phase)

### Caching Patterns
- Use write-through pattern for wallet balances
- Implement cache-aside pattern for user sessions
- Use time-based expiration for temporary data
- Implement cache invalidation strategies
- Monitor cache hit ratios

### Data Types
- Use STRING for simple key-value pairs
- Use HASH for structured data (user profiles)
- Use SET for unique collections
- Use SORTED SET for ranked data
- Use LIST for ordered data

### Performance & Monitoring
- Monitor memory usage and eviction policies
- Implement proper TTL strategies
- Use Redis Cluster for high availability
- Monitor cache performance metrics
- Implement proper error handling for cache failures