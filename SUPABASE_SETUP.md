# Supabase PostgreSQL Database Setup for RSS-to-Telegram Bot

This guide explains how to set up and run the RSS-to-Telegram Bot with a local Supabase PostgreSQL database using Docker Compose.

## Prerequisites

- Docker and Docker Compose installed on your system
- A Telegram bot token from [@BotFather](https://t.me/BotFather)
- Your Telegram user ID from [@userinfobot](https://t.me/userinfobot)

## Configuration

1. Update the `docker-compose.yml` file with your:
   - Telegram bot token (`TOKEN`)
   - Telegram user ID (`MANAGER`)

2. Optionally, update the Telegraph API tokens if you want to use Telegraph posts.

## Starting the Services

You can start the services using either of these methods:

### Method 1: Using the init script (recommended)
```bash
./init-with-supabase.sh
```

### Method 2: Manual start
```bash
# Start the database first
docker-compose up -d db

# Wait for the database to be ready
docker-compose exec db pg_isready

# Start the main bot service
docker-compose up -d main
```

## Database Access

The PostgreSQL database is accessible on port 5432 with the following credentials:
- Host: localhost
- Port: 5432
- Database: rsstt
- Username: postgres
- Password: postgres

## Stopping the Services

To stop the services:
```bash
docker-compose down
```

To stop the services and remove the database volume (WARNING: This will delete all data):
```bash
docker-compose down -v
```