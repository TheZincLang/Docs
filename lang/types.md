# Type System
Source of truth: the compiler repo's `src/global/types/globalTypes.ts` (`TypeKind` enum)

## TypeKind values
| TypeKind        | Description                                   | Notes                                                 |
|-----------------|-----------------------------------------------|-------------------------------------------------------|
| `Int`           | Integer — all int-width aliases collapse here | i8/i16/i32/i64/u8/u16/u32/u64/int all → `Int` for now |
| `Float`         | 32-bit floating point                         | f32 / float                                           |
| `Double`        | 64-bit floating point                         | f64 / double                                          |
| `Bool`          | Boolean                                       | bool                                                  |
| `Char`          | Single character                              | char                                                  |
| `String`        | String                                        | string                                                |
| `Void`          | Absent value / no return                      | void; default when return type is omitted             |
| `Never`         | Diverging / unreachable                       | functions that never return (throw, infinite loop)    |
| `Struct`        | User-defined struct or class                  |                                                       |
| `Enum`          | User-defined enum                             |                                                       |
| `Interface`     | Interface type                                |                                                       |
| `TypeAlias`     | Type alias                                    | [UNDEC: alias syntax not yet defined]                 |
| `Union`         | Tagged union (T \| U \| ...)                  | TS-style syntax; C++ `std::variant` at runtime        |
| `Function`      | Named function type                           |                                                       |
| `Lambda`        | Lambda / callable value                       | see `lang/lambdas.md`                                 |
| `Method`        | Bound method on a struct or class             |                                                       |
| `Array`         | Array of element type                         | T[] — nestable; parsed by `parseType()`               |
| `Tuple`         | Fixed-length heterogeneous sequence           | [UNDEC: syntax not yet defined]                       |
| `Optional`      | Nullable wrapper                              | [UNDEC: T? syntax not yet defined]                    |
| `Reference`     | Read-only reference (`ref T`)                 | see `lang/memory.md`                                  |
| `Pointer`       | Raw unsafe pointer                            | bypasses ownership; see `lang/memory.md`              |
| `TypeParameter` | Generic type variable                         | T in `fn foo<T>` — declarations parse; see `lang/generics.md` |
| `Generic`       | Concrete generic instantiation                | Foo<T, U> — type arguments parse to a `Generic` `TypeNode`; see `lang/generics.md` |
| `Unknown`       | Not yet inferred                              | compiler-internal; never user-visible                 |
| `Inferred`      | Successfully inferred, pending resolution     | compiler-internal                                     |
| `Error`         | Type error placeholder                        | lets inference continue past a type error             |

## Primitive types
| Zinc spelling(s)                                  | TypeKind | Size   | Signed |
|---------------------------------------------------|----------|--------|--------|
| int / i8 / i16 / i32 / i64 / u8 / u16 / u32 / u64 | `Int`    | varies | varies |
| float / f32                                       | `Float`  | 32-bit | —      |
| double / f64                                      | `Double` | 64-bit | —      |
| bool                                              | `Bool`   | 8-bit  | —      |
| char                                              | `Char`   | 16-bit | —      |
| string                                            | `String` | —      | —      |
| void                                              | `Void`   | —      | —      |

All int-width aliases currently resolve to a single `Int` TypeKind. Distinct fixed-width
kinds (i8 ≠ i32 etc.) will be split out when the type checker enforces width-specific
rules.

## Composite types
| Kind            | Syntax                                         | AST node                       |
|-----------------|------------------------------------------------|--------------------------------|
| Array           | `<type>[]`                                     | `TypeNode` (`Array`, nestable) |
| Union           | `<type> "\|" <type> {"\|" <type>}`             | `TypeNode` (`Union`, ≥2 members) |
| Generic         | `<name> "<" <type> {"," <type>} ">"`           | `TypeNode` (`Generic`, nestable) |
| Enum            | `enum Name { ... }`                            | `EnumNode`                     |
| Struct          | `"struct" Name "{" {<member>} "}"`             | `StructNode`                   |
| Class           | `"class" Name [<clauses>] "{" {<member>} "}"`  | `ClassNode`                    |

