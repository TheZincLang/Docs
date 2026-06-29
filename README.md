# Zinc Docs

Language and compiler reference documentation for **Zinc**, a compiled,
statically typed programming language built from scratch. This is a separate
repository from the compiler itself, which lives in the companion
[Zinc](../Zinc) repo.

The docs are filled in incrementally as the language and compiler take shape, so
some sections are still templates (see [Conventions](#conventions) below).

## What is Zinc?

Zinc targets the safety and performance of a systems language while keeping
TypeScript-like ergonomics. A plain `=` is a copy and the ownership system is
opt-in, so most code never has to think about memory; in the hot path you reach
for `move` / `borrow` / `ref` and manual control. There is no garbage collector
— memory safety comes from ownership, borrowing, and compiler-inferred
lifetimes. See [`lang/overview.md`](lang/overview.md) for the full picture.

## Contents

### Language (`lang/`)

| Topic                                                 | File                                     |
|-------------------------------------------------------|------------------------------------------|
| Language overview, goals, paradigm, status            | [`lang/overview.md`](lang/overview.md)   |
| Token types, literals, keywords, string templates     | [`lang/tokens.md`](lang/tokens.md)       |
| Type system, `TypeKind` enum, rules                   | [`lang/types.md`](lang/types.md)         |
| Operators, precedence table, semantics                | [`lang/operators.md`](lang/operators.md) |
| Statement grammar (all statement forms)               | [`lang/statements.md`](lang/statements.md) |
| Function declaration and call syntax                  | [`lang/functions.md`](lang/functions.md) |
| Lambdas, closures, capture semantics                  | [`lang/lambdas.md`](lang/lambdas.md)     |
| Memory model, ownership operators, lifetime inference | [`lang/memory.md`](lang/memory.md)       |
| Import/export, module resolution                      | [`lang/modules.md`](lang/modules.md)     |
| Enum syntax and AST representation                    | [`lang/enums.md`](lang/enums.md)         |
| Struct syntax, members, and AST nodes                 | [`lang/structs.md`](lang/structs.md)     |
| Class syntax, COOP inheritance, modifiers, AST nodes  | [`lang/classes.md`](lang/classes.md)     |

### Compiler (`compiler/`)

| Topic                                  | File                                         |
|----------------------------------------|----------------------------------------------|
| Pipeline stages and orchestration      | [`compiler/pipeline.md`](compiler/pipeline.md) |
| Compiler error codes and messages      | [`compiler/errors.md`](compiler/errors.md)   |

### Examples (`examples/`)

| Topic                       | File                                           |
|-----------------------------|------------------------------------------------|
| Annotated `.zn` code examples | [`examples/snippets.md`](examples/snippets.md) |

## Conventions

- `[FILL]` — unfilled placeholder; replace with the actual value, then remove
  the marker.
- `[UNDEC]` — undecided / open design question; discuss and decide, then replace
  with the value and remove the marker.
- Grammar notation: `<rule>` non-terminal · `[x]` optional · `{x}`
  zero-or-more · `|` alternation.
- Tables are preferred over prose; no decorative content.
- Keep implementation-status fields in sync with
  [`../Zinc/CLAUDE.md`](../Zinc/CLAUDE.md) § "Current implementation status".

`CLAUDE.md` at the repo root is the agent index — it maps each topic to its file
so automated tools can fetch exactly what they need.

## License

See [LICENSE](LICENSE).
