---
name: Command Query Separation
description: Every method should either change state (command) or return data (query), never both
category: design
---

# Command Query Separation (CQS)

Originated by Bertrand Meyer. The principle: every method should be either a **command** that changes state and returns nothing, or a **query** that returns data and has no side effects. Never both.

## Why It Matters

- **Reasoning**: Queries are safe to call anywhere -- in assertions, logs, debugging, retries -- because they don't change anything. Commands are where all mutation is concentrated.
- **Testability**: Queries can be called repeatedly with identical results. Commands can be tested by calling the command, then querying to verify the new state.
- **Composability**: Side-effect-free queries can be freely reordered, cached, or parallelized.

## Applying It

```typescript
// BAD: mutates and returns -- caller can't query without side effects
function addItemAndGetTotal(cart: Cart, item: Item): number {
  cart.items.push(item);
  return cart.items.reduce((sum, i) => sum + i.price, 0);
}

// GOOD: separate command and query
function addItem(cart: Cart, item: Item): void {
  cart.items.push(item);
}
function getTotal(cart: Cart): number {
  return cart.items.reduce((sum, i) => sum + i.price, 0);
}
```

```python
# BAD: save() returns the saved object with generated fields
def save(self) -> User:
    self.db.insert(self.data)
    return self.db.get(self.data.id)

# GOOD: command saves, query retrieves
def save(self) -> None:
    self.db.insert(self.data)

def get(self, user_id: str) -> User:
    return self.db.get(user_id)
```

## Pragmatic Exceptions

Some operations are inherently both -- `stack.pop()`, `queue.dequeue()`, `iterator.next()`, `Map.set()` returning the previous value. These are acceptable when:

- Splitting into two calls would create race conditions or TOCTOU bugs (concurrent data structures).
- The combined operation is an atomic unit with a long-established, universally understood contract.

When you do combine, document clearly that the method both mutates and returns.

## Relationship to CQRS

CQRS (Command Query Responsibility Segregation) is the architectural scaling of this principle -- separate read and write models/stores. CQS is the method-level discipline that makes CQRS natural to adopt later.
