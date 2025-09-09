
# Database Migration System - Technical Documentation

## Overview

This document provides a detailed technical overview of the database migration system used in the RSS to Telegram Bot (RSStT). The system is built on Tortoise ORM with Aerich as the migration tool, supporting both SQLite and PostgreSQL databases with database-specific migration files.

## Architecture

### Core Components

1. **Tortoise ORM**: The underlying ORM that provides database abstraction
2. **Aerich**: Migration tool that manages schema changes and versioning
3. **Database Adapters**: Separate migration files for SQLite and PostgreSQL
4. **Migration Manager**: Custom logic in `src/db/__init__.py` for migration orchestration

### File Structure

```
src/db/
├── __init__.py          # Database initialization and migration orchestration
├── config.py           # Tortoise ORM configuration
├── models.py           # Database models
├── effective_utils.py  # Database utilities
├── migrations_sqlite/  # SQLite migrations
│   └── models/
│       ├── 0_20211117110249_init.py
│       ├── 1_20211130051128_update.py
│       ├── 2_20220306045951_update.py
│       ├── 3_20220415025716_update.py
│       └── 4_20240425020849_display_entry_tags.py
└── migrations_pgsql/   # PostgreSQL migrations
    └── models/
        ├── 0_20211117110249_init.py
        ├── 1_20211130051128_update.py
        ├── 2_20220306045951_update.py
        ├── 3_20220415030554_update.py
        └── 4_20240425020849_display_entry_tags.py
```

## Migration Initialization Process

The migration system is initialized in `src/db/__init__.py` through the `init()` function:

1. **Database Type Detection**: 
   ```python
   DB_TYPE = DBType(env.DATABASE_URL.partition(':')[0])
   ```
   The system detects whether to use SQLite or PostgreSQL based on the URL scheme.

2. **Migration Directory Selection**:
   ```python
   __MIGRATIONS_DIRS: Final[dict[DBType, Path]] = {
       DBType.SQLITE: __DB_PKG_DIR / 'migrations_sqlite',
       DBType.PGSQL: __DB_PKG_DIR / 'migrations_pgsql',
   }
   ```
   The appropriate migration directory is selected based on the database type.

3. **Aerich Command Setup**:
   ```python
   aerich_command = aerich.Command(
       tortoise_config=config.TORTOISE_ORM,
       location=str(__MIGRATIONS_DIRS[DB_TYPE]),
   )
   ```

4. **Migration Upgrade Process**:
   The system upgrades old SQL-based migrations to Python-based ones and applies pending migrations.

## Migration File Structure

Each migration file follows a consistent structure:

### Example Migration File (`4_20240425020849_display_entry_tags.py`)

```python
from tortoise import BaseDBAsyncClient

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

### Key Components

1. **Upgrade Function**: Contains SQL commands to apply the migration
2. **Downgrade Function**: Contains SQL commands to reverse the migration
3. **Database Client**: `BaseDBAsyncClient` provides database-specific SQL execution

## Database-Specific Differences

### SQLite Migrations

SQLite migrations use SQLite-specific syntax:
- `INTEGER PRIMARY KEY AUTOINCREMENT` for auto-incrementing keys
- Simpler data types
- Limited ALTER TABLE capabilities

### PostgreSQL Migrations

PostgreSQL migrations use PostgreSQL-specific features:
- `SERIAL` and `BIGSERIAL` for auto-incrementing keys
- `TIMESTAMPTZ` for timezone-aware timestamps
- `JSONB` for JSON data
- Comprehensive COMMENT support for documentation

## Migration Version Management

### Aerich Table Structure

The `aerich` table tracks migration versions:
- `id`: Primary key
- `version`: Migration file name
- `app`: Application name (typically "models")
- `content`: JSON content of the migration

### Version Upgrade Process

The system includes logic to upgrade old SQL-based migrations to Python-based ones:
```python
async def __upgrade_migrations_in_db():
    # Check for old .sql migration files
    outdated_revisions = await aerich.models.Aerich.filter(version__endswith='.sql')
    # Replace with corresponding .py files
    for revisions in outdated_revisions:
        old_migration_file = __MIGRATIONS_DIRS[DB_TYPE] / revisions.app / revisions.version
        new_migration_file = old_migration_file.with_suffix('.py')
        revisions.version = new_version
        await revisions.save(update_fields=['version'])
