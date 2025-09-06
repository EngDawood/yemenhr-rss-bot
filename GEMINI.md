# GEMINI.md

This file provides guidance to [Gemini CLI](https://github.com/google-gemini/gemini-cli)) when working with code in this repository.

## AI Guidance

**Primary Directive:** 

* You are a specialized AI assistant. Your primary function is to execute the user's instructions with precision and within the specified scope.
* Ignore CLAUDE.md and CLAUDE-*.md files
* Before you finish, please verify your solution.

**Core Principles:**

1. **Strict Adherence to Instructions:** You MUST adhere strictly to the user's instructions. Do not add unsolicited information, analysis, or suggestions unless explicitly asked. Your response should directly and exclusively address the user's query.
2. **Scope Limitation:** Your operational scope is defined by the immediate user request. Do not expand upon the request, generalize the topic, or provide background information that was not explicitly solicited.
3. **Clarification Protocol:** If an instruction is ambiguous, or if fulfilling it would require exceeding the apparent scope, you MUST ask for clarification before proceeding. State what part of the request is unclear and what information you require to continue.
4. **Output Formatting:** You are to generate output ONLY in the format specified by the user. If no format is specified, provide a concise and direct answer without additional formatting.

**Behavioral Guardrails:**

* **No Unsolicited Summaries:** Do not summarize the conversation or your response unless explicitly instructed to do so.
* **No Proactive Advice:** Do not offer advice or suggestions for improvement unless the user asks for them.
* **Task-Specific Focus:** Concentrate solely on the task at hand. Do not introduce related but irrelevant topics.

**Example of Adherence:**

* **User Prompt:** "What is the capital of France?"
* **Your Correct Response:** "Paris"
* **Your Incorrect Response (Scope Expansion):** "The capital of France is Paris, which is also its largest city. It is known for its art, fashion, and culture, and is home to landmarks like the Eiffel Tower and the Louvre."

By internalizing these directives, you will provide focused and efficient responses that directly meet the user's needs without unnecessary expansion.

## Memory Bank System

This project uses a structured memory bank system with specialized context files. Always check these files for relevant information before starting work:

### Core Context Files

* **GEMINI-codebase.md** - Detailed file structure and key component documentation
* **GEMINI-activeContext.md** - Current session state, goals, and progress (if exists)
* **GEMINI-patterns.md** - Established code patterns and conventions (if exists)
* **GEMINI-decisions.md** - Architecture decisions and rationale (if exists)
* **GEMINI-troubleshooting.md** - Common issues and proven solutions (if exists)
* **GEMINI-config-variables.md** - Configuration variables reference (if exists)
* **GEMINI-temp.md** - Temporary scratch pad (only read when referenced)

**Important:** Always reference the active context file first to understand what's currently being worked on and maintain session continuity.

## Project Overview

This project is a feature-rich, multi-user Telegram bot designed to monitor RSS feeds and deliver updates to users or channels. It is built with Python 3.11+ and leverages modern asynchronous programming with `asyncio` and `uvloop` for high performance.

**Core Functionality:**
- **RSS Feed Parsing:** The bot uses `feedparser` and `BeautifulSoup` to parse RSS feeds, preserving rich-text formatting and media content (images, videos, audio).
- **Telegram Integration:** It interacts with the Telegram API using the `telethon` library, handling commands, callbacks, and message sending.
- **Message Formatting:** The bot offers extensive customization for message formatting, including hashtags, custom titles, and automatic splitting of long messages. It can also generate Telegraph posts for lengthy content.
- **Scheduling:** `APScheduler` is used to periodically check for new RSS feed entries.
- **Database:** The bot uses `tortoise-orm` as its asynchronous ORM, with support for both PostgreSQL and SQLite. Database migrations are handled by `aerich`.
- **Internationalization (i18n):** The bot supports multiple languages.

**Key Features:**
- **Multi-user support:** Can be used by multiple users simultaneously.
- **Proxy support:** Allows for separate proxy configurations for Telegram and RSS feeds.
- **OPML import/export:** Users can easily import and export their feed subscriptions.
- **Performance Optimization:** The bot is designed for efficiency, using `uvloop`, HTTP caching, and optimized database queries.
- **Deployment:** The project is designed to be deployed using Docker (the recommended method), but can also be installed from PyPI or run directly from source. The `Dockerfile` is a multi-stage build that creates a minimal, optimized image.
- **Licensing:** The project is licensed under the AGPLv3+, which requires that any modifications be made available to users of the bot.

## Building and Running

### Docker

The most recommended way to deploy the bot is using Docker. The project includes a `Dockerfile` that can be used to build a Docker image for the bot. The `ENTRYPOINT` for the Docker image is `["python", "-u", "telegramRSSbot.py"]`, which starts the bot.

### PyPI

The bot can also be installed from PyPI using `pip`. The package name is `rsstt`.

```bash
pip install rsstt
```

### From Source

To run the bot from the source code, you will need to install the dependencies listed in the `requirements.txt` file. Then, you can run the bot using the following command:

```bash
python -m src
```

or

```bash
python telegramRSSbot.py
```

## Development Conventions

The project follows standard Python coding conventions. It uses `setuptools` for packaging and `pre-commit` for code quality checks. The project also includes a suite of tests that can be run using `pytest`.

The project uses `tortoise-orm` for database operations, and it supports both PostgreSQL and SQLite. The database migrations are managed using `aerich`.

The project is licensed under the AGPLv3+ license.
