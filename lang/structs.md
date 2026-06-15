# Structs
Status: [FILL: implemented / planned / partial]

## Syntax
```
"struct" <Ident> "{" {<field>} "}"
<field> ::= <ident> ":" <type>
```

## Example
```zn
struct Point {
  x: f32
  y: f32
}
```

## Instantiation
```zn
let p: Point = Point { x: 1.0, y: 2.0 }
```
[UNDEC: named-field initializer syntax confirmed, or positional, or both?]

## AST node
Source: `../Zinc/src/parser/ParserTypes.ts`

| Field     | Type            | Description             |
|-----------|-----------------|-------------------------|
| modifiers | `Set<Modifier>` | struct modifiers        |
| id        | `number`        | struct name identifier  |
| fields    | `Node[]`        | field declaration nodes |

## Field access
```zn
p.x
```
Parsed in `parsePostfix` as member access. AST node: [FILL: check ParserTypes.ts for member node name].

## Memory
Stack-allocated by default. Can be promoted to the heap via ownership operators (see `lang/memory.md`).

## Rules
| Rule                  | Value                                      |
|-----------------------|--------------------------------------------|
| Methods               | supported (same syntax as classes)         |
| Inheritance           | not supported                              |
| Field default values  | [UNDEC]                                    |
| Nested structs        | [FILL]                                     |
| Recursive / self-ref  | [UNDEC: heap pointer required?]            |
| Memory layout         | [UNDEC: packed / aligned]                  |
| Mutability            | [FILL: field-level or binding-level?]      |
