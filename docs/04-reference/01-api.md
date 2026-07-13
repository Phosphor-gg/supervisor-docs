---
title: API Reference
description: Complete API documentation for Supervisor content moderation. Explore endpoints, authentication, request formats, and best practices.
slug: integrations/api
nav: API Reference
---

# API Reference

Complete reference for the Supervisor API endpoints.

## Authentication

All API requests require authentication using an API key in the Authorization header:

```text
Authorization: Bearer YOUR_API_KEY
```

## Base URL

```text
https://supervisor.gg/api
```

## POST /moderate

Moderate text content using AI models. Optionally include context analysis to determine if message history is needed.

### Request Body

```json
{
    "text": "string (optional, required if no image)",
    "image": "base64 string (optional, required if no text)",
    "model": "observer|sentinel|arbiter (optional, default: sentinel)",
    "enabled_labels": ["profanity", "toxicity", "harassment", "hate", "insult", "sexual", "sexual/unlawful", "sexual/explicit", "sensitive", "violence", "self-harm", "medical", "spam", "promotional", "scam", "illegal"] (optional),
    "include_context": boolean (optional, default: false, requires Basic plan or higher)
}
```

### Response

```json
{
    "flagged": boolean,
    "labels": string[],
    "implicit_labels": string[],
    "model_version": string,
    "needs_context": boolean (only present if include_context=true),
    "context_labels": string[] (only present if include_context=true)
}
```

### Example: Basic Moderation

```bash
curl -X POST https://supervisor.gg/api/moderate \
  -H "Authorization: Bearer sk_test_..." \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Hello, this is a test message",
    "model": "sentinel"
  }'
```

### Example: With Context Analysis

```bash
curl -X POST https://supervisor.gg/api/moderate \
  -H "Authorization: Bearer sk_test_..." \
  -H "Content-Type: application/json" \
  -d '{
    "text": "stop it",
    "model": "sentinel",
    "include_context": true
  }'

# Response:
{
    "flagged": false,
    "labels": [],
    "needs_context": true,
    "context_labels": ["harassment"]
}
```

### Example: Image Moderation

Images can be moderated by sending base64-encoded image data in the `image` field instead of `text`. Send text and images in separate requests.

```bash
# Encode image to base64 and send
curl -X POST https://supervisor.gg/api/moderate \
  -H "Authorization: Bearer sk_test_..." \
  -H "Content-Type: application/json" \
  -d '{
    "image": "<base64-encoded image data>",
    "model": "sentinel"
  }'
```

## GET /labels

Get a list of all available content labels and their descriptions.

### Response

```json
{
    "profanity": "Profanity",
    "toxicity": "Toxicity",
    "harassment": "Harassment",
    "hate": "Hate/Racism",
    "insult": "Insult",
    "sexual": "Sexual",
    "sexual/unlawful": "Sexual (Unlawful)",
    "sexual/explicit": "Sexual (Explicit)",
    "sensitive": "Sensitive Content",
    "violence": "Violence",
    "self-harm": "Self-Harm",
    "medical": "Medical/Injury",
    "spam": "Spam",
    "promotional": "Promotional",
    "scam": "Scam/Incoherent",
    "illegal": "Illegal Activity"
}
```

### Example

```bash
curl -X GET https://supervisor.gg/api/labels \
  -H "Authorization: Bearer sk_test_..."
```

## POST /batch

Moderate multiple texts in a single request. Useful for processing large volumes of content efficiently.

### Request Body

```json
{
    "texts": ["string"] (required array),
    "model": "observer|sentinel|arbiter (optional, default: sentinel)",
    "enabled_labels": ["profanity", "toxicity", "harassment", "hate", "insult", "sexual", "sexual/unlawful", "sexual/explicit", "sensitive", "violence", "self-harm", "medical", "spam", "promotional", "scam", "illegal"] (optional),
    "include_context": boolean (optional, default: false, requires Basic plan or higher)
}
```

### Response

```json
[
    {
        "flagged": boolean,
        "labels": string[],
        "implicit_labels": string[],
        "model_version": string,
        "needs_context": boolean (only present if include_context=true),
        "context_labels": string[] (only present if include_context=true)
    }
]
```

### Example

