# AuthKeyz Documentation

<div align="center">
  <img src="https://authkeyz.bond/assets/img/authkeyz-logo.png" alt="AuthKeyz" width="120" />
  <h3>High-Performance Software Authentication & Cloud Licensing</h3>
  <p>Secure, developer-friendly cloud authentication system with Ed25519 digital signatures, HWID machine-locking, and zero-trust session management.</p>

  [![Website](https://img.shields.io/badge/Website-authkeyz.bond-blue)](https://authkeyz.bond)
  [![Discord](https://img.shields.io/badge/Community-Discord-5865F2)](https://discord.gg/KNRpCwWANJ)
  [![API Version](https://img.shields.io/badge/API-v1.3%20%7C%20v1.2-purple)](#architecture--api-versions)
</div>

---

## Table of Contents
- [Overview](#overview)
- [Architecture & API Versions](#architecture--api-versions)
  - [API 1.3 (Modern Asymmetric Ed25519)](#api-13-master-protocol)
  - [API 1.2 (Legacy Symmetric HMAC-SHA256)](#api-12-legacy-protocol)
- [Master Cryptographic Parameters](#master-cryptographic-parameters)
- [Official SDK Repositories](#official-sdk-repositories)
- [Quick Start Integration Examples](#quick-start-integration-examples)
  - [C++ (API 1.3)](#c-api-13)
  - [C# (.NET Core / WPF / WinForms)](#c-net)
  - [Python](#python)
  - [JavaScript / TypeScript](#javascript--typescript)
  - [Go](#go)
  - [Rust](#rust)
- [Security Features](#security-features)
  - [Replay Attack Protection](#replay-attack-protection)
  - [Anti-Host Redirection / Anti-Spoofing](#anti-host-redirection--anti-spoofing)
- [Web Documentation](#web-documentation)

---

## Overview

**AuthKeyz** is an enterprise-grade cloud authentication backend designed to protect Windows, macOS, Linux, and web applications from reverse-engineering, tampering, and key-sharing.

Key capabilities:
- **Asymmetric Ed25519 Signatures**: Cryptographically tamper-proof packet validation. Client binary never holds the server's private key.
- **Hardware ID (HWID) Locking**: Enforces single-device license policies.
- **Replay Protection**: Duplicate signature caching prevents network packet replay attacks.
- **Anti-Host Redirection**: Detects `hosts` file tampering and local proxy loopbacks (`127.0.0.1`, `::1`).
- **Cloud Variables & Files**: Encrypted server-side variables and streaming file downloads.
- **Dual Compatibility**: Easily drop in or migrate from official KeyAuth setups.

---

## Architecture & API Versions

### API 1.3 (Master Protocol)
* **Endpoint:** `https://authkeyz.bond/api/1.3/`
* **Cryptography:** Asymmetric **Ed25519** detached digital signature + Unix timestamp freshness verification.
* **Security Benefit:** Secretless architecture. The client binary contains only the **public key**. Even if an attacker completely dumps the client's memory or decompiles the binary, they cannot forge responses because the server signs packets using the Ed25519 **private key**.
* **Headers returned:**
  * `x-signature-ed25519`: 64-byte hex Ed25519 detached signature.
  * `x-signature-timestamp`: Unix timestamp of the server response.
  * `signature`: Legacy HMAC-SHA256 for backward-compatibility.

### API 1.2 (Legacy Protocol)
* **Endpoint:** `https://authkeyz.bond/api/1.2/`
* **Cryptography:** Symmetric **HMAC-SHA256**.
* **Compatibility:** Drop-in compatibility for existing KeyAuth 1.2 binaries by updating the endpoint URL to `https://authkeyz.bond/api/1.2/`.

---

## Master Cryptographic Parameters

| Parameter | Value |
| :--- | :--- |
| **API Base Gateway** | `https://authkeyz.bond/api/1.3/` |
| **Legacy Gateway** | `https://authkeyz.bond/api/1.2/` |
| **Ed25519 Master Public Key** | `8e8d104f3ce9b29b411cfcf8e049d19fed42e63caa275e111bfa71ef3d237041` |
| **Allowed Clock Skew** | 300 seconds (5 minutes) |

---

## Official SDK Repositories

| Language / Framework | Repository Link | Target API |
| :--- | :--- | :--- |
| **C++ 1.3 Example** | [AuthKeyz-CPP-Example-API_1.3](https://github.com/AuthKeyz/AuthKeyz-CPP-Example-API_1.3) | API 1.3 |
| **C++ 1.3 Library** | [authkeyz-cpp-library-1.3API](https://github.com/AuthKeyz/authkeyz-cpp-library-1.3API) | API 1.3 |
| **C++ 1.2 Example** | [AuthKeyz-CPP-Example-API_1.2](https://github.com/AuthKeyz/AuthKeyz-CPP-Example-API_1.2) | API 1.2 |
| **C++ 1.2 Library** | [authkeyz-cpp-library-1.2API](https://github.com/AuthKeyz/authkeyz-cpp-library-1.2API) | API 1.2 |
| **C# (.NET / WinForms / Console)** | [AuthKeyz-CSHARP-Example](https://github.com/AuthKeyz/AuthKeyz-CSHARP-Example) | Dual (1.2 & 1.3) |
| **C# (WPF Modern UI)** | [AuthKeyz-WPF-Example](https://github.com/AuthKeyz/AuthKeyz-WPF-Example) | Dual (1.2 & 1.3) |
| **Python** | [AuthKeyz-Python-Example](https://github.com/AuthKeyz/AuthKeyz-Python-Example) | Dual (1.2 & 1.3) |
| **JavaScript / TypeScript** | [AuthKeyz-JavaScript-Example](https://github.com/AuthKeyz/AuthKeyz-JavaScript-Example) | Dual (1.2 & 1.3) |
| **React SPA** | [AuthKeyz-React-Example](https://github.com/AuthKeyz/AuthKeyz-React-Example) | API 1.3 |
| **Vue.js SPA** | [AuthKeyz-Vue-Example](https://github.com/AuthKeyz/AuthKeyz-Vue-Example) | API 1.3 |
| **Go** | [AuthKeyz-Go-Example](https://github.com/AuthKeyz/AuthKeyz-Go-Example) | API 1.3 |
| **Rust Crate** | [authkeyz-rust](https://github.com/AuthKeyz/authkeyz-rust) | Multi (1.0 - 1.3) |
| **PHP** | [AuthKeyz-PHP-Example](https://github.com/AuthKeyz/AuthKeyz-PHP-Example) | API 1.3 |
| **Custom Customer Web Panel** | [AuthKeyz-Custom-Customer-Panel](https://github.com/AuthKeyz/AuthKeyz-Custom-Customer-Panel) | API 1.3 |

---

## Quick Start Integration Examples

### C++ (API 1.3)
```cpp
#include <auth.hpp>

using namespace AuthKeyz;

std::string name = "YourAppName";
std::string ownerid = "YourOwnerID";
std::string secret = "YourAppSecret";
std::string version = "1.0";
std::string url = "https://authkeyz.bond/api/1.3/";

api AuthKeyzApp(name, ownerid, secret, version, url);

int main() {
    AuthKeyzApp.init();
    
    // Login with username and password
    AuthKeyzApp.login("username", "password");
    
    // Or authenticate with license key:
    // AuthKeyzApp.license("LICENSE-KEY-HERE");
    
    std::cout << "Logged in as: " << AuthKeyzApp.user_data.username << std::endl;
    return 0;
}
```

### C# (.NET)
```csharp
using AuthKeyz;

public static api AuthKeyzApp = new api(
    name: "YourAppName",
    ownerid: "YourOwnerID",
    secret: "YourAppSecret",
    version: "1.0"
);

static void Main() {
    AuthKeyzApp.init();
    
    AuthKeyzApp.login("username", "password");
    // Or: AuthKeyzApp.license("LICENSE-KEY-HERE");
    
    Console.WriteLine("Welcome, " + AuthKeyzApp.user_data.username);
}
```

### Python
```python
from authkeyz import api

AuthKeyzApp = api(
    name="YourAppName",
    ownerid="YourOwnerID",
    secret="YourAppSecret",
    version="1.0",
    hash_to_check=None
)

AuthKeyzApp.init()

# Authenticate with username/password or key
AuthKeyzApp.login("username", "password")
# AuthKeyzApp.license("LICENSE-KEY-HERE")

print(f"Logged in as: {AuthKeyzApp.user_data.username}")
print(f"Expires: {AuthKeyzApp.user_data.expires}")
```

### JavaScript / TypeScript
```javascript
import AuthKeyz from "./authkeyz.js";

const auth = new AuthKeyz(
  "YourAppName",
  "YourOwnerID",
  "YourAppSecret",
  "1.0"
);

await auth.init();
await auth.login("username", "password");

console.log("Logged in user:", auth.user_data.username);
```

---

## Security Features

### Replay Attack Protection
Every signed packet includes a server timestamp. Client SDKs verify:
1. `abs(current_unix_time - server_timestamp) <= 300` (rejects expired responses).
2. `timestamp + ":" + signature` is cached in a thread-safe set (`seen_signatures`). If an attacker intercepts and replays an identical packet within the 5-minute window, the SDK rejects it immediately with:
   `"Replay attack detected: duplicate signature rejected."`

### Anti-Host Redirection / Anti-Spoofing
SDKs perform proactive DNS queries on `authkeyz.bond` before initiating transport:
- If the resolved IP belongs to `127.0.0.1`, `::1`, or private network ranges (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`), the SDK detects that `C:\Windows\System32\drivers\etc\hosts` was manipulated to redirect API traffic to a local emulator, and terminates immediately.

---

## Web Documentation

For the full interactive REST API reference, seller API documentation, and code examples, visit:
👉 **[https://authkeyz.bond/docs](https://authkeyz.bond/docs)**
