# Lambdas & Closures
Zinc distinguishes between two related but distinct concepts.

## Lambda
A lambda is a **function pointer variable** — a value that holds the address of a function and can be called, copied, and overwritten like any other variable. It carries no captured environment.

```zn
[FILL: lambda literal / assignment syntax]
let double: [FILL: lambda type syntax] = [FILL]
double(4) // jumps to address stored in double
```

### Lifetime contract
Lambdas are a **black box for lifetime inference**:
- Ownership/borrow/ref status is encoded in the parameter types themselves — the lambda cannot change it
- Outputs are always owned values
- Calling a lambda has no lifetime side-effects on its inputs beyond what the types already describe

This means the inference system never needs to inspect a lambda's body to track lifetimes at the call site.

## Closure
A closure is a function that **captures variables from its enclosing scope**. Capture mode is determined automatically by lifetime inference — no manual annotation required:

| Situation | Capture mode |
|---|---|
| Captured variable outlives the closure | borrow (reference) |
| Closure outlives the captured variable | copy |

```zn
[FILL: closure syntax]
```

## Comparison
| | Lambda | Closure |
|---|---|---|
| Captured environment | none | yes (auto copy or borrow) |
| Lifetime inference role | black box | inlined like any other borrow/copy |
| Callable | yes | yes |
| Copyable | yes | [FILL] |
| Overwritable | yes | [FILL] |

## Status
[FILL: planned / in progress / implemented]
