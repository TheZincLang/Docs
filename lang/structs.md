# Structs
Status: implemented

## Syntax
```
"struct" <Ident> "{" {<member>} "}"
<member>     ::= <field-decl> | <method-decl>
<field-decl> ::= <ident> ":" <type>
<method-decl> ::= ("fn" | "func" | "function") <ident> "(" [<param> {"," <param>}] ")" [":" <type>] <block>
<param>      ::= <ident> ":" <type>
```

Structs do not support access modifiers on members, constructors, or inheritance clauses. Methods use the same syntax as top-level functions.

## Example
```zn
struct Point {
  x: int
  y: int

  fn manhattan(): int {
    return x + y
  }
}
```

## Instantiation
```zn
let p: Point = Point { x: 1, y: 2 }
```
[UNDEC: named-field initializer syntax confirmed, or positional, or both?]

## AST nodes
Source: `../Zinc/src/parser/ParserTypes.ts`

### StructNode
| Field     | Type            | Description                                    |
|-----------|-----------------|------------------------------------------------|
| modifiers | `Set<Modifier>` | struct-level modifiers (e.g. `export`)         |
| id        | `number`        | interned identifier of the struct name         |
| fields    | `Node[]`        | `FieldNode` and `FunctionNode` member nodes    |

### FieldNode
| Field     | Type            | Description                            |
|-----------|-----------------|----------------------------------------|
| modifiers | `Set<Modifier>` | member modifiers (unused for structs)  |
| id        | `number`        | interned identifier of the field name  |
| type      | `TypeNode`      | declared field type                    |

## Field access
```zn
p.x
```
Parsed in `parsePostfix` as member access. AST node: `FieldAccessNode`.

## Memory
Stack-allocated by default. Can be promoted to the heap via ownership operators (see `lang/memory.md`).

## Rules
| Rule                  | Value                                      |
|-----------------------|--------------------------------------------|
| Methods               | supported (same syntax as top-level `fn`)  |
| Constructors (`init`) | not supported                              |
| Access modifiers      | not supported                              |
| Inheritance           | not supported                              |
| Field default values  | [UNDEC]                                    |
| Nested structs        | [UNDEC]                                    |
| Recursive / self-ref  | [UNDEC: heap pointer required?]            |
| Memory layout         | [UNDEC: packed / aligned]                  |
| Mutability            | [UNDEC: field-level or binding-level?]     |
