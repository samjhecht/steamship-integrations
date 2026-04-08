---
name: Disposability
description: Processes must start fast and shut down gracefully on SIGTERM
category: coding
---

# Disposability

- Processes MUST start fast (seconds, not minutes) and shut down gracefully on SIGTERM
- On shutdown: stop accepting new work, finish in-flight requests/jobs, then exit
- Worker processes MUST return incomplete jobs to the queue -- design jobs to be reentrant or idempotent
- Design for robustness against sudden death (hardware failure, OOM kill) -- do not rely on processes running indefinitely
