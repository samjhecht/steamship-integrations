---
name: Make Illegal States Unrepresentable
description: Model data so that invalid combinations cannot be expressed in the type system, shifting error detection from runtime to compile-time
category: coding
---

# Make Illegal States Unrepresentable

Design types so that every possible value is a valid value. If a state combination is nonsensical, restructure the model so it cannot be constructed.

## Core Technique: Sum Types Over Optional Fields

When an entity has mutually exclusive states with different associated data, use discriminated unions -- not a flat record with optional fields.

```typescript
// BAD: allows illegal combos (e.g. status "pending" with a completedAt date)
interface Task {
  status: "pending" | "in_progress" | "completed" | "failed";
  assignedTo?: string;
  completedAt?: Date;
  error?: string;
}

// GOOD: each state carries exactly the data it needs
type Task =
  | { status: "pending" }
  | { status: "in_progress"; assignedTo: string }
  | { status: "completed"; assignedTo: string; completedAt: Date }
  | { status: "failed"; error: string };
```

## When to Apply

- State machines with distinct phases carrying different data
- API responses that vary by status or type
- Configuration objects where certain fields are only valid in combination
- Any record where you find yourself writing "field X is only valid when field Y is Z" in comments or docs

## Parse, Don't Validate

Validate raw input once at the boundary (API handler, file reader, CLI parser) and convert it into a constrained type. Downstream code then works with the parsed type and never needs to re-check invariants.

## In Dynamic Languages

Without a static type system, enforce constraints via smart constructors -- factory functions that assert invariants at creation time and return opaque objects. Never construct state objects with raw dict/object literals scattered through the codebase.
