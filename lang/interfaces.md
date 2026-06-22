# Interfaces
Status: **parses** — the `interface` keyword and interface *declarations* parse to
an `InterfaceNode` (member signatures become `FieldNode` / `MethodSignatureNode`).
A class may name an interface in its `implements` clause. Semantic checking
(conformance, "implementing class defines every signature") is not implemented —
there is no type checker yet.

Interfaces declare a set of fields and method signatures that a class can `implements`.
Unlike structs and classes, an interface **cannot be instantiated or used as a value
type** (i.e. not "used like a struct"). A class that `implements` an interface can be
used *as* that interface (nominal).

## Syntax
```
"interface" <Ident> "{" {<member-sig>} "}"
<member-sig> ::= <field-sig> | <method-sig>
<field-sig>  ::= <ident> ":" <type>
<method-sig> ::= ("fn" | "func" | "function") <ident> "(" [<param> {"," <param>}] ")" [":" <type>]
```
Both field signatures and method signatures parse. A method signature has no body;
an omitted return type defaults to `void`. Duplicate member names are rejected.

## Example
```zn
interface Speaker {
  fn speak(): str
}

class Dog implements Speaker {
  fn speak(): str {
    return "Woof"
  }
}
```

## Semantics vs. TypeScript
| Aspect                           | Zinc          | TypeScript        |
|----------------------------------|---------------|-------------------|
| Usable as a value / object type  | no            | yes (structural)  |
| `implements` → usable as type    | yes (nominal) | yes               |
| Provides an implementation       | no            | no                |

## Rules
| Rule                          | Value                                         |
|-------------------------------|-----------------------------------------------|
| Instantiable                  | no                                            |
| Used like a struct            | no                                            |
| Class `implements` it         | class becomes usable as the interface         |
| `implements` provides methods | no — implementing class must define them      |
| Interface extends interface   | [UNDEC]                                        |
| Default methods               | [UNDEC]                                        |
| Generic interfaces            | yes — interfaces are a generic binding site; see `lang/generics.md` |
