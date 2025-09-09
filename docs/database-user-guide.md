# Database Management Guide

## Overview

This guide provides instructions for managing the database in the RSS to Telegram Bot (RSStT). The system supports both SQLite (for development/testing) and PostgreSQL (for production) databases with automatic migration handling.

## Database Configuration

### Environment Variables

The database is configured through environment variables. The primary variable is:

```
DATABASE_URL=postgres://username:password@host:port/database_name
```

For SQLite, if no DATABASE_URL is provided, the system defaults to:
```
sqlite:/path/to/config/db.sqlite3
```

### Railway.app Configuration

The system automatically handles Railway.app database configuration by checking these environment variables in order:
1. `DATABASE_URL`
2. `DATABASE_PRIVATE_URL`
3. `DATABASE_PUBLIC_URL`

### Example Configurations

**SQLite (Development)**:
```
# Uses default SQLite database
# No DATABASE_URL needed
```

**PostgreSQL (Production)**:
```
DATABASE_URL=postgres://rsstt:password@localhost:5432/rsstt
```

## Database Initialization

The database is automatically initialized when the application starts:

1. The system detects the database type from DATABASE_URL
2. Appropriate migrations are selected based on the database type
3. Pending migrations are automatically applied
4. The database is ready for use

### Manual Initialization

For advanced scenarios, you can manually initialize the database:

```bash
# If using Aerich directly (development only)
# First, ensure your environment is set up
export DATABASE_URL=your_database_url

# Initialize migrations
aerich init -t src.db.config.TORTOISE_ORM

# Run migrations
aerich upgrade
```

## Migration Management

### Understanding Migrations

Migrations are automatic in RSStT. When you start the application:

1. The system checks for pending migrations
2. Applies any unapplied migrations
3. Updates the database schema accordingly

### Migration Files

Migrations are stored in:
- `src/db/migrations_sqlite/` for SQLite
- `src/db/migrations_pgsql/` for PostgreSQL

Each migration file contains:
- Upgrade instructions (SQL commands to apply changes)
- Downgrade instructions (SQL commands to revert changes)

### Example Migration Process

When a new feature requires a database schema change:

1. Developer modifies models in `src/db/models.py`
2. New migration file is generated using Aerich
3. Migration is tested on development database
4. Migration is committed to version control
5. On deployment, migration is automatically applied

## Database Operations

### Backup and Restore

#### SQLite Backup

```bash
# Simple file copy (while application is stopped)
cp config/db.sqlite3 config/db.sqlite3.backup

# Or using SQLite commands
sqlite3 config/db.sqlite3 ".backup config/db.sqlite3.backup"
```

#### PostgreSQL Backup

```bash
# Using pg_dump
pg_dump -h hostname -U username -d database_name > backup.sql

# Using Docker (if running in Docker)
docker exec -t container_name pg_dump -U postgres database_name > backup.sql
```

#### Restore Process

**SQLite Restore**:
```bash
# Stop the application
# Replace the database file
cp config/db.sqlite3.backup config/db.sqlite3
# Start the application
```

**PostgreSQL Restore**:
```bash
# Create new database
createdb -h hostname -U username new_database_name

# Restore from backup
psql -h hostname -U username -d new_database_name < backup.sql
```

### Database Monitoring

#### Checking Database Status

```bash
# For SQLite
ls -la config/db.sqlite3

# For PostgreSQL (using Docker)
docker exec -it container_name pg_isready

# For PostgreSQL (direct access)
pg_isready -h hostname -p port -U username
```

#### Database Size Monitoring

**SQLite**:
```bash
# Check database file size
du -h config/db.sqlite3
```

**PostgreSQL**:
```sql
-- Check database size
SELECT pg_size_pretty(pg_database_size('database_name'));

-- Check table sizes
SELECT 
    table_name,
    pg_size_pretty(pg_total_relation_size(table_name)) as size
FROM information_schema.tables 
WHERE table_schema = 'public';
```

## Troubleshooting

### Common Issues

#### Migration Failures

If migrations fail to apply:

1. Check the error logs for specific error messages
2. Verify database connectivity
3. Ensure the database user has proper permissions
4. Check if the database is in a consistent state

```bash
# Check current migration status
# This would require direct Aerich access in development
```

#### Database Connection Issues

Common connection problems:

