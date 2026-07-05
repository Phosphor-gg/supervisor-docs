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

Select which types of content to moderate. All 16 labels can be toggled individually in the dashboard:

| Label | What it catches |
| --- | --- |
| Profanity | Swear words and obscene language |
| Toxicity | Generally toxic or disrespectful behavior |
| Harassment | Bullying, intimidation, or targeted harassment |
| Hate/Racism | Hate speech, slurs, or discrimination |
| Insult | Casual insults directed at a person or group |
| Sexual | Sexually explicit or suggestive content |
| Sexual (Unlawful) | Severe sexual content or content involving minors |
| Sexual (Explicit) | Explicit sexual depictions or solicitation |
| Sensitive Content | Other sensitive or inappropriate content |
| Violence | Graphic violence or violent threats |
| Self-Harm | Content promoting self-harm or suicide |
| Medical/Injury | Medical trauma or physical injury descriptions |
| Spam | Repetitive, unsolicited, or irrelevant content |
| Promotional | Promotional or advertising content |
| Scam/Incoherent | Scam, phishing, or incoherent content |
| Illegal Activity | Content encouraging or describing illegal activity |

## 5. Context-Aware Moderation

Turn on the Context-Aware Moderation toggle in the dashboard (requires a Basic plan or higher) and the bot understands messages as part of the conversation instead of in isolation:

- The bot automatically detects when a message is ambiguous on its own
- When needed, it reads recent channel history to judge the message in context
- The Context History Count setting controls how many previous messages it may consider

This ensures ambiguous messages like "stop it" or "not cool" are properly understood based on the conversation.

## Image Moderation

Turn on the Image Moderation toggle in the dashboard (requires a Standard plan or higher) and the bot also analyzes images posted in moderated channels, applying the same labels and actions as it does for text.

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

1. Checks whether the message needs conversation context (when Context-Aware Moderation is on)
2. Reads recent channel history if needed
3. Analyzes the message (and images, when Image Moderation is on) with your selected AI model
4. Takes your configured actions if content is flagged (Delete/Warn/Timeout)
5. Sends detailed alerts to your alerts channel (if configured)

Alert notifications include the flagged user, channel, content type, and message preview.

All moderation uses your account's credits. View usage in the Dashboard.
