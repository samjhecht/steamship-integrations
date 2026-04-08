---
name: Backing Service Abstraction
description: Treat all backing services as attached resources addressable by URL or connection config
category: infrastructure
---

# Backing Service Abstraction

- Treat all backing services (databases, caches, queues, email, object storage, external APIs) as attached resources addressable by URL or connection config
- Make no code distinction between local and third-party services -- swapping a local Postgres for RDS, or Postfix for SendGrid, should require only a config change
- Service bindings should be config, not code -- a deploy attaches resources, code consumes them through an abstract interface
