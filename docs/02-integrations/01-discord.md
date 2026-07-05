---
title: Discord Integration
description: Set up Supervisor moderation bot for Discord with AI-powered filtering. Configure automatic message moderation and custom content filters.
slug: integrations/discord
nav: Discord
---

# Discord Integration

Automatically moderate your Discord server with Supervisor's AI-powered bot.

## 1. Invite the Bot

Add Supervisor to your Discord server:

1. Go to Dashboard > Integrations
2. Click 'Add to Discord'
3. Select your server and authorize the bot
4. **Grant required permissions:** The bot requires 'Manage Messages' permission to delete flagged content and 'Read Message History' to fetch conversation context

**Note:** You must be the server owner or have administrator permissions to add and configure Supervisor.

## 2. Configure Channels

Choose which channels Supervisor should monitor and where to send alerts:

- **Moderated Channels:** Moderate all channels (default), or select specific channels to monitor
- **Alerts Channel (Optional):** Designate a channel to receive alert notifications when content is flagged, including user, channel, violation type, and message preview

## 3. Set Moderation Rules

Define how Supervisor handles flagged content:

- **Delete:** Automatically delete flagged messages
- **Warn:** Send a warning to the user via DM
- **Timeout:** Temporarily mute the user (5 minutes)

## 4. Choose Labels

Select which types of content to moderate:

- Sexual Content (S)
- Harassment (H)
- Violence (V)
- Hate/Racism (HR)
- Self-Harm (SH)
- Sexual (Unlawful) (SU)
- Sensitive Content (SE)
- Spam (SP)
- Toxicity (T)

## 5. Context-Aware Moderation

The bot automatically detects when a message needs conversation history for accurate moderation (requires Basic plan or higher):

- The bot checks if the message needs context using the `include_context` parameter in the moderation request
- If context is needed, it fetches recent message history from the channel
- Message history is included in a follow-up moderation request for better accuracy
- You can configure how many previous messages to include (configurable count)

This ensures ambiguous messages like "stop it" or "not cool" are properly understood based on the conversation.

## 6. Advanced Configuration

Additional configuration options available in the dashboard:

- **Role Exclusions/Inclusions:** Choose specific roles to exclude from moderation or only moderate specific roles
- **Billing User:** The server owner can designate which user's API credits are used for moderation. By default, the server owner's credits are used.
- **Model Selection:** Choose which AI model to use (Observer, Sentinel, or Arbiter) based on your subscription tier and accuracy needs
- **Channel Scope:** Enable 'Moderate All Channels' to monitor every channel, or select specific channels to monitor

**Important:** The server owner must explicitly opt-in to use their account for billing. An account with sufficient credits and appropriate subscription tier is required.

## 7. Bot Commands

The Supervisor bot provides a single command for getting help and configuration instructions:

```text
/help - Get help and view configuration URL
```

All configuration is managed through the web dashboard at **https://supervisor.gg**

## 8. How It Works

When a message is sent in a moderated channel, the bot:

1. Checks if the message needs conversation context
2. Fetches message history if needed (configurable count)
3. Sends the message to the moderation API with your selected model
4. Takes configured actions if content is flagged (Delete/Warn/Timeout)
5. Sends detailed alerts to your alerts channel (if configured)

Alert notifications include the flagged user, channel, content type, and message preview.

All moderation uses your account's credits. View usage in the Dashboard.
