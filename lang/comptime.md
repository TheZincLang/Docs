# Comptime
Status: **unimplemented** — design complete, not yet in compiler

Zinc's comptime model rests on three principles:

1. **Types are values.** A type is an enum value at both compile time and runtime,
   so types can drive logic in either stage.
2. **Pure functions run at comptime.** Any pure function may be evaluated at
   comptime — see [Pure functions](#pure-functions).
3. **Definitions are values.** Comptime code can produce new definitions of any
   kind and treat existing definitions as ordinary values.

## Comptime mechanisms

There are two ways to run comptime code.

### Inline comptime block

An anonymous `{ }` block inside any definition body is a comptime block. It runs
at comptime and its `return` value is spliced into the definition at that position.
The type of the returned value determines what gets added (a field, a method, etc.).

```zn
struct Foo {
    bar: getType()          // simple case — type annotation is a comptime call
    {
        const baz: StructField = new StructField("baz")
        baz.type = makeType(Zinc::type.int)
        baz.defaultValue = 42
        return baz          // spliced in as a field of Foo
    }
}
```

Both forms are comptime: a bare call in a type-annotation position (`bar: getType()`)
and a full block that builds and returns a definition value. The block form is used
when more than one statement is needed.

Multiple blocks may appear in the same definition body; each is evaluated in source
order and contributes its return value independently.

### File-level `comptime()` function

Any source file may define a `comptime()` function. It is a special entry point
analogous to `main()` — no inputs, no return type. It calls `define()` to register
new definitions into the current compilation unit.

```zn
fn comptime() {
    const T: StructDef = new StructDef("Point")
    T.addField("x", type.int)
    T.addField("y", type.int)
    define(T)
}
```

`define(value)` registers the definition. Without a `define()` call the value has
no effect.

#### Apply comptime to file

The compiler exposes an **"apply comptime to file"** operation that runs the file's
`comptime()` function and writes the generated definitions back into the source
file as ordinary Zinc code. This makes the generated output visible, diffable, and
editable. After applying, the generated definitions exist as normal source and the
`comptime()` function is no longer needed for them.

## Pure functions

A function is **pure** if it has no side effects. Purity is what makes a function
callable from a comptime context.

### Inferred purity

Purity is inferred automatically. The compiler inspects the function body; if it
finds no impure operations the function is eligible for comptime use. No annotation
is required.

Calling a function the compiler cannot verify as pure from a comptime position is
a compile error.

### Enforced purity (`pure`)

The `pure` modifier converts purity from an inferred property to a
**compiler-enforced contract**: any impure operation in the body is an error
regardless of whether the function is ever called from a comptime position.

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

`pure` is a modifier placed before `fn`/`func`/`function`, consistent with `async`
and `export`.

| Mode     | Annotation | When the compiler errors on impure ops                    |
|----------|------------|-----------------------------------------------------------|
| Inferred | *(none)*   | Only when called from a comptime position                 |
| Enforced | `pure fn`  | Always, regardless of call site                           |

### Allowed and disallowed at comptime

| Allowed                                | Disallowed (side effects)       |
|----------------------------------------|---------------------------------|
| Arithmetic and logic                   | IO (files, network, stdout)     |
| Allocation                             | Reading the clock               |
| Constructing values and types          | RNG / reading machine state     |
| Calling other pure functions           | Calling impure functions        |
| Producing new definitions              |                                 |

## Types as values

A type is an enum value — an instance of the built-in `type` enum in the `Zinc` namespace. The `typeof`
operator returns this value.

```zn
let t: Zinc::type = typeof val   // e.g. type.int, type.string, ...
```

Type values support comparison and `switch`:

```zn
switch (typeof val) {
    case Zinc::type.int:    { ... }
    case Zinc::type.string: { ... }
    default:                { ... }
}
```

`any` is the one case where type information travels with a value: an `any` value
is a pointer to a heap-allocated `{ value, type }` struct. For all other types the
compiler bakes type information into every use site at compile time rather than
carrying it at runtime — there is no per-value tag, but the type is not simply
absent; it is resolved and encoded into the generated code at each relevant
position.

| Aspect                     | Behavior                                        |
|----------------------------|-------------------------------------------------|
| `typeof expr` return type  | `type` (built-in enum)                          |
| Variant names              | lowercase, matching keyword spellings           |
| Comparison form            | `typeof val == Zinc::type.int`                  |
| `any` runtime layout       | pointer to heap struct `{ type: Zinc::, type: type }` |
| Non-`any` type information | baked into use sites at comptime; no runtime tag |
| Avoiding `any` overhead    | use generics instead                            |

[UNDEC: complete list of `type` enum variants — confirm they cover all `TypeKind`
values from `lang/types.md` and decide names for composite kinds like `type.array`,
`type.union`, etc.]

## Definitions as values

Comptime code operates on first-class definition values. The built-in definition
types are:

| Type         | Represents                        |
|--------------|-----------------------------------|
| `StructField`| A field in a struct or class      |
| `StructDef`  | A complete struct definition      |
| `ClassDef`   | A complete class definition       |
| `EnumDef`    | A complete enum definition        |
| `FunctionDef`| A function definition             |
| `MethodDef`  | A method on a struct or class     |

[UNDEC: exact names and API surface of these built-in definition types]

**Staging rule.** Comptime code is compiled using definitions as they exist at the
start of the comptime pass. A comptime expression that modifies an existing
definition cannot observe its own modification during the same pass. The modified
definition becomes visible to runtime code after the pass.

## Generics and comptime

Generic type parameters (`<T>`) are not comptime parameters. A generic is resolved
by one of two strategies depending on what the compiler can prove at the use site:

- **Monomorphisation** — when the type argument is statically known, the compiler
  emits a dedicated concrete version (C++ template–style).
- **Jump table** — when the type argument is not statically known, the compiler
  emits one generic version that dispatches through a vtable / jump table at runtime.

The same `<T>` syntax covers both cases; the compiler chooses the strategy. Because
generics subsume the "type as statically-known parameter" role, there is no separate
comptime-parameter kind. See [`lang/generics.md`](generics.md).

## Open questions

| Question                                                          | Status          |
|-------------------------------------------------------------------|-----------------|
| Complete list of `type` enum variants (especially composite kinds) | [UNDEC]        |
| Exact names and method API of built-in definition types (`StructDef`, etc.) | [UNDEC] |
| Generic constraint mechanism (bounds on `<T>`)                    | [UNDEC: future] |
