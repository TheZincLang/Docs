# Type System
Source of truth: `../Zinc/src/global/types/globalTypes.ts` — `TypeKind` enum

## TypeKind values
| TypeKind | Description | Notes  |
|----------|-------------|--------|
| [FILL]   | [FILL]      | [FILL] |

## Primitive types
| Zinc type    | Size   | Signed | Notes |
|--------------|--------|--------|-------|
| i8 / int8    | 8-bit  | yes    |       |
| i32 / int32  | 32-bit | yes    |       |
| u8 / uint8   | 8-bit  | no     |       |
| bool         | 8-bit  | —      |       |
| f32 / float  | 32-bit | —      | float |
| f64 / double | 64-bit | -      | float |


## Composite types
| Kind            | Syntax                            | AST node                       |
|-----------------|-----------------------------------|--------------------------------|
| Array           | `<type>[]`                        | `TypeNode` (`Array`, nestable) |
| Enum            | `enum Name { ... }`               | `EnumNode`                     |
| Struct / record | `("struct" \| "class") Name "{" {<field>} "}"` | `StructNode`      |

`struct` and `class` are interchangeable keywords (both map to the `Struct`
TokenType, like `fn`/`func`/`function`). A field is `<ident> ":" <type>`;
fields may be separated by `,` or `;` or just newlines. Structs are global-scope
only and register their name in the parser's `declaredTypes` set.

## Type annotations
```
<ident> ":" <type>           // variable or parameter / struct field
<function> ":" <type>        // return type
<type>    ::= <name> {"[]"}   // a primitive or user type, plus array suffixes
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