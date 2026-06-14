# Enums

## Syntax
```
"enum" <Ident> "{" {<variant> ","} "}"
<variant> ::= <Ident> ["=" <expr>]
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
| Field | Type | Description |
|---|---|---|
| [FILL: node kind] | `"EnumDecl"` [FILL] | |
| [FILL: name field] | `string` | enum identifier |
| [FILL: variants field] | `EnumVariant[]` | |
[FILL: fill in actual field names from ParserTypes.ts EnumDecl interface]

## Usage
```zn
[FILL: how to reference a variant, e.g. Color.Red or just Red?]
```

## Rules
| Rule | Value |
|---|---|
| Variant value type | [FILL: integer only? any expr?] |
| Duplicate variant names | [FILL: error?] |
| Enum as type annotation | [FILL: `let x: Color = Color.Red`?] |
| Exhaustive switch | [FILL: enforced by compiler?] |