```

## Migration Application Process

### Initialization Sequence

1. **Tortoise Initialization**: Initialize the ORM with configuration
2. **Aerich Initialization**: Set up the migration command
3. **Migration Upgrade**: Upgrade old migration formats
4. **Pending Migrations**: Apply any unapplied migrations
5. **Error Handling**: Graceful error handling with detailed logging

### Error Handling

The system includes comprehensive error handling:
```python
try:
    if applied_migrations := await aerich_command.upgrade(run_in_transaction=True):
        logger.info(f'Applied migrations due to DB schema changes: {", ".join(applied_migrations)}')
except Exception as e:
    logger.critical('FAILED TO APPLY MIGRATIONS', exc_info=e)
    try:
        if migrations_to_apply := await aerich_command.heads():
            logger.critical(f'UNAPPPLIED MIGRATIONS: {", ".join(migrations_to_apply)}')
    except Exception as e:
        logger.error('Failed to fetch unapplied migrations', exc_info=e)
    exit(1)
```

## Migration Best Practices

### Schema Design

1. **Backward Compatibility**: Ensure new migrations don't break existing functionality
2. **Default Values**: Provide sensible defaults for new columns
3. **Data Types**: Use appropriate data types for each database
4. **Indexing**: Consider indexing for frequently queried fields

### Migration Development

1. **Incremental Changes**: Make small, focused changes in each migration
2. **Testing**: Test migrations on both SQLite and PostgreSQL
3. **Rollback Support**: Always implement proper downgrade functions
4. **Documentation**: Include comments explaining the purpose of each migration

## Migration Commands

While the system doesn't expose direct Aerich commands, the underlying process follows these patterns:

### Creating Migrations

When models change, new migrations are generated using:
```bash
aerich migrate --name migration_description
```

### Applying Migrations

Migrations are applied automatically during application startup:
```python
await aerich_command.upgrade(run_in_transaction=True)
```

## Transaction Management

The system uses database transactions for migration safety:
```python
async with in_transaction():
    # Migration operations
    pass
```

This ensures that if a migration fails, the database remains in a consistent state.

## Performance Considerations

### Migration Efficiency

1. **Batch Operations**: Group related changes in single migrations
2. **Index Management**: Create indexes after data population when possible
3. **Large Data Handling**: Consider partitioning for tables with large datasets

### Concurrent Access

The system handles concurrent database access through:
- Asynchronous connection pooling
- Transaction isolation
- Proper locking mechanisms in migrations

## Database Compatibility

### Cross-Database Support

The system maintains separate migration files for each database type to handle:
- Syntax differences
- Data type variations
- Feature availability

### Version Compatibility

The system checks for Aerich version compatibility:
```python
aerich_version = aerich.version.__version__
if int(aerich_version[0]) == 0 and int(aerich_version[2]) < 7:
    logger.critical(f'UNSUPPORTED AERICH VERSION: {aerich_version}, PLEASE UPGRADE TO >=0.7.0')
    exit(1)
```

## Monitoring and Logging

### Migration Logging

The system provides detailed logging for migration operations:
- Applied migrations
- Migration failures
- Version upgrades
- Performance metrics

### Error Recovery

The system includes mechanisms for error recovery:
- Graceful failure handling
- Detailed error reporting
- Migration state tracking

## Future Improvements

### Planned Enhancements

1. **Automated Migration Testing**: Integration tests for migration sequences
2. **Migration Validation**: Pre-deployment validation of migration logic
3. **Rollback Automation**: Enhanced rollback capabilities for failed migrations
4. **Performance Monitoring**: Metrics collection for migration performance

### Migration Tooling

Potential improvements to the migration tooling:
- Custom migration generators
- Automated migration conflict detection
- Visual migration dependency graphs
- Migration impact analysis

## Conclusion

The RSStT database migration system provides a robust, database-agnostic approach to schema management. By leveraging Tortoise ORM and Aerich, the system ensures consistent database state across different environments while maintaining compatibility with both SQLite and PostgreSQL databases. The careful separation of database-specific migrations and comprehensive error handling makes it suitable for production deployment with minimal risk of data corruption or inconsistency.