# Enums

## Syntax
```
"enum" <Ident> [":" <type>] "{" {<variant> ","} "}"
<variant> ::= <Ident> ["=" <expr>]
<type> ::= "u8" | "u16" | "u32" | "u64" | "i8" | "i16" | "i32" | "i64"
```

## Example
```zn
enum Color {
  Red = 0,
  Green,
  Blue,
}
```
Uninitialized variants auto-increment from previous value (like C).

## AST node
Source: the compiler repo's `src/parser/ParserTypes.ts`

| Field     | Type                | Description                                            |
|-----------|---------------------|-------------------------------------------------------|
| id        | `number`            | interned identifier of the enum name                  |
| modifiers | `Set<Modifier>`     | enum modifiers (e.g. `const`)                         |
| type      | `TypeNode`          | backing type (e.g. `: u8`); `Unknown` when omitted    |
| options   | `EnumOptionNode[]`  | each option is `{id, value?}` (`value` = `Node` expr) |

## Usage
```zn
let c: Color = Color.Green
```

## Rules
| Rule                    | Value                                 |
|-------------------------|---------------------------------------|
| Variant value type      | only integers allowed                 |
| Duplicate variant names | error                                 |
| Enum as type annotation | `let x: Color = Color.Red`            |
| Exhaustive switch       | can be inforced later via config file |