`struct` and `class` are distinct constructs. A struct is a lightweight,
stack-allocated record with fields and methods but no access modifiers,
constructors, or inheritance. A class is heap-allocated and supports
`extends`, `implements`, `owns`, `serves` clauses, access modifiers, and
`init` constructors. See `lang/structs.md` and `lang/classes.md` for full
syntax. Both register their name in the parser's `declaredTypes` set.

## Union types
Status: **parses** — `parseType()` parses a `|`-separated list of members into a
`TypeNode` `Union` (each member is a name plus `[]` suffixes; duplicate members
are rejected). The semantics below (narrowing, tagged-union runtime layout,
member assignability) belong to the type checker, which is not implemented;
`TypeKind.Union` is still only a reserved enum value. The rest of this section is
the intended design.

Union types follow TypeScript syntax exactly. A union is a `|`-separated list of
two or more types that describes a value that can hold any one of those types at
a given moment.

### Syntax
```
<type> ::= <simple-type> {"|" <simple-type>}
```
```zn
let x: int | string = 42
let result: Ok | Err | null = getValue()

fn parse(input: string): int | null {
  ...
}
```

### Semantics
- A union value holds **exactly one** of the member types at runtime.
- Runtime representation: tagged union (equivalent to C++ `std::variant<T, U, ...>`).
  The tag is a small integer discriminant stored alongside the value; the largest
  member type determines the payload size.
- The type checker tracks which branch is active via **type narrowing** (see below).
- Assigning a value of type `T` to a `T | U` variable is always valid with no
  explicit cast.
- Reading a union without narrowing first is an error — the compiler requires you
  to prove which branch is active.

### Type narrowing
Narrowing works exactly as in TypeScript: any conditional that can statically prove
the active branch causes the checker to refine the type inside that branch.

```zn
let val: int | string = getValue()

if (typeof val == "int") {
  // val is int here
  let n: int = val
} else {
  // val is string here
  let s: string = val
}
```

| Narrowing mechanism          | Status       |
|------------------------------|--------------|
| `typeof` check               | [FILL]       |
| `instanceof` / type guard fn | [UNDEC]      |
| `switch` on union tag        | [UNDEC]      |
| Exhaustiveness enforcement   | [UNDEC]      |

### Null / nullable
`null` is a valid union member. A nullable type is just `T | null`:
```zn
let name: string | null = null
```
[UNDEC: is there a `T?` shorthand for `T | null`, or is the explicit form the only spelling?]

### Union in function signatures
```zn
fn first(arr: int[] | null): int | null {
  ...
}
```

### Rules
| Rule                      | Value                                                          |
|---------------------------|----------------------------------------------------------------|
| Minimum member count      | 2                                                              |
| Duplicate members         | error                                                          |
| Nested unions flattened   | `(A \| B) \| C` → `A \| B \| C` (same as TS)                   |
| Union with `never`        | `T \| never` → `T`                                             |
| Union with `unknown`      | `T \| unknown` → `unknown`                                     |
| Assigning member → union  | implicit (no cast required)                                    |
| Reading without narrowing | type error                                                     |
| Runtime layout            | tagged union; tag is smallest int that fits discriminant count |

## Type annotations
```
<ident> ":" <type>           // variable or parameter / struct field
<function> ":" <type>        // return type
<type>    ::= <member> {"|" <member>}    // union (two or more members) — parsed by parseType()
<member>  ::= <name> ["<" <type> {"," <type>} ">"] {"[]"}  // a primitive/user type, optional generic args, plus array suffixes — parsed by parseTypeMember()
```
A type annotation parses to a `TypeNode` (`parseType()`): a `Name` node carries
the interned id of the written name plus a `resolved: TypeKind` (set for known
primitives like `int`/`bool`/`string`, left `Unknown` for user-defined types to
be resolved by a later pass), and an `Array` node wraps an element `TypeNode`
for each `[]`. The same routine is reused for `let`, parameters, return types,
struct fields, and enum backing types.

Annotations are optional on `let` (an omitted type yields a `Name` node with
id `-1` / `Unknown`, i.e. inferred). Omitted function return types default to
`Void`.

## Type inference
[FILL: describe what is inferred vs. must be explicit]

## Coercion / widening rules
[FILL: e.g. i8 widens to i32, no implicit float↔int, etc.]
