---
name: Function Length
description: Functions should be less than 50 lines
category: coding
---

Avoid functions longer than 50 lines of actual code:

1. **Count Code Lines**: Exclude blank lines and comment-only lines
2. **Function Body**: Measure from opening brace to closing brace
3. **All Function Types**: Methods, closures, lambdas, standalone functions

## Exceptions

- Test functions with many assertions
- Generated code
- Functions that are mostly configuration/data (e.g., builder patterns with many options)
- Initialization functions that set many fields
