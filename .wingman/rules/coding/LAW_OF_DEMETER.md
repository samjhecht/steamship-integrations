---
name: Law of Demeter
description: The Law of Demeter, also known as "The Principle of Least Knowledge" is a principle for software design, particularly relevant in object orientated languages. It states that a unit of software should talk only to its immediate collaborators. An object A with a reference to object B can call its methods, but if B has a reference to object C, A should not call Cs methods. So, if C has a doThing() method, A should not invoke it directly; B.getC().doThis(). Following this principal limits the scope of changes, making them easier and safer in future.
category: coding
---

A given object should assume as little as possible about the structure or properties of anything else (including its subcomponents), in accordance with the principle of "information hiding". In accordance with the principle of least privilege, a module possess only the information and resources necessary for its legitimate purpose.

Accordantly, the following guidelines should be adhered to:

- Each unit should have only limited knowledge about other units: only units "closely" related to the current unit.
- Each unit should only talk to its friends; don't talk to strangers.
- Only talk to your immediate friends.
