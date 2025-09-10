# RSS to Telegram Bot - Project Context

## Project Overview

RSS to Telegram Bot (RSStT) is a Python-based Telegram bot that fetches RSS feeds and sends them to Telegram chats. The bot focuses on providing an excellent reading experience by preserving rich-text formatting, handling media properly, and offering customizable formatting options.

### Key Features
- Multi-user support
- Internationalization (i18n) with support for multiple languages
- Rich text formatting preservation
- Media handling (images, videos, audio, documents)
- Emoji shortcode replacement
- Automatic title detection and omission
- Author name display
- Message splitting for long content
- Telegraph post support
- Customizable formatting settings (hashtags, custom titles)
- Individual proxy settings for Telegram and RSS feeds
- OPML importing and exporting
- Optimized performance
- HTTP caching

### Technologies Used
- Python 3.9+
- Telethon (Telegram client library)
- aiohttp (HTTP client)
- feedparser (RSS parsing)
- Tortoise ORM (database abstraction)
- PostgreSQL/SQLite (database)
- Docker (containerization)

## Project Structure

```
.
├── src/                     # Main source code
│   ├── __main__.py         # Entry point
│   ├── entrypoint.py       # Main application logic
│   ├── env.py              # Environment configuration
│   ├── version.py          # Version information
│   ├── log/                # Logging utilities
│   ├── db/                 # Database models and migrations
│   ├── command/            # Telegram command handlers
│   ├── parsing/            # RSS parsing utilities
│   ├── helpers/            # Helper utilities
│   └── monitor/            # RSS monitoring logic
├── docs/                   # Documentation
├── scripts/                # Utility scripts
├── config/                 # Configuration files (created at runtime)
├── telegramRSSbot.py       # Main entry point script
├── requirements.txt        # Python dependencies
├── Dockerfile              # Docker build configuration
├── docker-compose.yml.sample  # Sample Docker Compose configuration
├── .env.sample             # Sample environment configuration
├── app.json                # Heroku deployment configuration
└── Procfile                # Heroku process configuration
```

## Building and Running

### Docker Deployment (Recommended)
1. Create a directory for the bot:
   ```bash
   mkdir rsstt
   cd rsstt
   ```
2. Download the sample docker-compose file:
   ```bash
   wget https://raw.githubusercontent.com/Rongronggg9/RSS-to-Telegram-Bot/dev/docker-compose.yml.sample -O docker-compose.yml
   ```
3. Edit the docker-compose.yml file to fill in environment variables:
   ```bash
   vi docker-compose.yml
   ```
4. Start the bot:
   ```bash
   docker-compose up -d
   ```

### Local Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/Rongronggg9/RSS-to-Telegram-Bot.git
   cd RSS-to-Telegram-Bot
   ```
2. Install dependencies:
   ```bash
   python3 -m pip install -r requirements.txt
   ```
3. Copy the sample environment file:
   ```bash
   cp .env.sample .env
   ```
4. Edit the .env file to configure the bot:
   ```bash
   vi .env
   ```
5. Run the bot:
   ```bash
   python3 -u telegramRSSbot.py
   ```

### PyPI Installation
1. Install the package:
   ```bash
   python3 -m pip install -U rsstt
   ```
2. Create the configuration directory:
   ```bash
   mkdir -p ~/.rsstt
   ```
3. Download the sample environment file:
   ```bash
   wget https://raw.githubusercontent.com/Rongronggg9/RSS-to-Telegram-Bot/dev/.env.sample -O ~/.rsstt/.env
   ```
4. Edit the .env file to configure the bot:
   ```bash
   vi ~/.rsstt/.env
   ```
5. Run the bot:
   ```bash
   python3 -m rsstt
   ```

## Configuration

The bot is configured using environment variables. The main required variables are:

- `TOKEN`: Telegram bot token from [@BotFather](https://t.me/BotFather) (you need to create a new bot with the `/newbot` command and obtain its token)
- `MANAGER`: Telegram user ID from [@userinfobot](https://t.me/userinfobot) (you need to start a chat with this bot to get your user ID; can be a list of IDs separated by semicolons)

Optional but recommended:
- `TELEGRAPH_TOKEN`: Telegraph API access tokens for sending long posts

Advanced settings include:
- `DATABASE_URL`: Database connection string (default: SQLite)
- `API_ID`/`API_HASH`: Telegram API credentials
- `T_PROXY`/`R_PROXY`: Proxies for Telegram and RSS connections
- Various performance and behavior tuning options

## Development Conventions

### Code Style
- Follow Python PEP 8 style guide
- Use type hints where possible
- Maintain consistent naming conventions
- Write docstrings for public functions and classes

### Testing
- Ensure all functionality is covered by tests
- Run tests before submitting changes
- Use pytest for test execution

### Documentation
- Keep README.md and docs/ up to date
- Document new features and changes
- Provide examples for usage

## Deployment Platforms

The bot supports multiple deployment platforms:
1. Docker/Docker Compose (recommended)
2. Railway.app
3. Heroku (deprecated free tier)
4. Direct installation from PyPI
5. Running directly from source

For production deployments, Docker is recommended for its consistency and ease of management.

## Qwen Added Memories
- Project uses uv instead of pip for faster dependency management. Dockerfile has been updated to install and use uv. Project configuration files (pyproject.toml, setup.cfg) have been updated with correct YemenHR project information and metadata.
- Telegraph generation uses hardcoded author name "Generated by YemenHR RSS Bot" and URL "https://github.com/EngDawood/yemenhr-rss-bot" instead of environment variables.
