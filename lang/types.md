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
| Kind            | Syntax              | AST node                     |
|-----------------|---------------------|------------------------------|
| Array           | `<type>[]`          | array postfix node           |
| Enum            | `enum Name { ... }` | `EnumNode`                   |
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