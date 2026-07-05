---
title: Python SDK
description: "Official Python SDK for the Supervisor content moderation API. Install, configure, and start moderating in a few lines."
slug: sdks/python
nav: Python
---

# Python SDK

Official async + sync Python client for the Supervisor moderation API.

## Install

```bash
pip install supervisor-sdk
```

Requires Python 3.10+. Grab an API key from your dashboard under API Keys.

## Moderate text

```python
import asyncio
from supervisor import SupervisorClient

async def main():
    async with SupervisorClient(api_key="sk_prod_...") as client:
        result = await client.moderate("text to check")
        print(result.flagged, result.labels)

asyncio.run(main())
```

Prefer synchronous code? Use SyncSupervisorClient with the same methods, minus the await.

```python
from supervisor import SyncSupervisorClient

with SyncSupervisorClient(api_key="sk_prod_...") as client:
    result = client.moderate("text to check")
```

## Methods

Available on both SupervisorClient (async) and SyncSupervisorClient:

```python
# Single text or image (base64). model: "auto" | "observer" | "sentinel" | "arbiter"
await client.moderate(text, image=None, model=None, enabled_labels=None, include_context=False)

# Many at once -> list[ModerationResponse]
await client.moderate_batch(texts, images=None, model=None, enabled_labels=None)

# Username policy check -> UsernameCheckResponse(flagged, score)
await client.check_username("user123")

# All labels -> {key: description}
await client.get_labels()
```

A ModerationResponse has: flagged (bool), labels (list[str]), implicit_labels, model_version, and optional context fields.

## Platform API

If you have a registered platform, use PlatformClient to moderate on behalf of your own users with your client_id / client_secret (created in the dashboard under Platform API).

```python
from supervisor import PlatformClient

async with PlatformClient(client_id="...", client_secret="sk_platform_...") as p:
    user = await p.provision_user("user@example.com")
    result = await p.moderate("user@example.com", text="text to check")
```

Other methods: list_users(), get_user(id), create_checkout(...), get_connect_status(). See the Platform API guide for the full flow.
