---
name: Composition Over Inheritance
description: Prefer composing behavior from small, focused collaborators rather than building deep class hierarchies
category: coding
---

# Composition Over Inheritance

Favor composing objects from small, focused components over extending behavior through class hierarchies. Inheritance couples a subclass to its parent's implementation details; composition couples only to a public interface.

## Why Inheritance Breaks Down

- **Fragile base class**: Changes to a parent class ripple unpredictably into all descendants.
- **Rigid hierarchy**: Single-inheritance languages force "is-a" taxonomies that rarely survive evolving requirements. You end up with `ReadableWritableBufferedEncryptedStream`.
- **Implicit coupling**: Subclasses depend on parent internals -- overriding one method can break assumptions in another.
- **Reuse lock-in**: Behavior trapped in a class hierarchy cannot be reused outside that hierarchy without duplication.

## When to Use Composition

- The object "has" or "uses" a capability rather than "is" a specialization.
- You need to combine behaviors from multiple independent axes (logging + retry + auth).
- The set of behaviors may change at runtime or vary by configuration.
- You find yourself creating intermediate abstract classes just to share a few methods.

## When Inheritance Is Still Appropriate

- Modeling a genuinely fixed, shallow taxonomy (e.g., AST node types, a small set of error classes).
- Framework extension points explicitly designed for subclassing (e.g., `TestCase`).
- Keep hierarchies to 2 levels max (base + concrete). If you need a third level, reconsider the design.

## Applying It

```typescript
// BAD: Deep hierarchy to mix in behaviors
class Animal { ... }
class FlyingAnimal extends Animal { fly() { ... } }
class SwimmingFlyingAnimal extends FlyingAnimal { swim() { ... } }

// GOOD: Compose capabilities
interface Flyer { fly(): void; }
interface Swimmer { swim(): void; }

class Duck {
  constructor(
    private flyer: Flyer,
    private swimmer: Swimmer,
  ) {}
  fly() { this.flyer.fly(); }
  swim() { this.swimmer.swim(); }
}
```

```python
# BAD: Mixin soup
class LoggingMixin: ...
class RetryMixin: ...
class AuthMixin: ...
class MyClient(LoggingMixin, RetryMixin, AuthMixin, BaseClient): ...

# GOOD: Inject collaborators
class MyClient:
    def __init__(self, logger: Logger, retry: RetryPolicy, auth: AuthProvider):
        self.logger = logger
        self.retry = retry
        self.auth = auth
```

## Rule of Thumb

If you are about to type `extends` or add a mixin, ask: "Can I pass this behavior in as a dependency instead?" If yes, do that.
