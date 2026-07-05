---
title: Rust SDK
description: "Official async Rust SDK for the Supervisor content moderation API. Built on Tokio and reqwest to add fast, context-aware moderation to your Rust app."
slug: sdks/rust
nav: Rust
---

# Rust SDK

Official async Rust client built on reqwest + tokio.

## Install

```toml
[dependencies]
supervisor-sdk = { git = "https://github.com/Phosphor-gg/supervisor-sdk-rust" }
tokio = { version = "1", features = ["full"] }
```

Grab an API key from your dashboard under API Keys.

## Moderate text

```rust
use supervisor_sdk::{SupervisorClient, ModerationRequest};

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    let client = SupervisorClient::new("sk_prod_...")?;
    let res = client.moderate(ModerationRequest {
        text: Some("text to check".into()),
        ..Default::default()
    }).await?;
    println!("{} {:?}", res.flagged, res.labels);
    Ok(())
}
```

## Methods

```rust
client.moderate(ModerationRequest) -> Result<ModerationResponse>
client.moderate_batch(BatchModerationRequest) -> Result<Vec<ModerationResponse>>
client.check_username("user123") -> Result<UsernameCheckResponse>
client.get_labels() -> Result<HashMap<String, String>>
```

Use SupervisorClient::with_base_url(key, url) to point at a custom base URL.

## Platform API

Moderate on behalf of your own users with a registered platform's credentials (from the dashboard under Platform API).

```rust
use supervisor_sdk::{PlatformClient, PlatformModerationRequest};

let p = PlatformClient::new("client-id", "sk_platform_...")?;
p.provision_user("user@example.com").await?;
let res = p.moderate(PlatformModerationRequest {
    user_email: "user@example.com".into(),
    text: Some("text to check".into()),
    ..Default::default()
}).await?;
```

Other methods: list_users, get_user, create_checkout, get_connect_status. See the Platform API guide.
