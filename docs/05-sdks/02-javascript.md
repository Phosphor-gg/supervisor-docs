---
title: JavaScript / TypeScript SDK
description: "Official JavaScript/TypeScript SDK for the Supervisor content moderation API for Node, Bun, Deno, and browsers."
---

# JavaScript / TypeScript SDK

Official TypeScript client for Node, Bun, Deno, and the browser. Zero dependencies (native fetch).

## Install

```bash
npm install supervisor-sdk
```

Ships with full type definitions. Grab an API key from your dashboard under API Keys.

## Moderate text

```typescript
import { SupervisorClient } from "supervisor-sdk";

const client = new SupervisorClient({ apiKey: "sk_prod_..." });

const result = await client.moderate({ text: "text to check" });
console.log(result.flagged, result.labels);
```

## Methods

```typescript
// Single text or image (base64)
await client.moderate({ text, image, model, enabled_labels, include_context });

// Many at once -> ModerationResponse[]
await client.moderateBatch({ texts, images, model });

// Username policy check -> { flagged, score }
await client.checkUsername("user123");

// All labels -> Record<string, string>
await client.getLabels();
```

All methods return Promises. model is "auto" | "observer" | "sentinel" | "arbiter".

## Platform API

Moderate on behalf of your own users with a registered platform's client_id / client_secret (created in the dashboard under Platform API).

```typescript
import { PlatformClient } from "supervisor-sdk";

const platform = new PlatformClient({ clientId: "...", clientSecret: "sk_platform_..." });

await platform.provisionUser("user@example.com");
const result = await platform.moderate({ user_email: "user@example.com", text: "text to check" });
```

Other methods: listUsers(), getUser(id), createCheckout(...), getConnectStatus(). See the Platform API guide.
