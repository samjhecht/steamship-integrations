---
name: TypeScript Path Aliases
description: Require @/ path aliases for cross-directory imports instead of relative paths
category: coding
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.mts"
  - "**/*.cts"
---

# TypeScript Path Aliases

Use `@/` path aliases for all cross-directory imports. Never use relative paths that traverse up (`../`).

```typescript
// GOOD: Path alias
import { UserService } from "@/services/user-service";
import type { Config } from "@/config/schema";

// BAD: Relative traversal
import { UserService } from "../../services/user-service";
import type { Config } from "../../../config/schema";
```

Same-directory and child-directory relative imports are fine:

```typescript
// OK: Same directory
import { helper } from "./helper";

// OK: Child directory
import { schema } from "./schemas/user";
```

This rule requires `paths` configuration in `tsconfig.json`:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

Bundlers and test runners (Vite, Jest, etc.) may need equivalent alias configuration.
