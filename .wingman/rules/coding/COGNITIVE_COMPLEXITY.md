---
name: Cognitive Complexity
description: Limit cognitive complexity of functions
category: coding
---

Avoid functions with high cognitive complexity:

1. **Deep Nesting**: Conditions nested more than 3 levels deep (4+ is a flag)
2. **Many Branches**: Functions with numerous if/else, switch, or match branches
3. **Complex Boolean Logic**: Conditions with multiple AND/OR operators
4. **Nested Loops**: Loops inside conditionals or other loops
5. **Long Conditional Chains**: Extended if/else if/else chains

## Exceptions

- Test functions with multiple assertions
- Generated code or macro expansions
- Simple match/switch statements with many variants but simple bodies
- Configuration parsing with many options
