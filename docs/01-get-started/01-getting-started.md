---
title: Getting Started
description: Learn to integrate Supervisor AI moderation API with step-by-step instructions. Create your account, get API keys, and make your first request.
slug: getting-started
---

# Getting Started

Welcome to Supervisor! This guide will help you get up and running with our moderation API in minutes.

## 1. Create Your Account

First, sign up for a Supervisor account using Discord, Google, or GitHub OAuth.

## 2. Get Your API Key

Navigate to the API Keys section in your dashboard to generate your first API key.

1. Go to Dashboard > API Keys
2. Click 'Create New Key'
3. Give your key a descriptive name
4. Copy and securely store your API key (format: sk_prod_...)

## 3. Make Your First Request

Use your API key to moderate content. Select a model:

- Observer (~£0.0013/KB)
- Sentinel (~£0.0041/KB)
- Arbiter (~£0.0123/KB)

```bash
curl -X POST https://supervisor.gg/api/moderate \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Your content to moderate",
    "model": "observer"
  }'
```

## 4. Understand the Response

The API returns a JSON response with moderation results:

```json
{
    "flagged": false,
    "labels": [],
    "scores": {
        "profanity": 0.01,
        "toxicity": 0.02,
        "harassment": 0.02,
        "hate": 0.01,
        "insult": 0.01,
        "sexual": 0.01,
        "sexual/unlawful": 0.01,
        "sexual/explicit": 0.01,
        "sensitive": 0.02,
        "violence": 0.01,
        "self-harm": 0.01,
        "medical": 0.01,
        "spam": 0.03,
        "promotional": 0.01,
        "scam": 0.01,
        "illegal": 0.01
    }
}
```

## 5. Model Tiers and Subscription Requirements

Your subscription tier determines which AI models you can access:

- **Free Tier:** Observer model only (1 GBP of credits each month, no context)
- **Basic Tier:** Observer model only (includes context; see the pricing page for current credit allowances)
- **Standard Tier:** Observer + Sentinel models (includes context; see the pricing page for current credit allowances)
- **Premium Tier:** All models: Observer, Sentinel, Arbiter (includes context; see the pricing page for current credit allowances)
- **Verified (lifetime):** Observer model only, like the free tier, but with a larger monthly credit allowance and paid once instead of billed monthly. If you also hold a subscription, the subscription applies while it is active and Verified takes over when it lapses.

Attempting to use a model not included in your tier will result in an error.

## 6. Credit Management

Each moderation request consumes credits based on content size and model used:

- Observer: ~£0.0013/KB
- Sentinel: ~£0.0041/KB
- Arbiter: ~£0.0123/KB

**When your balance is exhausted:** Requests will fail with a 402 Payment Required error until credits are replenished (monthly reset or upgrade).

## 7. Advanced: Context-Aware Moderation

Some messages may need conversation history for accurate moderation. Use the `include_context` parameter to check (requires Basic plan or higher):

```bash
curl -X POST https://supervisor.gg/api/moderate \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "stop it",
    "include_context": true
  }'

# Response:
{
    "flagged": false,
    "labels": [],
    "scores": {...},
    "needs_context": true,
    "context_labels": ["harassment"]
}
```

If `needs_context` is true, include message history in a follow-up moderation request for better accuracy.

## 8. Image Moderation

You can also moderate images by sending base64-encoded image data in the `image` field instead of `text`:

```bash
curl -X POST https://supervisor.gg/api/moderate \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "image": "<base64-encoded image data>",
    "model": "sentinel"
  }'
```

Send text and images in separate requests. Costs for images are calculated based on the original image file size.

## 9. Next Steps

- [Explore the full API reference](/docs/integrations/api)
- [Set up Discord integration](/docs/integrations/discord)
