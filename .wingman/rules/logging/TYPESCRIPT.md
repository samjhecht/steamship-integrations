---
name: TypeScript Logging Standards
description: TypeScript-specific logging, error handling, and observability standards
category: logging
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.mts"
  - "**/*.cts"
---

# TypeScript Logging Standards

## Logger Usage

```typescript
import { Logger } from "@/utils/logger";
const logger = new Logger({ component: "FileUploader" });

logger.info("Upload started", { fileName: file.name, sizeBytes: file.size });
```

Do not use `console.log`, `console.error`, or `console.warn` in application code. Use the Logger utility. Only exception: one-time bootstrap code before the logger is initialized.

## Error Hierarchy (Frontend)

| Class             | Use for                                  |
| ----------------- | ---------------------------------------- |
| `AppError`        | Base class                               |
| `ValidationError` | User input problems (always recoverable) |
| `NetworkError`    | HTTP/fetch failures                      |
| `ProcessingError` | Domain processing failures               |

Every error class carries both:

- `message` -- technical detail for logs
- `userMessage` -- safe, helpful text for display in the UI

## Error Display

- Error toasts show `error.userMessage` -- never stack traces or technical details.
- Error boundaries wrap the app root and major subtrees (editor, sidebar).
- Recoverable errors show a retry button and recovery suggestion.
- Non-recoverable errors show a "refresh page" suggestion.

## Anti-Patterns

| Anti-pattern                               | Fix                                                          |
| ------------------------------------------ | ------------------------------------------------------------ |
| `console.log("clicked")`                   | `logger.debug("button_clicked", { action })`                 |
| `catch (e) { /* ignore */ }`               | Log the error, show toast if user-facing                     |
| `toast(error.message)`                     | `toast(error.userMessage)`                                   |
| `throw new Error("fail")`                  | `throw new NetworkError("API timeout", { code: "TIMEOUT" })` |
| No error boundary around major UI sections | Wrap editor, sidebar, and other major subtrees               |
