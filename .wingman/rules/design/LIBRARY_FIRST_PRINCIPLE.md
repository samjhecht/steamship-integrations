---
name: Library First Principle
description: Every new subcomponent/feature must begin as a standalone library
category: design
---

Every new subcomponent/feature must begin as a standalone library. This forces modular design from the start. No feature shall be implemented directly within application code without first being abstracted into a reusable library component. This principle ensures that specifications generate modular, reusable code rather than monolithic applications. When the LLM generates an implementation plan, it must structure features as libraries with clear boundaries and minimal dependencies.