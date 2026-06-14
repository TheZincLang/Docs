# Type System
Source of truth: `../Zinc/src/global/types/globalTypes.ts` — `TypeKind` enum

## TypeKind values
| TypeKind | Description | Notes  |
|----------|-------------|--------|
| [FILL]   | [FILL]      | [FILL] |

## Primitive types
| Zinc type         | Size   | Signed | Notes |
|-------------------|--------|--------|-------|
| [FILL: e.g. i8]   | 8-bit  | yes    |       |
| [FILL: e.g. i32]  | 32-bit | yes    |       |
| [FILL: e.g. u8]   | 8-bit  | no     |       |
| [FILL: e.g. bool] | —      | —      |       |
| [FILL: e.g. f32]  | 32-bit | —      | float |
| [FILL: e.g. str]  | —      | —      |       |

## Composite types
| Kind            | Syntax              | AST node                     |
|-----------------|---------------------|------------------------------|
| Array           | [FILL]              | [FILL]                       |
| Enum            | `enum Name { ... }` | `EnumDecl`                   |
| Struct / record | [FILL]              | [FILL: not yet implemented?] |

## Type annotations
```
<ident> ":" <type>           // variable or parameter
<function":" <type>                  // return type
```
[FILL: is type annotation always required, or can it be inferred?]

## Type inference
[FILL: describe what is inferred vs. must be explicit]

## Coercion / widening rules
[FILL: e.g. i8 widens to i32, no implicit float↔int, etc.]