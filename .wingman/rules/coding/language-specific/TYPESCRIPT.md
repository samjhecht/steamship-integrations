---
name: TypeScript Coding Standards
description: Language-specific coding standards for TypeScript projects
category: coding
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.mts"
  - "**/*.cts"
---

# TypeScript Coding Standards

- **JSDoc on exports**: Public functions get JSDoc comments explaining what they do.
- **NEVER use inline imports** - no `await import("./foo.js")`, no `import("pkg").Type` in type positions, no dynamic imports for types. Always use standard top-level imports.

## Type System Rules

- Never use `any`. Use `unknown` and narrow with type guards (`typeof`, `instanceof`, custom predicates).
- Check node_modules for external API type definitions instead of guessing
- Avoid type assertions (`as`). Prefer type annotations (`: Foo`) on object literals. If an assertion is unavoidable, add a comment justifying safety.
- Never use non-null assertions (`!`) without a comment explaining why null is impossible at that point.
- Never use `@ts-ignore` or `@ts-nocheck`. Use `@ts-expect-error` only in tests, with a comment explaining why.
- Enable strict mode in `tsconfig.json`: `strict: true`, `noImplicitAny: true`, `strictNullChecks: true`, `noUncheckedIndexedAccess: true`.
- Use discriminated unions with exhaustive checking. Tag union members with a literal `kind` or `type` field. Use a `never`-based assertion in the `default` case:
  ```typescript
  function assertNever(x: never): never {
    throw new Error(`Unexpected value: ${x}`);
  }
  ```
- Explicitly define return types on all exported/public functions. Internal functions may rely on inference.
- Never use `const enum` (invisible to JS consumers). Use plain `enum` or string literal unions.
- Use `Record<string, unknown>` or `object` for "any object" constraints. Never use `{}` (matches anything except `null`/`undefined`).
- Use branded types for domain primitives that share underlying types (e.g., `UserId` vs `OrderId`, both strings).
- **NEVER use `ReturnType<>`** — it obscures types behind indirection. Use the actual type name instead. Look up return types in source or `node_modules` type definitions and reference them directly.

  ```typescript
  // BAD: Indirection through ReturnType
  let timer: ReturnType<typeof setTimeout> | null = null;
  let stmt: ReturnType<Database["prepare"]>;
  let stat: Awaited<ReturnType<typeof fs.stat>>;

  // GOOD: Use the actual type
  let timer?: NodeJS.Timeout;
  let stmt: Statement;
  let stat: Stats;
  ```

  If a function's return type has no exported name, define a named type alias at the call site — don't use `ReturnType<>`.

## Value Mapping

Use `Record<UnionType, T>` lookup objects for mapping between variants and display
values, config, or behavior. Never use chained ternaries or switch statements for
static variant-to-value mappings. The Record enforces exhaustiveness at compile
time -- adding a new variant to the union produces a type error until a mapping is
added.

## Variables, Functions, and Control Flow

- Always use `const` by default. Use `let` only when reassignment is needed. Never use `var`.
- One declaration per statement. No `let a = 1, b = 2`.
- Always use `===` and `!==`. Exception: `foo == null` to check both `null` and `undefined`.
- Always brace control flow bodies, even single statements.
- Use arrow functions for callbacks. Use function declarations for named top-level functions.
- Use ES module syntax exclusively. Never use `require()`.
- Use `import type` for type-only imports. Prefer named exports over default exports.
- Only throw `Error` instances or subclasses. Never throw strings or plain objects.
- Catch with `e: unknown` and narrow before handling.

## Naming Conventions

| Element                                   | Convention                       | Example           |
| ----------------------------------------- | -------------------------------- | ----------------- |
| Classes, interfaces, types, enums         | `PascalCase`                     | `UserService`     |
| Variables, parameters, functions, methods | `camelCase`                      | `getUserById`     |
| Global constants, enum values             | `CONSTANT_CASE`                  | `MAX_RETRIES`     |
| Files                                     | `kebab-case`                     | `user-service.ts` |
| Type parameters                           | Single uppercase or `PascalCase` | `T`, `TResult`    |

- Never prefix interfaces with `I` (e.g., `IUser`).
- Never use leading/trailing underscores for private members.
- Use whole words. Treat abbreviations as words: `loadHttpUrl`, not `loadHTTPURL`.

### Error Handling

- Raise explicit errors for unsupported inputs/parameters — prevents silent failures and makes contract violations obvious — Explicit failures expose bugs immediately instead of allowing invalid data to propagate through the system silently
- Catch specific exception types, not broad Exception — identifies actual failure modes and prevents masking unexpected errors — Broad exception handlers hide bugs by catching unexpected errors (like KeyboardInterrupt or SystemExit) and make debugging harder by obscuring the actual failure type

## Disallowed Patterns

- Never use `eval()` or `Function(...string)` constructor.
- Never use `with`.
- Never modify builtin prototypes.
- Never use wrapper constructors (`new String()`, `new Boolean()`, `new Number()`).
- Never commit `debugger` statements.
- Always terminate statements with `;`. Do not rely on ASI.
- No magic strings or numbers. Extract to named constants.

## ESLint Configuration

- Use `typescript-eslint` with the `strict-type-checked` config as baseline.
- Run ESLint in pre-commit hooks. Zero linting errors in CI.
- TypeScript must compile with zero errors under strict mode.

## Code Quality

- Functions do one thing. If a function needs an "and" in its description, split it.
- Prefer pure functions where possible. Isolate side effects.
- No reinventing established patterns; use ecosystem libraries.
- Prefer composition over inheritance.

- **Use `Promise.withResolvers()`** instead of `new Promise((resolve, reject) => ...)` — cleaner, avoids callback nesting, and the resolver functions are properly typed:

  ```typescript
  // BAD: Verbose, callback nesting
  const promise = new Promise<string>((resolve, reject) => { ... });

  // GOOD: Clean destructuring, typed resolvers
  const { promise, resolve, reject } = Promise.withResolvers<string>();
  ```

- **NEVER create multiple handles to the same path**:

```typescript
// BAD: Creates two file handles
if (await Bun.file(path).exists()) {
  const content = await Bun.file(path).text();
}

// BAD: Still wasteful even in separate functions
async function checkConfig() {
  return await Bun.file(configPath).exists();
}
async function loadConfig() {
  return await Bun.file(configPath).json(); // second handle
}
```

## Symlink Creation

Always use `path.relative()` when creating symlinks programmatically. Never pass absolute paths to `fs.symlinkSync` or `fs.symlink`:

```typescript
import { relative, dirname } from "path";

// GOOD -- relative symlink, portable across machines
const target = relative(dirname(linkPath), actualPath);
fs.symlinkSync(target, linkPath);

// BAD -- absolute symlink, breaks on any other machine
fs.symlinkSync(actualPath, linkPath);
```
