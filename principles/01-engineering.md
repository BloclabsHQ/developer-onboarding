# Engineering Principles

## The Priority Hierarchy

```
Correctness > Clarity > Conciseness > Performance
```

Never sacrifice a higher priority for a lower one. Premature optimization is sacrificing correctness for performance nobody measured. Clever code is sacrificing clarity for conciseness. Correct and ugly beats elegant and wrong every single time.

## Core Principles

### Complexity is the enemy
Every line of code is a liability. Every abstraction has a cost. Simplicity is a feature you actively maintain, not a default you start with. If you cannot explain a design in one sentence, simplify until you can.

### The error path IS the main path
92% of catastrophic failures in distributed systems come from incorrect handling of non-fatal errors. For every `try`, write the `catch` first. The error path is what 80% of your users will hit in the real world -- bad networks, stale caches, API changes, timeouts, missing permissions. Handle failure explicitly.

### Data drives design
Start with what flows through the system, not what objects you want to model. Define types before behavior. Types ARE documentation. If you understand the data, the architecture reveals itself.

### Cognitive load is finite
The human brain holds roughly 5 items in working memory. Design for that constraint at every level:

- Directories in a folder: aim for ~5
- Files in a package: aim for ~5
- Functions in a file: aim for ~5
- Parameters in a function: aim for ~5
- Nesting depth: maximum 3

You cannot increase working memory capacity. You can only decrease what your code demands of it.

### Understanding > memorization
Know *why*, not just *how*. If you cannot explain why a pattern exists, you cannot know when to break it. Every decision should be traceable to a reason. "We've always done it this way" is not a reason.

### Value types by default
Default to value semantics (structs, records, data classes). 92% of concurrency bugs come from shared mutable state. Value types eliminate the entire category. Reach for reference types only when identity semantics, inheritance, or explicitly shared state is required.

### Earn every resource
Do not spawn workers, add concurrency, create caches, or introduce complexity without measured evidence it is needed. Profile first. The right question is not "will this be faster?" but "is this actually slow?"

## Anti-Patterns to Reject

- **Premature optimization**: Optimizing code that is not proven correct
- **Clever code**: Sacrificing readability for brevity
- **Copy-paste shortcuts**: Sacrificing correctness for delivery speed
- **Log-and-continue**: Swallowing errors with a log line instead of handling them
- **Stringly typed**: Using strings where enums, types, or constants belong
- **God objects**: Classes that know everything and do everything
- **Speculative generality**: Building abstractions for hypothetical future requirements