1. **Wrong DATABASE_URL format**:
   - Ensure correct scheme (postgres:// or sqlite:)
   - Verify host, port, username, and password

2. **Network connectivity**:
   - Check if the database server is reachable
   - Verify firewall settings

3. **Authentication failures**:
   - Confirm username and password
   - Check if the user exists and has proper permissions

#### Data Corruption

If you suspect data corruption:

1. **Immediate steps**:
   - Stop the application
   - Create a backup of the current database
   - Check database integrity

**SQLite integrity check**:
```bash
sqlite3 config/db.sqlite3 "PRAGMA integrity_check;"
```

**PostgreSQL integrity check**:
```sql
-- Check for corrupted tables
SELECT schemaname, tablename 
FROM pg_tables 
WHERE has_table_privilege(tablename, 'SELECT');

-- Check database consistency
SELECT * FROM pg_stat_database;
```

### Recovery Procedures

#### Rolling Back Migrations

In case of a problematic migration:

1. **Identify the problematic migration**
2. **Stop the application**
3. **Manually revert the migration** (if possible)
4. **Restore from backup** (if necessary)

#### Complete Database Reset

For development environments, you might want to reset the database:

**SQLite**:
```bash
# Remove the database file
rm config/db.sqlite3
# Restart the application - it will create a new database
```

**PostgreSQL**:
```bash
# Drop and recreate database
dropdb database_name
createdb database_name
# Restart the application
```

## Performance Optimization

### Index Management

The system automatically creates indexes for:
- Primary keys
- Foreign keys
- Unique constraints
- Fields marked for indexing

To add custom indexes, modify the models:

```python
class Feed(Model):
    link = fields.CharField(max_length=4096, unique=True)
    # This creates an index automatically due to unique=True
    
    class Meta:
        table = 'feed'
        # Add custom indexes if needed
        indexes = (
            ('state', 'link'),  # Composite index
        )
```

### Query Optimization

For optimal database performance:

1. **Use appropriate field types**:
   - Use BigIntField for large integers (user IDs)
   - Use SmallIntField for small integers (states, flags)
   - Use CharField with appropriate max_length

2. **Limit result sets**:
   ```python
   # Good - limit results
   feeds = await Feed.filter(state=1).limit(100)
   
   # Avoid - fetching all records
   feeds = await Feed.all()
   ```

3. **Use select_related for joins**:
   ```python
   # Efficient - single query with join
   subs = await Sub.filter(user_id=user_id).select_related('feed')
   
   # Less efficient - multiple queries
   subs = await Sub.filter(user_id=user_id)
   for sub in subs:
       feed = await sub.feed
   ```

## Security Considerations

### Database Access Control

1. **Principle of Least Privilege**:
   - Database users should have minimal required permissions
   - Application user should not be database superuser

2. **Connection Security**:
   - Use TLS connections for remote databases
   - Set VERIFY_TLS=0 only in trusted environments

3. **Credential Management**:
   - Store credentials in environment variables
   - Never commit credentials to version control
   - Use secret management tools in production

### SQL Injection Prevention

The system uses Tortoise ORM which automatically prevents SQL injection:

```python
# Safe - ORM handles parameterization
user = await User.get(id=user_id)

# Safe - ORM handles parameterization
feeds = await Feed.filter(state=1, link__contains=search_term)

# Avoid - raw SQL without parameterization (if used)
# await db.execute_query('SELECT * FROM user WHERE id = ' + user_id)  # DANGEROUS
```

## Development Workflow

### Local Development

1. **Use SQLite for development**:
   - Faster setup
   - No external dependencies
   - Easy to reset

2. **Migration Development**:
   - Modify models in `src/db/models.py`
   - Generate migrations using Aerich
   - Test migrations locally
   - Commit migrations with code changes

### Testing Migrations

Before deploying migration changes:

1. **Test on development database**:
   ```bash
   # Apply migrations
   aerich upgrade
   
   # Verify schema
   # Check that application functions correctly
   ```

2. **Test rollback capability**:
   ```bash
   # Test downgrade
   aerich downgrade
   
   # Test upgrade again
   aerich upgrade
   ```

### Production Deployment

1. **Backup before deployment**:
   - Create database backup
   - Verify backup integrity

2. **Migration Application**:
   - Deploy new code
   - Application automatically applies migrations on startup
   - Monitor logs for migration success

3. **Post-Deployment Verification**:
   - Verify application functionality
   - Check database performance
   - Monitor for errors

## Advanced Topics

### Custom Database Queries

For complex queries not easily expressed through ORM:

```python
from tortoise import Tortoise

# Raw SQL queries (use with caution)
async def complex_query():
    connection = Tortoise.get_connection("default")
    result = await connection.execute_query_dict("""
        SELECT u.id, COUNT(s.id) as sub_count 
        FROM "user" u 
        LEFT JOIN sub s ON u.id = s.user_id 
        GROUP BY u.id
    """)
    return result
```

### Database-Specific Features

#### PostgreSQL-Specific Features

1. **JSONB Operations**:
   ```python
   # PostgreSQL supports advanced JSON operations
   feeds = await Feed.filter(entry_hashes__contains=some_hash)
   ```

2. **Full-Text Search**:
   ```sql
   -- PostgreSQL full-text search capabilities
   SELECT * FROM feed WHERE to_tsvector('english', title) @@ to_tsquery('english', 'search_term');
   ```

#### SQLite-Specific Considerations

1. **Limited ALTER TABLE**:
   - SQLite has limited support for ALTER TABLE operations
   - Complex schema changes may require table recreation

2. **Performance Considerations**:
   - SQLite is suitable for small to medium deployments
   - Consider PostgreSQL for high-volume applications

## Maintenance Procedures

### Regular Maintenance Tasks

1. **Database Vacuuming** (SQLite):
   ```bash
   sqlite3 config/db.sqlite3 "VACUUM;"
   ```

2. **PostgreSQL Maintenance**:
   ```sql
   -- Regular PostgreSQL maintenance
   VACUUM ANALYZE;
   REINDEX DATABASE database_name;
   ```

3. **Backup Verification**:
   - Regularly test backup restoration
   - Verify backup integrity
   - Update backup procedures as needed

### Monitoring and Alerts

Set up monitoring for:

1. **Database Connectivity**:
   - Connection failures
   - Slow query performance

2. **Disk Space**:
   - Database growth monitoring
   - Alert on low disk space

3. **Migration Failures**:
   - Monitor application startup logs
   - Alert on migration errors

## Conclusion

The RSStT database management system provides a robust, automated approach to database schema management. By following the practices outlined in this guide, you can ensure reliable database operations, smooth migrations, and optimal performance for your RSS to Telegram Bot deployment.