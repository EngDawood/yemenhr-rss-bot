# Default Subscriptions Feature

## Overview

The default subscriptions feature automatically adds predefined RSS feeds to new users when they first interact with the bot. This feature is particularly useful for the Yemen HR bot to ensure all users receive important job and tender notifications without manual setup.

## Implementation Details

### Default Subscription URLs

The feature subscribes new users to two predefined feeds:

1. **Yemen HR Jobs**: `https://fetchrss.com/feed/aLS3RMahL60yaLTMyXb2OBOy.rss`
2. **Yemen HR Tenders**: `https://fetchrss.com/feed/aLS3RMahL60yaLTCVPjZpzNi.rss`

### How It Works

1. When a new user interacts with the bot for the first time, they select their preferred language using the `/lang` command
2. The `callback_set_lang` function in `src/command/misc.py` is triggered
3. The function detects if the user is new (using `update_or_create` which returns a tuple with a "created" flag)
4. If the user is new, the system asynchronously calls `add_default_subscriptions` from `src/command/inner/sub.py`
5. The default subscriptions are added to the user's subscription list with custom titles

### Key Files

1. **`src/command/inner/sub.py`**:
   - Contains the `DEFAULT_SUBSCRIPTIONS` list with feed URLs and titles
   - Implements the `add_default_subscriptions` function that subscribes new users

2. **`src/command/misc.py`**:
   - Modified `callback_set_lang` function that detects new users and triggers default subscriptions

3. **Language Files** (`src/i18n/*.json`):
   - Added `default_subscriptions_added` message to inform users about the automatic subscriptions

### Asynchronous Processing

The default subscriptions are added asynchronously to avoid blocking the initial user response. This is implemented using `env.loop.create_task()` to ensure a smooth user experience.

## Testing

To test the feature:

1. Create a new Telegram user or use a user that has never interacted with the bot
2. Start a conversation with the bot
3. Select a language when prompted
4. Verify that the default subscriptions appear in the user's subscription list using the `/list` command

## Customization

To modify the default subscriptions:

1. Edit the `DEFAULT_SUBSCRIPTIONS` list in `src/command/inner/sub.py`
2. Update the welcome message in the i18n JSON files if needed
3. The changes will apply to all new users

## Unsubscribing

Users can unsubscribe from default subscriptions just like any other subscription:
- Use the `/list` command to view subscriptions
- Use the `/unsub` command to remove unwanted subscriptions
- Or use `/unsub_all` to remove all subscriptions

The default subscriptions do not prevent users from managing their subscriptions freely.