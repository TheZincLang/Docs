# Zinc — Language Overview

| Field               | Value                                                                      |
|---------------------|----------------------------------------------------------------------------|
| Status              | v0 — minimal runnable subset under active development                      |
| Source extension    | `.zn`                                                                      |
| Paradigm            | compiled, statically typed, systems                                        |
| Compiler written in | TypeScript first, then self-hosted                                         |
| Target              | LLVM to x86/64 first, custom backend later                                 |
| Memory model        | TBD (v0 defers ownership/borrowing)                                        |

## Goals
- **Safety**: prevent common bugs through static checks and (later) runtime checks.
- **Performance**: generate efficient machine code comparable to C/C++.
- **Expressiveness**: support a range of programming paradigms without sacrificing performance.
- **Developer experience**: clear error messages, fast compilation, smooth workflow.

## v0 Scope

v0 is the minimal subset needed to get codegen running. Features not listed here are deferred.

### Supported
| Feature                              | Notes                                        |
|--------------------------------------|----------------------------------------------|
| Primitives (`int`, `float`, `bool`, `string`, `char`) | See `lang/types.md`          |
| `let` / `const`                      |                                              |
| All basic operators                  | Arithmetic, bitwise, logical, comparison, assignment |
| `if`/`else`, `while`, `for`, `loop`, `switch` | See `lang/statements.md`         |
| Functions (positional args only)     | See `lang/functions.md`                      |
| Structs (no field defaults)          | Fields + methods; see `lang/structs.md`      |
| Enums (basic tagged values)          | See `lang/enums.md`                          |
| Arrays (fixed or dynamic)            |                                              |
| `import`/`export` (basic)            | See `lang/modules.md`                        |

### Deferred (not in v0)
- Union types (`T | U`)
- Lambdas / closures
- Generics
- Interfaces
- Classes / inheritance
- Memory operators (`move`/`borrow`/`ref`)
- Mixins, `owns`/`serves`, groups
- Concurrency primitives (`async`/`await`, Mutex, Task, Promise)
- `throw`/`try`/`catch`
- `new`/`typeof`/`sizeof`/`delete`

## Implementation status

**Done:**
- Lexer (complete for v0 token set)
- Parser: variable declarations (`let`/`const`), enums, structs (fields + methods), functions, array literals, `loop`, `for` (C-style three-clause and `for`-in), all expression forms and operators, `if`/`else`, `while`, `switch`, `break`/`continue`/`return`, `import` statements (named / wildcard / aliased / type-only)

**Pending:**
- Type checker
- Code generation (LLVM backend)
