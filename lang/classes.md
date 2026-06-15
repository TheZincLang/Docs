# Classes
Status: [FILL: implemented / planned / partial]

## Syntax
```
"class" <Ident> ["extends" <Ident>] "{" {<member>} "}"
<member> ::= <field-decl> | <method-decl> | <constructor-decl>
<field-decl>       ::= [<modifier>] <ident> ":" <type> [";"]
<method-decl>      ::= [<modifier>] ("fn" | "func" | "function") <ident> "(" [<param> {"," <param>}] ")" [":" <type>] <block>
<constructor-decl> ::= [<modifier>] "init" "(" [<param> {"," <param>}] ")" <block>
<modifier>         ::= "pub" | "priv" | "static" | "override"
```
[UNDEC: exact keyword set for modifiers; `pub`/`priv` vs `public`/`private`]

## Example
```zn
class Animal {
  name: str;

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

| Field      | Type            | Description                          |
|------------|-----------------|--------------------------------------|
| modifiers  | `Set<Modifier>` | class-level modifiers                |
| id         | `number`        | class name identifier                |
| superClass | `number \| null`| parent class identifier, or null     |
| members    | `Node[]`        | field, method, and constructor nodes |

## `self` reference
`self` refers to the current instance inside methods and constructors.
[UNDEC: is `self` implicit (like Python) or a keyword receiver (like Rust)?]

## Inheritance
| Feature              | Status   | Notes                              |
|----------------------|----------|------------------------------------|
| Single inheritance   | [FILL]   | via `extends`                      |
| Multiple inheritance | [UNDEC]  |                                    |
| Interface / trait    | [UNDEC]  | separate feature?                  |
| `super` calls        | [UNDEC]  |                                    |
| Method overriding    | [FILL]   | `override` modifier                |

## Access modifiers
| Modifier  | Meaning                          |
|-----------|----------------------------------|
| `pub`     | accessible outside the class     |
| `priv`    | accessible only within the class |
| (default) | [UNDEC: pub or priv by default?] |

## Rules
| Rule                        | Value                                    |
|-----------------------------|------------------------------------------|
| Fields vs struct fields     | classes have methods; structs do not     |
| Static members              | [UNDEC: `static` keyword on member]      |
| Abstract classes            | [UNDEC]                                  |
| Generics / type parameters  | [UNDEC]                                  |
| Memory / ownership          | [UNDEC: heap-allocated? see `lang/memory.md`] |
