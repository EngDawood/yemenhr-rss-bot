# Database Architecture and Migration System Documentation

## Overview

The RSS to Telegram Bot (RSStT) uses a database architecture based on Tortoise ORM with Aerich for migrations. The system supports both SQLite and PostgreSQL databases, with separate migration files for each database type. The database handles user management, feed subscriptions, and application options.

## Database Technologies

- **ORM**: [Tortoise ORM](https://tortoise-orm.com/) - An easy-to-use asyncio ORM inspired by Django
- **Migration Tool**: [Aerich](https://github.com/tortoise/aerich) - Tortoise ORM migration tool
- **Supported Databases**: SQLite and PostgreSQL
- **Connection Management**: Asynchronous database connections with connection pooling

## Database Models

The database consists of four main models defined in `src/db/models.py`:

### 1. User Model
Stores information about Telegram users and their default feed options.

**Fields:**
- `id` (BigIntField, PK): Telegram user ID (8 bytes)
- `state` (SmallIntField): User state (-1=banned, 0=guest, 1=user, 50=channel, 51=group, 100=admin)
- `lang` (CharField): Preferred language code (default: 'zh-Hans')
- `admin` (BigIntField): Admin of channel/group (nullable)
- `sub_limit` (SmallIntField): Subscription number limit (nullable)
- Default feed options (interval, notify, send_mode, etc.)

### 2. Feed Model
Stores RSS feed information and monitoring settings.

**Fields:**
- `id` (IntField, PK): Auto-incrementing ID
- `state` (SmallIntField): Feed state (0=deactivated, 1=activated)
- `link` (CharField): RSS feed URL (unique, up to 4096 characters)
- `title` (CharField): Feed title (up to 1024 characters)
- `interval` (SmallIntField): Monitoring interval (nullable)
- `entry_hashes` (JSONField): Hashes (CRC32) of entries (nullable)
- `etag` (CharField): Webpage ETag for cache validation (nullable)
- `last_modified` (DatetimeField): Last modified time of webpage (nullable)
- `error_count` (SmallIntField): Error count for feed monitoring
- `next_check_time` (DatetimeField): Next checking time (nullable)

### 3. Sub Model
Manages subscriptions (user-feed relationships) and their options.

**Fields:**
- `id` (IntField, PK): Auto-incrementing ID
- `state` (SmallIntField): Subscription state (0=deactivated, 1=activated)
- `user_id` (BigIntField, FK): Reference to User model
- `feed_id` (IntField, FK): Reference to Feed model
- `title` (CharField): Custom subscription title (nullable)
- `tags` (CharField): Subscription tags (nullable, up to 255 characters)
- Subscription options (interval, notify, send_mode, etc.)

### 4. Option Model
Stores application-wide options set by administrators.

**Fields:**
- `id` (IntField, PK): Auto-incrementing ID
- `key` (CharField): Option key (unique, up to 255 characters)
- `value` (TextField): Option value (nullable)

## Database Initialization

The database initialization process is handled in `src/db/__init__.py`:

1. **Database Type Detection**: The system automatically detects the database type (SQLite or PostgreSQL) from the `DATABASE_URL` environment variable
2. **Migration Directory Selection**: Based on the database type, the appropriate migration directory is selected
3. **Migration Upgrade**: Existing migrations are upgraded if needed
4. **Schema Application**: New migrations are applied to keep the database schema up-to-date
5. **Utility Initialization**: Effective options and task utilities are initialized

## Migration System

### Migration Structure

The migration system maintains separate directories for each database type:
- `src/db/migrations_sqlite/`: SQLite-specific migrations
- `src/db/migrations_pgsql/`: PostgreSQL-specific migrations

Each migration file follows the naming convention: `{sequence_number}_{timestamp}_{description}.py`

### Migration Files

Each migration file contains:
- `upgrade(db: BaseDBAsyncClient) -> str`: Function that returns SQL for upgrading the schema
- `downgrade(db: BaseDBAsyncClient) -> str`: Function that returns SQL for downgrading the schema

### Migration Process

1. **Initialization**: Aerich is initialized with the appropriate configuration
2. **Migration Upgrade**: The system checks for and applies any pending migrations
3. **Error Handling**: If migrations fail, the system logs the error and exits
4. **Version Management**: Migration versions are tracked in the `aerich` table

### Example Migration (Adding a New Column)

```python
# 4_20240425020849_display_entry_tags.py
async def upgrade(db: BaseDBAsyncClient) -> str:
    return """
        ALTER TABLE "sub" ADD "display_entry_tags" SMALLINT NOT NULL DEFAULT -100;
        ALTER TABLE "user" ADD "display_entry_tags" SMALLINT NOT NULL DEFAULT -1;
    """

async def downgrade(db: BaseDBAsyncClient) -> str:
    return """
        ALTER TABLE "sub" DROP COLUMN "display_entry_tags";
        ALTER TABLE "user" DROP COLUMN "display_entry_tags";
    """
```

## Database Configuration

### Environment Variables

The database is configured through environment variables in `src/env.py`:

- `DATABASE_URL`: Primary database connection URL
- The system also supports Railway.app specific variables:
  - `DATABASE_PRIVATE_URL`: For private networking
  - `DATABASE_PUBLIC_URL`: For public networking

### Connection URL Formats

- **SQLite**: `sqlite:/path/to/database.sqlite3`
- **PostgreSQL**: `postgres://username:password@host:port/database_name`

### Default Configuration

If no database URL is provided, the system defaults to SQLite with a database file at `config/db.sqlite3`.

## Database Utilities

### EffectiveOptions

A singleton class that implements a write-through cache for application options:
- Caches all options in memory to reduce database load
- Provides type casting and validation
- Supports callbacks for option changes
- Default options include intervals, subscription limits, etc.

### EffectiveTasks

A task dispatcher for managing feed monitoring tasks:
- Organizes feeds into buckets based on monitoring intervals
- Distributes feed checks across time to avoid overwhelming the system
- Supports dynamic task addition, deletion, and interval updates

## Database Operations

### Connection Management

- **Initialization**: Asynchronous database initialization in `src/db/__init__.py`
- **Closing**: Proper connection closing during application shutdown
- **Transactions**: Support for database transactions using `in_transaction()`

### Model Operations

The Tortoise ORM provides standard database operations:
- Create, Read, Update, Delete (CRUD) operations
- Filtering and querying with async/await syntax
- Relationship handling (Foreign Keys, Reverse Relations)

## Migration Management

### Creating New Migrations

New migrations are created using Aerich commands:
1. Modify the models in `src/db/models.py`
2. Generate migration files using Aerich
3. Review and customize migration files if needed

### Migration Best Practices

1. **Backward Compatibility**: Ensure migrations maintain data integrity
2. **Testing**: Test migrations on development databases before deployment
3. **Rollback Support**: Provide proper downgrade functions
4. **Performance**: Consider impact of migrations on large datasets

## Database Security

### Connection Security

- Support for TLS verification through `VERIFY_TLS` environment variable
- Proxy support for secure database connections
- Proper credential management through environment variables

### Data Security

- Proper field validation and type checking
- SQL injection protection through ORM usage
- Access control through user states and permissions

## Performance Considerations

### Connection Pooling

- Asynchronous connection handling
- Configurable concurrency settings
- Efficient resource utilization

### Query Optimization

- Indexing on frequently queried fields (user_id, feed_id, link, key)
- Proper relationship modeling
- Efficient migration strategies

## Error Handling

The system includes comprehensive error handling for:
- Database connection failures
- Migration application errors
- Schema validation issues
- Transaction rollback scenarios

## Backup and Recovery

While not explicitly implemented in the codebase, the system supports:
- SQLite file-based backups through file copying
- PostgreSQL backups through standard PostgreSQL tools
- Migration version tracking for recovery scenarios

## Development Workflow

1. **Local Development**: SQLite database for easy setup
2. **Testing**: Separate test databases for isolation
3. **Deployment**: PostgreSQL for production environments
4. **Migration Testing**: Apply migrations on test databases before production

## Supabase Integration

The project includes Docker Compose configuration for local PostgreSQL development using Supabase-compatible settings, making it easy to develop with a production-like database environment.