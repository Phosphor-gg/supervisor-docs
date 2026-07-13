---
title: Java SDK
description: "Official Java SDK for the Supervisor content moderation API. Add it to any Maven or Gradle project to moderate text from any JVM application."
slug: sdks/java
nav: Java
---

# Java SDK

Official Java client (Java 17+), built on the JDK HttpClient + Jackson.

## Install

The SDK is distributed from GitHub via [JitPack](https://jitpack.io/#Phosphor-gg/supervisor-sdk-java).

Gradle — add the JitPack repository, then the dependency:

```kotlin
repositories {
    mavenCentral()
    maven { url = uri("https://jitpack.io") }
}

dependencies {
    implementation("com.github.Phosphor-gg:supervisor-sdk-java:v0.2.2")
}
```

Maven:

```xml
<repositories>
  <repository>
    <id>jitpack.io</id>
    <url>https://jitpack.io</url>
  </repository>
</repositories>

<dependency>
  <groupId>com.github.Phosphor-gg</groupId>
  <artifactId>supervisor-sdk-java</artifactId>
  <version>v0.2.2</version>
</dependency>
```

## Moderate text

```java
import gg.supervisor.sdk.SupervisorClient;
import gg.supervisor.sdk.models.ModerationRequest;

var client = SupervisorClient.builder().apiKey("sk_prod_...").build();

var result = client.moderate(
    ModerationRequest.builder().text("text to check").build());
System.out.println(result.flagged() + " " + result.labels());
```

## Methods

```java
client.moderate(ModerationRequest)            -> ModerationResponse
client.moderateBatch(BatchModerationRequest) -> List<ModerationResponse>
client.checkUsername("user123")              -> UsernameCheckResponse
client.getLabels()                           -> Map<String, String>
```

Configure the builder with .baseUrl(url) and .timeout(seconds).

## Platform API

Moderate on behalf of your own users with a registered platform's credentials (from the dashboard under Platform API).

```java
import gg.supervisor.sdk.PlatformClient;
import gg.supervisor.sdk.models.PlatformModerationRequest;

var p = PlatformClient.builder()
    .clientId("...").clientSecret("sk_platform_...").build();

p.provisionUser("user@example.com");
var result = p.moderate(PlatformModerationRequest.builder()
    .userEmail("user@example.com").text("text to check").build());
```

Other methods: listUsers(), getUser(id), createCheckout(...), getConnectStatus(). See the Platform API guide.
