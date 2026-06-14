# Memory Model

Zinc uses keyword-based ownership operators instead of sigil syntax (`&`, `*`).
Ownership/borrow/ref status is encoded directly in the type — there are no separate lifetime annotations.

## Ownership operators
Used as prefix operators on expressions, similar to `await` or `new` in TypeScript.

| Keyword         | Meaning | Resulting type |
|-----------------|---------|----------------|
| `move`          | Transfer ownership to the receiver; original is invalidated | `T` (owned) |
| `copy`          | Copy the value; both original and copy are valid owned values | `T` (owned) |
| `borrow` / `bor` | Mutable borrow — caller retains ownership, callee may modify | `borrow T` |
| `ref` / `reference` | Read-only reference — caller retains ownership, callee cannot modify | `ref T` |

```zn
foo(move x)       // x is no longer valid after this
foo(copy x)       // x is still valid; foo gets an independent copy
foo(borrow x)     // foo can mutate x; x is still owned by caller
foo(ref x)        // foo can only read x
```

## Ownership as part of the type
`borrow T` and `ref T` are distinct types from `T`. A function that takes `borrow i32` cannot be called with a bare `i32` without an explicit `borrow` operator, and vice versa. This means the compiler always knows statically what ownership state any value is in.

## Lifetime inference
Lifetimes are inferred entirely by the compiler — no annotations ever appear in source code.

The inference is **inside-out**: each function computes what effect it has on the lifetimes of its inputs and outputs, then that effect is propagated ("inlined") up to the caller. This recurses up the full call chain. The result is equivalent to having explicit lifetime contracts at every call site, but without ever writing them.

Rules the inference relies on:
- A `borrow` or `ref` cannot outlive the value it points to
- When a closure captures a variable: if the captured variable outlives the closure → captured as `ref`; if the closure outlives the variable → captured by `copy`
- Lambdas (pure function pointers, no capture) are a black box — their inputs' ownership types are unchanged by the call, outputs are always owned

## Raw pointers
Raw pointers (`[FILL: syntax]`) exist for bare-metal use cases where a specific memory address must be targeted directly. They bypass the ownership system entirely and should not be used in normal code.

## Brass format
Compiled modules (`.brass`) carry the lifetime effect metadata for every exported function alongside the machine code. This allows the lifetime inference to work across module boundaries without requiring source or annotations — importing a brass file gives the compiler everything it needs.
