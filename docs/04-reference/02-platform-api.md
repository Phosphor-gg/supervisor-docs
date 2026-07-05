---
title: Platform API
description: "Build moderation into your own product with the Supervisor Platform API: OAuth2 client credentials, per-user provisioning, and delegated moderation."
slug: platform-api
nav: Platform API
---

# Platform API

Build moderation into your own product and run it on behalf of your users. The Platform API gives you OAuth2 client credentials, per-user provisioning, and optional revenue-share billing.

## Who it's for

The Platform API is for products that moderate content for many of their own end-users (a community platform, a SaaS, a marketplace). Instead of one API key, you get a client_id / client_secret, link your users by email, and moderate against each user's own plan and credits. It requires a Premium subscription.

## 1. Register a platform

In your dashboard, open Platform API and click Register Platform. You'll set a name and a redirect URI, and receive a client_id and client_secret. The secret is shown once: store it securely. You can register multiple platforms and rotate the secret at any time.

## 2. Get an access token

Exchange your credentials for a short-lived bearer token (valid 1 hour). The SDKs do this for you automatically; the raw call is:

```bash
curl -X POST https://supervisor.gg/api/platform/token \
  -H "Content-Type: application/json" \
  -d '{
    "grant_type": "client_credentials",
    "client_id": "YOUR_CLIENT_ID",
    "client_secret": "sk_platform_..."
  }'

# -> { "access_token": "...", "token_type": "Bearer", "expires_in": 3600 }
```

Send the token on every other Platform API call as `Authorization: Bearer <access_token>`.

## 3. Provision a user

Link one of your users to your platform by email. This creates a Supervisor account for them if they don't have one yet.

```bash
curl -X POST https://supervisor.gg/api/platform/users/provision \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{ "email": "user@example.com" }'

# -> { "user_id": "...", "email": "...", "is_new_account": true, "is_newly_linked": true }
```

## 4. Moderate on behalf of a user

Once a user has authorized your platform, moderate content against their plan and credits. The request and response match the standard moderation endpoint, plus a user_email field.

```bash
curl -X POST https://supervisor.gg/api/platform/moderate \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "user_email": "user@example.com",
    "text": "text to check",
    "model": "auto"
  }'

# -> { "flagged": false, "labels": [], "model_version": "2.0" }
```

Note: moderation requires the user to have authorized your platform. If they haven't, the call returns 403: send them through the authorization (consent) flow first.

## 5. The authorization (consent) flow

Before you can moderate or bill for a user, they must approve your platform once. Send them to the consent page with a random state value:

```text
https://supervisor.gg/authorize?platform_id=YOUR_CLIENT_ID&state=RANDOM_STATE
```

After they approve, Supervisor redirects to your registered redirect URI with ?code=...&state=.... Verify the state matches what you sent, then exchange the code (codes are single use and short lived):

```bash
curl -X POST https://supervisor.gg/api/platform/users/confirm-authorization \
  -H "Authorization: Bearer ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{ "code": "CODE_FROM_REDIRECT" }'

# -> { "user_id": "...", "email": "...", "authorized": true }
```

Users can revoke access at any time from their dashboard under Authorized Apps. After a revoke, moderation and billing calls for that user return 403 until they authorize again.

## Selling plans and credits (revenue share)

Platforms can sell Supervisor subscriptions and credit packs from their own site and take a revenue share of every payment, renewals included. Complete Stripe Connect onboarding first (dashboard, Platform API, Complete Stripe onboarding); payouts require a Connect account in a supported country (US, UK, EEA, Canada, Switzerland), which you can pick when registering the platform.

List what you can sell, then mint a checkout link per user and redirect them to it. Links must be minted through these endpoints for the revenue share to apply.

```bash
# What can I sell?
GET /api/platform/products
# -> { "plans": [ { "price_id", "tier", "billing_cycle", "amount", "currency", ... } ],
#      "credit_packs": [ { "price_id", "name", "price_cents", "credits_amount", ... } ],
#      "lifetime": { "price_id", "name", "amount", "currency", "monthly_credits" } }

# Subscription checkout (new subscription for the user)
POST /api/platform/checkout
{ "user_email": "...", "tier": "premium", "billing_cycle": "monthly",
  "success_url": "https://myapp.com/thanks", "cancel_url": "https://myapp.com/pricing" }
# -> { "checkout_url": "https://checkout.stripe.com/..." }

# Credit pack checkout (one-time payment)
POST /api/platform/checkout-credits
{ "user_email": "...", "price_id": "price_...",
  "success_url": "...", "cancel_url": "..." }
# -> { "checkout_url": "..." }

# Verified lifetime plan (one-time payment, same revenue share)
POST /api/platform/checkout
{ "user_email": "...", "tier": "verified", "billing_cycle": "monthly",
  "success_url": "...", "cancel_url": "..." }
# -> { "checkout_url": "..." } (billing_cycle is ignored for verified)

# Switch an existing subscription you sold (same Stripe customer, prorated)
POST /api/platform/change-plan
{ "user_email": "...", "tier": "premium", "billing_cycle": "annual" }
# -> { "subscription_id": "sub_...", "tier": "premium", "billing_cycle": "annual" }
```

Rules: the user must have authorized your platform (403 otherwise). Checkout returns 400 if the user already has an active subscription; use change-plan instead. Change-plan only works on subscriptions that were created through your platform. Revenue attribution is set when the subscription is created and is preserved across plan changes. The Verified lifetime plan (tier "verified") is a one-time purchase: it can be sold even to users with an active subscription (the subscription rides out its paid period, then Verified takes over), returns 400 if the user already owns it, and cannot be used with change-plan.

## User credits

Show an authorized user their remaining credits, for example next to your buy-credits button:

```bash
GET /api/platform/users/{user_id}/credits

# -> { "user_id": "...", "email": "...", "balance": 950,
#      "monthly_allocation": 1000, "used_this_month": 50,
#      "remaining_this_month": 950, "extra_credits": 0,
#      "reset_date": "2026-08-01 00:00:00" }
```

## Endpoints

| Method | Path | Purpose |
| --- | --- | --- |
| `POST` | `/api/platform/token` | Exchange client credentials for an access token |
| `POST` | `/api/platform/users/provision` | Create or link a user by email |
| `GET` | `/api/platform/users` | List your linked users |
| `GET` | `/api/platform/users/{user_id}` | Get a linked user |
| `POST` | `/api/platform/moderate` | Moderate on behalf of a user |
| `POST` | `/api/platform/users/confirm-authorization` | Exchange a consent-flow code |
| `GET` | `/api/platform/users/{user_id}/credits` | Remaining credits of an authorized user |
| `GET` | `/api/platform/products` | Plans and credit packs you can sell |
| `POST` | `/api/platform/checkout` | Subscription checkout for a user (revenue share) |
| `POST` | `/api/platform/checkout-credits` | Credit pack checkout for a user (revenue share) |
| `POST` | `/api/platform/change-plan` | Switch a subscription you sold to another plan |
| `GET` | `/api/platform/connect/status` | Stripe Connect onboarding status |

## Use an SDK

Every official SDK has a PlatformClient that handles the token exchange and refresh for you. For example, in Python:

```python
from supervisor import PlatformClient

async with PlatformClient(client_id="...", client_secret="sk_platform_...") as p:
    await p.provision_user("user@example.com")
    result = await p.moderate("user@example.com", text="text to check")
```

See the per-language SDK pages for Go, Rust, Java, and JavaScript equivalents.
