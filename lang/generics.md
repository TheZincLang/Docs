# Generics & Type Parameters
Status: **parses** — the parser reads generic parameter declarations at binding
sites and generic type arguments in type annotations. Arity, scope, and
constraint checking belong to the (unwritten) type checker. Resolves the
`Generics / type parameters` `[UNDEC]` in `lang/classes.md` and the
`Generic interfaces` `[UNDEC]` in `lang/interfaces.md`.

Generic parameters let a construct be written once and reused across many types.

## Syntax
A binder declares its type parameters in an angle-bracket list immediately after its name;
whatever follows the list is the construct's body.

```
<binder> "<" <type-param> {"," <type-param>} ">" <body...>
<type-param> ::= <Ident>
```

```zn
Box<T> { ... }
Map<K, V> { ... }
fn pick<T, F, C>(...) { ... }
```

Parameter names are ordinary identifiers — any name works (`T`, `F`, `C`, or whatever)
**as long as it does not clash with an existing type name** in scope. There is no separate
namespace or reserved set for type-parameter names.

## Binding sites
Generic type parameters can be bound by any construct that introduces a **named scope
other code references**. That is the unifying rule.

| Binding site            | Generic params? | Notes                                  |
|-------------------------|-----------------|----------------------------------------|
| Function definitions    | yes             |                                        |
| Type definitions        | yes             | structs and enums                      |
| Type aliases            | yes             |                                        |
| Interface declarations  | yes             | see `lang/interfaces.md`               |
| `impl` / reuse constructs | yes           | mixin / reuse sites — see `lang/mixins.md` |

## Parsing & AST representation
Source: the compiler repo's `src/parser/parser.ts` and `src/parser/ParserTypes.ts`.

**Declarations.** `parseTypeParameters()` reads the `"<" <ident> {"," <ident>} ">"`
list that may follow a binder's name and returns the interned ids. It is wired
into functions, methods, interface method signatures, structs, classes, enums,
and interfaces; each binder node carries a `typeParameters: number[]` field
(empty when none). Duplicate parameter names are rejected. Parameter names are
recorded in the parser's `declaredTypes` so uses of them inside the body (e.g.
`value: T`) resolve by name; full scope enforcement is the type checker's job.

**Type arguments.** In a type annotation, a `<` after a name applies type
arguments. `parseType()` → `parseTypeMember()` produces a `TypeNode` of kind
`Generic` (`{id, resolved, arguments}`), where each argument is itself a full
`TypeNode` — so arguments may be unions (`Box<int | string>`) or nested generics
(`Map<K, Box<V>>`). Array suffixes wrap the whole application (`Box<int>[]`).

**Angle brackets.** The lexer greedily forms `>>`, `>=`, and `>>=`, so a closing
`>` can be glued to a following operator (notably in nested generics like
`Box<Map<K, V>>`). `consumeGenericClose()` splits such a token in place —
consuming one `>` and rewriting the remainder — so each generic level closes
correctly.

```zn
struct Box<T> { value: T }
fn pick<T, F, C>(a: T, b: F, c: C): T { return a }

let pair: Map<string, Box<int>>   // Generic node, nested Generic argument
let boxes: Box<int>[]             // Array wrapping a Generic node
```

## Ownership is part of the type
Ownership qualifiers are first-class components of a type: `ref T`, `T`, and `bor T`
are **distinct types**. Because ownership-qualified types are themselves types, a generic
`T` with no annotations is an implicit owned T and doesn't work for other ownership states.

```zn
Box<ref Foo>   // just works — no special "ownership-generic" parameter kind
```

There is **no second parameter axis** for ownership. A generic over `T` already admits
`ref Foo`, `owns Foo`, etc., as instantiations.

See `lang/memory.md` for the ownership operators themselves.

## Dispatch strategy

The compiler resolves a generic use by one of two strategies depending on what it
can prove about the type argument at the use site:

| Strategy            | Condition                               | Result                                                        |
|---------------------|-----------------------------------------|---------------------------------------------------------------|
| **Monomorphisation**| Type argument statically known          | Compiler emits a dedicated concrete version (C++ template–style) |
| **Jump table**      | Type argument not statically known      | Compiler emits one generic version that dispatches at runtime via a vtable / jump table |

The same `<T>` syntax covers both cases; the compiler chooses automatically. There
is no annotation to force one strategy or the other.

Because generics already cover the "type as parameter resolved statically" case via
monomorphisation, Zinc has no separate comptime-parameter kind. See
[`lang/comptime.md`](comptime.md) for how comptime and generics relate.

## Constraints (future)
A type-qualifier **constraint** mechanism may be added later if generics must be bounded
— e.g. restricting `T` to owned-only or ref-only types. This is a *constraint-level*
feature layered onto the single type-parameter axis, **not** a second kind of parameter.

| Rule                              | Value                                            |
|-----------------------------------|--------------------------------------------------|
| Parameter kinds                   | one — a type parameter `T`                        |
| Ownership-qualified instantiation | allowed automatically (`Box<ref Foo>` etc.)       |
| Bounding to owned-only / ref-only | [UNDEC: constraint syntax — future]               |
| Other bounds (interface, etc.)    | [UNDEC]                                            |
