---
name: Idempotency
description: Design operations so they can be safely retried without unintended side effects -- essential for reliable distributed systems
category: design
---

# Idempotency

An operation is idempotent if performing it multiple times produces the same result as performing it once. In distributed systems, network failures make retries inevitable -- idempotency makes retries safe.

## The Core Problem

Three failure modes create ambiguity:

1. **Connection fails before the request reaches the server** -- safe to retry.
2. **Connection fails mid-operation** -- server state unknown.
3. **Operation succeeds but the response is lost** -- client doesn't know it worked.

Without idempotency, the client cannot distinguish these cases and cannot safely retry.

## Naturally Idempotent Operations

Some operations are idempotent by nature:

- **GET, HEAD, OPTIONS** -- read-only, no state change.
- **PUT** -- sets a resource to a specific state. Repeating the same PUT is a no-op.
- **DELETE** -- removing an already-deleted resource returns 404 or 204, not an error.

Design APIs to use these verbs where they fit. A `PUT /dns/records/subdomain` that sets state is inherently safer than a `POST /dns/records` that creates.

## Idempotency Keys for Non-Idempotent Operations

POST operations (charges, transfers, order creation) need explicit idempotency. The pattern:

1. **Client generates a unique key** (UUID) per logical operation and sends it via an `Idempotency-Key` header.
2. **Server on first request**: processes normally, stores the result keyed by the idempotency key.
3. **Server on retry (same key)**: returns the stored result without re-executing.

```
POST /v1/charges
Idempotency-Key: 4d7e8f2a-1b3c-...
Content-Type: application/json

{"amount": 2000, "currency": "usd", ...}
```

## Implementation Guidance

- **Wrap in ACID transactions** where possible -- if the operation fails mid-way, the DB rolls back and the retry starts clean.
- **Without transactions**, design operations to be resumable: detect partial state and continue rather than restart.
- **Expire idempotency keys** after a reasonable window (e.g., 24h). They are not a permanent dedup store.
- **Scope keys to the caller** -- combine the key with the authenticated identity to prevent cross-user collisions.

## Retry Strategy

- **Exponential backoff**: wait `2^n` seconds between retries (1s, 2s, 4s, 8s, ...).
- **Jitter**: add random variance to prevent thundering herd when many clients retry simultaneously.
- **Cap retries**: set a maximum (e.g., 5 attempts) and surface the failure to the caller.

## When to Apply

- Any mutation exposed over a network boundary (APIs, message handlers, webhooks).
- Background job processors -- jobs must tolerate being delivered more than once.
- Payment, billing, and inventory operations where duplication has real-world cost.
