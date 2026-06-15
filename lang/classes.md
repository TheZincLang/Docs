# Classes
Status: [FILL: implemented / planned / partial]

## Syntax
```
"class" <Ident>
  ["extends" <Ident>]
  ["implements" <Ident> {"," <Ident>}]
  ["owns" <Ident> {"," <Ident>}]
  ["serves" <Ident>]
"{" {<member>} "}"
<member> ::= <field-decl> | <method-decl> | <constructor-decl>
<field-decl>       ::= [<modifier>] <ident> ":" <type>
<method-decl>      ::= [<modifier>] ("fn" | "func" | "function") <ident> "(" [<param> {"," <param>}] ")" [":" <type>] <block>
<constructor-decl> ::= [<modifier>] "init" "(" [<param> {"," <param>}] ")" <block>
<modifier>         ::= "pub" | "priv" | "static" | "override"
```
[UNDEC: exact keyword set for modifiers; `pub`/`priv` vs `public`/`private`]

## Example
```zn
class Animal {
  name: str

  init(name: str) {
    self.name = name
  }

  fn speak(): str {
    return "..."
  }
}

class Dog extends Animal {
  override fn speak(): str {
    return "Woof"
  }
}
```

## Instantiation
```zn
let d: Dog = Dog("Rex")
```
[UNDEC: `new` keyword required, or bare constructor call?]

## AST node
Source: `../Zinc/src/parser/ParserTypes.ts`

| Field      | Type             | Description                          |
|------------|------------------|--------------------------------------|
| modifiers  | `Set<Modifier>`  | class-level modifiers                |
| id         | `number`         | class name identifier                |
| superClass | `number \| null` | parent class identifier, or null     |
| members    | `Node[]`         | field, method, and constructor nodes |

## `self` reference
`self` refers to the current instance inside methods and constructors.
[UNDEC: is `self` implicit (like Python) or a keyword receiver (like Rust)?]

## Memory
Always heap-allocated. The user cannot change this.

## Reuse and inheritance (COOP)
Zinc splits classical inheritance into two keywords. See `lang/coop.md` for the paradigm
and `lang/interfaces.md` for interfaces.

| Clause                   | Brings fields + methods | A usable *as* the target | Typical use            |
|--------------------------|-------------------------|--------------------------|------------------------|
| `extends B` (class)      | yes                     | yes                      | true subtype           |
| `implements B` (class)   | yes                     | no                       | optimization / reuse   |
| `implements I` (interface)| no — class must define   | yes                      | polymorphism / typing  |

- `extends` behaves like classical inheritance: `A extends B` means an `A` can be used
  anywhere a `B` is expected.
- `implements` on a **class** copies in that class's fields and methods but does **not**
  make the implementer usable as the implemented class. Mainly an optimization tool.
- `implements` on an **interface** requires the class to satisfy the interface; in return
  the class can be used as that interface. Interfaces cannot be instantiated or used like
  structs.

| Feature              | Status  | Notes                             |
|----------------------|---------|-----------------------------------|
| Single `extends`     | [FILL]  |                                   |
| Multiple inheritance | [UNDEC] |                                   |
| `extends` + `implements` together | [UNDEC] | assumed allowed     |
| `super` calls        | [UNDEC] |                                   |
| Method overriding    | [FILL]  | `override` modifier               |

## Access modifiers
| Modifier  | Meaning                          |
|-----------|----------------------------------|
| `pub`     | accessible outside the class     |
| `priv`    | accessible only within the class |
| (default) | [UNDEC: pub or priv by default?] |

## Rules
| Rule                       | Value                                              |
|----------------------------|----------------------------------------------------|
| Methods                    | supported                                          |
| Inheritance                | supported via `extends` (structs do not have it)   |
| `serves` targets           | exactly one class or interface                     |
| `serves` + group           | not allowed                                        |
| Protected access           | granted by `owns` and `serves` — see `lang/owns-serves.md` |
| Static members             | [UNDEC: `static` keyword on member]                |
| Abstract classes           | [UNDEC]                                            |
| Generics / type parameters | [UNDEC]                                            |