```bash
curl -X POST https://supervisor.gg/api/batch \
  -H "Authorization: Bearer sk_test_..." \
  -H "Content-Type: application/json" \
  -d '{
    "texts": [
      "First message to check",
      "Second message to check",
      "Third message to check"
    ],
    "model": "sentinel",
    "include_context": true
  }'
```

## POST /username

Check whether a username is allowed. Returns a flag and a confidence score.

### Request Body

```json
{ "username": "user123" }
```

### Response

```json
{ "flagged": false, "score": 0.12 }
```

### Example

```bash
curl -X POST https://supervisor.gg/api/username \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{ "username": "user123" }'
```

## Platform API

Building moderation into your own product for many of your own users? The Platform API adds OAuth2 client credentials, per-user provisioning, and delegated moderation. See the dedicated [Platform API guide](/docs/platform-api) for the full flow and endpoint reference.

## Models

- **observer** (v2.0): Fast, lightweight model for high-volume applications (~£0.0013/KB)
- **sentinel** (v2.0): Balanced model with good accuracy and speed (~£0.0041/KB)
- **arbiter** (v2.0): Most accurate model for critical moderation (~£0.0123/KB)

## Labels

| Label | Description |
| --- | --- |
| `profanity` | Profanity |
| `toxicity` | General toxicity |
| `harassment` | Harassment or bullying |
| `hate` | Hate speech or racism |
| `insult` | Insult |
| `sexual` | Sexual content |
| `sexual/unlawful` | Severe sexual content or minors |
| `sexual/explicit` | Explicit sexual depictions |
| `sensitive` | Sensitive content |
| `violence` | Violence or gore |
| `self-harm` | Self-harm content |
| `medical` | Medical/Injury |
| `spam` | Spam |
| `promotional` | Promotional |
| `scam` | Scam/Incoherent |
| `illegal` | Illegal activity |

## Credits and Billing

Usage is billed based on credits consumed. Text is billed per byte at the model's rate:

| Model | Credits per byte | Cost per KB |
| --- | --- | --- |
| **Observer** | 1 | ~£0.0014 |
| **Sentinel** | 2 | ~£0.0027 |
| **Arbiter** | 4 | ~£0.0055 |

**Images** are billed at a flat **1 credit per byte** of image data, regardless of model (~£0.0014/KB). If our OCR extracts readable text from the image, that text is additionally billed at the model's per-byte rate — you only pay text-model prices when the text models actually run.

**Examples:**

- Moderating 1 KB of text with Sentinel costs 2,048 credits (~£0.0027).
- Moderating a 100 KB image with no readable text costs 102,400 credits (~£0.14) on any model.
- Moderating a 100 KB image containing 200 bytes of readable text with Arbiter costs 102,400 + 800 = 103,200 credits (~£0.14).

Cached results are free: repeating an identical (or near-identical, for images) request within the cache window consumes no credits.

The grandfathered free tier (accounts created before 13 July 2026 only) includes 0.25 GBP of credits per month; newer accounts start with a paid plan or Verified. View your credit usage in the Dashboard.

**When your balance is exhausted:** API requests will fail with HTTP 402 Payment Required until credits are replenished through monthly reset or subscription upgrade.

## Subscription Tier Requirements

Your subscription tier determines which AI models you can access:

| Subscription Tier | Available Models | Monthly Allowance | Context Available |
| --- | --- | --- | --- |
| **Free** (grandfathered, pre-13 July 2026 accounts) | Observer only | 0.25 GBP of credits | No |
| **Basic** | Observer only | See the pricing page for current credit allowances | Yes |
| **Standard** | Observer, Sentinel | See the pricing page for current credit allowances | Yes |
| **Premium** | Observer, Sentinel, Arbiter | See the pricing page for current credit allowances | Yes |

**Important:** Attempting to use a model not included in your subscription tier will result in a 400 Bad Request error. Upgrade your plan to access higher-tier models.

## Error Codes

| Code | Description |
| --- | --- |
| `400` | Bad request: invalid parameters or unsupported model for your subscription tier |
| `401` | Unauthorized: invalid or missing API key |
| `402` | Payment Required: insufficient credits to process request |
| `429` | Too Many Requests: rate limit exceeded |
| `500` | Internal server error: unexpected error occurred |
