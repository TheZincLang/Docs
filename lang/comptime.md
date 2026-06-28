# Comptime
Status: **unimplemented** — design complete, not yet in compiler

Zinc's comptime model rests on three principles:

1. **Types are values.** A type is an enum value at both compile time and runtime,
   so types can drive logic in either stage.
2. **Pure functions run at comptime.** Any pure function may be evaluated at
   comptime — see [§ Pure functions](#pure-functions).
3. **Definitions are values.** Comptime code can produce new definitions of any
   kind and treat existing definitions as ordinary values.

## Trigger: implicit from position

There is no `comptime` keyword. An expression is evaluated at comptime when it
appears **in a position that demands a definite value inside a definition**.

| Position                       | Example                                          |
|--------------------------------|--------------------------------------------------|
| Struct / class field type      | `size: computeSize()`                            |
| Struct / class field default   | `count: defaultCount()`                          |
| Function default parameter     | `fn foo(x: int = baseVal())`                     |
| Module-level `const`           | `const MAX: int = computeMax()`                  |
| Type alias RHS                 | `type Grid = makeGrid()`                         |
| Enum variant value             | `A = computeA()`                                 |

Everything else — expressions inside a function body, `let` bindings in statement
position — is runtime by default.

Because the trigger is positional, there is no staged-computation syntax and no
explicit comptime block. If an expression is where a definite value must appear in
a definition, it runs at comptime; otherwise it does not.

## Pure functions

A function is **pure** if it has no side effects. Purity is what makes a function
callable in a comptime position.

### Inferred purity

Purity is inferred automatically. The compiler inspects the function body; if it
finds no impure operations, the function is eligible for comptime use. No
annotation is required.

Calling a function the compiler cannot verify as pure from a comptime position is
a compile error.

### Enforced purity (`pure`)

The `pure` modifier, placed before the `fn`/`func`/`function` keyword, converts
purity from an inferred property to a **compiler-enforced contract**. Any impure
operation in the body becomes a compile error regardless of whether the function
is called from a comptime position.

```zn
pure fn computeMax(values: int[]): int {
  // IO here is a compile error even if nobody calls this at comptime
  let m = values[0]
  for (let i = 1; i < values.length; i++) {
    if (values[i] > m) { m = values[i] }
  }
  return m
}
```

`pure` is a regular modifier and participates in the same modifier mechanism as
`async` and `export`.

| Mode      | Annotation  | When the compiler errors on impure ops                   |
|-----------|-------------|----------------------------------------------------------|
| Inferred  | *(none)*    | Only when the function is called from a comptime position |
| Enforced  | `pure fn`   | Always, regardless of call site                           |

### What is and is not allowed at comptime

| Allowed                                | Disallowed (side effects)       |
|----------------------------------------|---------------------------------|
| Arithmetic and logic                   | IO (files, network, stdout)     |
| Allocation                             | Reading the clock               |
| Constructing values and types          | RNG / reading machine state     |
| Calling other pure functions           | Calling impure functions        |
| Producing new definitions              |                                 |

## Types as values

A type in Zinc is an enum value — specifically an instance of the built-in `Type`
enum. The `typeof` operator returns this value.

```zn
let t: Type = typeof val   // e.g. Type.Int, Type.String, ...
```

Type values support comparison and `switch`:

```zn
switch (typeof val) {
  case int:    { ... }
  case string: { ... }
  default:     { ... }
}
```

`any` is the one case where type information travels with a value at runtime: an
`any` value is a pointer to a heap-allocated `{ value, Type }` struct. All other
types carry no runtime tag — the type is known statically and erased.

| Aspect                     | Behavior                                            |
|----------------------------|-----------------------------------------------------|
| `typeof expr` return type  | `Type` (built-in enum)                              |
| Comparison form            | `typeof val == int` — not a string                  |
| `any` runtime layout       | pointer to heap struct `{ value: any, type: Type }` |
| Non-`any` runtime type tag | none — statically known, erased                     |
| Avoiding `any` overhead    | use generics instead                                |

[UNDEC: exact variant names on the `Type` enum — whether they match the spelling
of the type keyword (`int`, `string`, …) or a capitalised form (`Int`, `String`, …)]

## Definitions as values

Comptime code can produce any kind of definition: structs, classes, enums,
functions, constants, type aliases, or methods added to an existing type.

All produced definitions are visible to the rest of the compilation unit after the
comptime pass completes.

**Staging rule.** Comptime code is compiled using the definitions as they exist at
the start of the comptime pass. A piece of comptime code that modifies a definition
cannot observe its own modification during the same pass. The modified definition
is visible to runtime code and to any later comptime passes.

| What comptime can produce          | Notes                                    |
|------------------------------------|------------------------------------------|
| New struct / class / enum          | Registered like any top-level definition |
| New function                       | Callable from runtime code               |
| New module-level `const`           |                                          |
| New type alias                     |                                          |
| Methods added to an existing type  | [UNDEC: exact augmentation syntax]       |

Comptime expressions within a single definition body are evaluated in source order.

## Generics and comptime

Generic type parameters (`<T>`) are not comptime parameters. A generic is resolved
by one of two strategies depending on what the compiler can prove at the use site:

- **Monomorphisation** — when the type argument is statically known, the compiler
  emits a dedicated concrete version (equivalent to C++ template instantiation).
- **Jump table** — when the type argument is not statically known, the compiler
  emits a single generic version that dispatches through a vtable / jump table at
  runtime.

The same `<T>` syntax covers both cases; the compiler chooses the strategy. This
means there is no separate "comptime parameter" kind in Zinc — generics subsume
that role with a fallback to dynamic dispatch when static knowledge is absent.

See [`lang/generics.md`](generics.md) for generic syntax and binding rules.

## Open questions

| Question                                         | Status          |
|--------------------------------------------------|-----------------|
| Exact variant names on the `Type` enum           | [UNDEC]         |
| Syntax for augmenting an existing type at comptime | [UNDEC]       |
| Whether comptime expressions can emit multiple declarations from one expression | [UNDEC] |
| Generic constraint mechanism (bounds on `<T>`)   | [UNDEC: future] |
