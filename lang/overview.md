# Zinc — Language Overview

| Field               | Value                                                                      |
|---------------------|----------------------------------------------------------------------------|
| Status              | earliest development                                                       |
| Source extension    | `.zn`                                                                      |
| Paradigm            | compiled, statically typed, systems but full capabilities in all paradigms |
| Compiler written in | TypeScript first since the similar syntax, then self hosted                |
| Target              | first LLVM to x86/64, then custom backend way later with full target range |
| Memory model        | guaranteed memory safety and automatic freeing, no GC                      |

## Goals
- **Safety**: prevent common bugs like null dereference, buffer overflow, use-after-free, data races, etc. through a combination of static checks and runtime checks.
- **Performance**: generate efficient machine code with minimal overhead, comparable to C/C++.
- **Expressiveness**: support a wide range of programming paradigms (procedural, object-oriented, functional, etc.) and provide powerful TypeScript-like high-level abstractions without sacrificing performance when using low-level code.
- **Developer experience**: provide clear and helpful error messages, fast compilation times, and a smooth development workflow.

## Design philosophy
Zinc does not force a trade-off between high-level and low-level code. The rule is: write in whatever style suits the problem, then drop to lower-level primitives only where it actually matters.

- In the hot path: use `move`/`borrow`/`ref`, manual memory control, raw types
- Everywhere else: use TypeScript-like abstractions, first-class networking primitives, union types, closures — without importing anything special or paying a runtime penalty

The memory model reflects this: a plain `=` is a copy and the ownership system is entirely opt-in. Most programs never need to think about it.

## Multi-paradigm capabilities
| Capability                   | Notes                                                        |
|------------------------------|--------------------------------------------------------------|
| Procedural                   | core                                                         |
| Object-oriented              | [FILL]                                                       |
| Functional                   | lambdas, closures, first-class functions                     |
| Networking                   | import-less, built-in primitives (TypeScript/Deno-style API) |
| TypeScript-like abstractions | union types, type narrowing, string templates, etc.          |

## Non-goals
- **Garbage collection**: Zinc will not include a garbage collector; instead, it uses ownership and borrowing for memory safety without GC overhead.
- **Scripting language features**: Zinc supports high-level abstractions and runtime dynamic features through lambdas and closures, but it is an AOT compiled language focused on performance and safety — not an interpreted or fully dynamic scripting language.

## Implementation status
Sync with the compiler repo's `CLAUDE.md` § "Current implementation status".

**Done:**
- Lexer (complete or near-complete)
- Parser: variable declarations (`let`/`const`), enums, structs, classes (with `extends`/`implements`/`owns`/`serves` clauses and member modifiers), interfaces (field + method signatures), groups (bulk clause targets), functions (`fn`/`func`/`function`), lambdas/closures, array literals, `loop`, `for` (C-style three-clause and `for`-in), all expression forms and operators, union types (`T | U`), generics (type-parameter declarations at binding sites + generic type arguments in annotations), `if`/`else`, `while`, `switch`, `break`/`continue`/`return`, `import` statements (named / wildcard / aliased / type-only)

**Pending:**
- Type checker (incl. interface conformance, group expansion, union narrowing, generic arity/constraints)
- Lifetime inference
- Code generation (LLVM backend)
