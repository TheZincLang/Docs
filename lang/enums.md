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
Source: `../Zinc/src/parser/ParserTypes.ts`

| Field     | Type            | Description               |
|-----------|-----------------|---------------------------|
| modifiers | `Set<Modifier>` | enum modifiers like const |
| id        | `number`        | enum identifier           |
| options   | `Node[]`        | enum options              |

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