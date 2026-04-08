---
name: Parse, Don't Validate
description: Convert unstructured input into typed, constrained data at the boundary -- downstream code should never re-check what the parser already proved
category: coding
---

# Parse, Don't Validate

Parsing and validation both check input, but parsing *preserves what it learned* by producing a more precise type. Validation checks a condition and discards the result.

```typescript
// VALIDATION: checks then throws away the knowledge
function validateNonEmpty(items: string[]): void {
  if (items.length === 0) throw new Error("empty");
}
// Every caller downstream must still handle the empty case

// PARSING: checks and encodes the guarantee in the return type
function parseNonEmpty(items: string[]): [string, ...string[]] {
  if (items.length === 0) throw new Error("empty");
  return items as [string, ...string[]];
}
// Downstream code receives a type that cannot be empty
```

## Why This Matters

- **Eliminates redundant checks.** Once data is parsed into a constrained type, no downstream function needs to re-validate the same invariant.
- **Catches drift.** If the boundary parsing changes, the type changes, and the compiler flags every affected call site. Scattered validation silently goes stale.
- **Prevents shotgun parsing.** Mixing validation with processing logic means a failure discovered mid-operation may require rolling back work already done. Parsing up front creates a clean separation: parse (where failures happen) then process (where the data is known-good).

## How to Apply

1. **At every system boundary** (API handler, file reader, CLI parser, message consumer), parse raw input into a domain type. Do not pass raw `any`, `dict`, `Record<string, unknown>`, or untyped JSON deeper than the boundary layer.

2. **Strengthen argument types, not return types.** Instead of `head(list) -> T | null`, require `head(nonEmptyList) -> T`. Push the burden of proof to the caller who has the context to provide it.

3. **Suspect functions that return void/unit after checking input.** A function that validates but returns nothing is likely discarding information that should be captured in a return type.

4. **Use smart constructors for invariants the type system can't express natively.** Wrap primitives in opaque types with factory functions that enforce the constraint:

```typescript
// Opaque branded type
type EmailAddress = string & { readonly __brand: "EmailAddress" };

function parseEmail(raw: string): EmailAddress {
  if (!EMAIL_REGEX.test(raw)) throw new ValidationError("Invalid email");
  return raw as EmailAddress;
}
```

```python
# Python: dataclass with __init__ validation
@dataclass(frozen=True)
class PositiveInt:
    value: int
    def __post_init__(self):
        if self.value <= 0:
            raise ValueError(f"Expected positive int, got {self.value}")
```

## Relationship to Other Rules

- **Make Illegal States Unrepresentable** focuses on *modeling* -- designing types so invalid states can't exist. Parse Don't Validate focuses on *where and how* raw data enters those types.
- **Command Query Separation** aligns naturally -- parsers are queries (return typed data, no side effects on success).
