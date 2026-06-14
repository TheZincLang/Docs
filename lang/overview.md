# Zinc — Language Overview

| Field               | Value                                                                      |
|---------------------|----------------------------------------------------------------------------|
| Status              | earliest development                                                       |
| Source extension    | `.zn`                                                                      |
| Paradigm            | compiled, statically typed, systems but full capabilities in all pardigms  |
| Compiler written in | TypeScript first since the similar syntax, then self hosted                |
| Target              | first LLVM to x84/64, then custom backend way later with full target range |
| Memory model        | rustic guaranteed memory safety and automatic freeing                      |

## Goals
- **Safety**: prevent common bugs like null dereference, buffer overflow, use-after-free, data races, etc. through a combination of static checks and runtime checks.
- **Performance**: generate efficient machine code with minimal overhead, comparable to C/C++.
- **Expressiveness**: support a wide range of programming paradigms (procedural, object-oriented, functional, etc.) and provide powerful and TypeScript like high level abstractions without sacrificing performance when using low level code.
- **Developer experience**: provide clear and helpful error messages, fast compilation times, and a smooth development workflow.

## Non-goals
- **Garbage collection**: Zinc will not include a garbage collector; instead, it will use a combination of ownership and borrowing rules (similar to Rust) for managing memory safely without GC overhead.
- **Scripüting language features**: While Zinc will support high-level abstractions and runtime dynamic features through function pointer replacement and a powerful lambda system, it will still be an AOT compiled language with a focus on performance and safety, rather than an interpreted and almost completely dynamic scripting language.

## Implementation status
Sync with `../Zinc/CLAUDE.md` § "Current implementation status".

**Done:** 
- basically nothing
  (joke, but also the actual state of the project, please correct when told to update the Docs based on the state of the compiler)

**Pending:** 
-basically everything
(same as above, but also the actual state of the project, please correct when told to update the Docs based on the state of the compiler)
