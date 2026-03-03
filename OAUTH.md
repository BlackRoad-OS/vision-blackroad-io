# 🔑 OAuth 2.0 — BlackRoad OS Authentication

**© 2025–2026 BlackRoad OS, Inc. All Rights Reserved.**

---

## Overview

All BlackRoad OS APIs use **OAuth 2.0** with **Bearer tokens** for authentication. This document covers how to obtain and use tokens to interact with BlackRoad services — including `vision-blackroad-io`.

> ⚠️ Third-party AI providers (OpenAI, Anthropic, GitHub Copilot, etc.) are **not authorized** to route through BlackRoad infrastructure. All traffic must originate from verified BlackRoad applications using a valid Converter API key.

---

## 🔐 Authentication Flow

```
Client → BlackRoad Auth Endpoint → Bearer Token → BlackRoad API
```

### Step 1: Obtain a Converter API Key

Contact [blackroad.systems@gmail.com](mailto:blackroad.systems@gmail.com) to request a Converter API key. Only approved contributors receive access (see [CONTRIBUTING.md](CONTRIBUTING.md)).

### Step 2: Request a Bearer Token

```bash
curl -X POST https://auth.blackroad.io/oauth/token \
  -H "Content-Type: application/json" \
  -d '{
    "grant_type": "client_credentials",
    "client_id": "<YOUR_CONVERTER_API_KEY>",
    "client_secret": "<YOUR_SECRET>",
    "scope": "vision:read vision:write"
  }'
```

**Response:**
```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "vision:read vision:write"
}
```

### Step 3: Use the Token

```bash
curl -H "Authorization: Bearer <ACCESS_TOKEN>" \
     https://api.blackroad.io/vision/analyze
```

---

## 🛡️ Scopes

| Scope | Description |
|-------|-------------|
| `vision:read` | Read-only access to vision analysis results |
| `vision:write` | Submit images/video for analysis |
| `admin:*` | Full administrative access (restricted) |

---

## 🔒 Security Requirements

- All tokens expire after **1 hour**. Implement token refresh in your client.
- Never expose your `client_secret` in frontend code or public repositories.
- All requests must use **HTTPS**. HTTP requests are rejected.
- Tokens are scoped — do not request broader scopes than necessary.

---

## 🌐 Cloudflare & Tailscale Routing

BlackRoad traffic routes exclusively through:

1. **Cloudflare** — edge delivery and DDoS protection
2. **BlackRoad-owned infrastructure** — authenticated via Converter API

No external AI providers route through BlackRoad's Tailscale mesh or internal Pi cluster. BlackRoad traffic flows:

```
Authorized Client → Cloudflare Edge → BlackRoad API Servers
```

Not:
```
Third-party AI (OpenAI / Anthropic / Copilot) → BlackRoad infrastructure
```

---

## 🔄 Token Refresh

```bash
curl -X POST https://auth.blackroad.io/oauth/token \
  -H "Content-Type: application/json" \
  -d '{
    "grant_type": "refresh_token",
    "refresh_token": "<YOUR_REFRESH_TOKEN>",
    "client_id": "<YOUR_CONVERTER_API_KEY>"
  }'
```

---

## ❌ Unauthorized Access

Requests without a valid token receive:

```json
{
  "error": "unauthorized",
  "message": "A valid BlackRoad Converter API key is required to access this service.",
  "docs": "https://docs.blackroad.io/oauth"
}
```

---

## 📧 Support

**Email:** blackroad.systems@gmail.com
**Docs:** [docs.blackroad.io/oauth](https://docs.blackroad.io/oauth)

---

**© 2025–2026 BlackRoad OS, Inc. All Rights Reserved.**
