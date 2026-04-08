---
name: SOLID Principles
description: SOLID principles are five object-oriented design guidelines—Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, and Dependency Inversion—introduced by Robert C. Martin to make software more maintainable, flexible, and scalable. They promote loose coupling, reducing code rot, bugs, and technical debt as systems grow.
category: coding
---

# SOLID Principles

**Single Responsibility (SRP):** Each module, class, or function has exactly one reason to change. If you describe what a component does with "and," it needs splitting.

**Open/Closed (OCP):** Design modules to allow new behavior via extension (new classes, plugins, strategies) without modifying existing, tested code.

**Liskov Substitution (LSP):** Any subclass must be usable wherever its parent is expected without breaking correctness. Subtypes honor the contracts of their parent types.

**Interface Segregation (ISP):** No client should depend on methods it does not use. Prefer many small, focused interfaces over one large "god" interface.

**Dependency Inversion (DIP):** High-level modules depend on abstractions, not concrete implementations. Inject dependencies rather than hard-coding them.
