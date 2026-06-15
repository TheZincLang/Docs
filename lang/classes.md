# Classes
Status: implemented

## Syntax
```
"class" <Ident>
  ["extends" <Ident>]
  ["implements" <Ident> {"," <Ident>}]
  ["owns" <Ident> {"," <Ident>}]
  ["serves" <Ident>]
"{" {<member>} "}"
<member>           ::= <field-decl> | <method-decl> | <constructor-decl>
<field-decl>       ::= [<modifier>] <ident> ":" <type>
<method-decl>      ::= {<modifier>} ("fn" | "func" | "function") <ident> "(" [<param> {"," <param>}] ")" [":" <type>] <block>
<constructor-decl> ::= [<modifier>] "init" "(" [<param> {"," <param>}] ")" <block>
<modifier>         ::= "public" | "private" | "protected" | "static" | "override"
<param>            ::= <ident> ":" <type>
```

Inheritance clauses appear in fixed order: `extends`, then `implements`, then `owns`, then `serves`. Each is optional and independent.

## Example
```zn
class Shape {
  public name: string
  private sides: int

  init(name: string, sides: int) {
    self.name = name
    self.sides = sides
  }

  public fn describe(): string {
    return name
  }
}

class Rect extends Shape implements Drawable, Sized owns Point serves Canvas {
  topLeft: Point
  size: Point
  static label: string[]

  init(topLeft: Point, size: Point) {
    self.topLeft = topLeft
    self.size = size
  }

  override public fn describe(): string {
    return "rect"
  }
}
```

## Instantiation
```zn
let d: Dog = Dog("Rex")
```
[UNDEC: `new` keyword required, or bare constructor call?]

## AST nodes
Source: `../Zinc/src/parser/ParserTypes.ts`

### ClassNode
| Field             | Type             | Description                                    |
|-------------------|------------------|------------------------------------------------|
| modifiers         | `Set<Modifier>`  | class-level modifiers (e.g. `export`)          |
| id                | `number`         | interned identifier of the class name          |
| superClass        | `number \| null` | `extends` target identifier, or null           |
| implementsTargets | `number[]`       | `implements` clause target identifiers         |
| owns              | `number[]`       | `owns` clause target identifiers               |
| serves            | `number \| null` | `serves` target identifier, or null            |
| members           | `Node[]`         | `FieldNode`, `FunctionNode`, `ConstructorNode` |

### FieldNode
| Field     | Type            | Description                           |
|-----------|-----------------|---------------------------------------|
| modifiers | `Set<Modifier>` | member access modifiers               |
| id        | `number`        | interned identifier of the field name |
| type      | `TypeNode`      | declared field type                   |

### ConstructorNode
| Field      | Type                  | Description                 |
|------------|-----------------------|-----------------------------|
| modifiers  | `Set<Modifier>`       | constructor modifiers       |
| parameters | `FunctionParameter[]` | parameter list              |
| body       | `Node` (CodeBlock)    | constructor body            |

Methods are represented as `FunctionNode` with the member modifiers stored in `FunctionNode.modifiers`.

## `self` reference
`self` refers to the current instance inside methods and constructors. It is implicit unless something with the same name exists in the current scope.
[UNDEC: is `self` always available, or only when there is no shadowing variable?]

## Memory
Always heap-allocated. The user cannot change this.

## Reuse and inheritance (COOP)
Zinc splits classical inheritance into two keywords. See `lang/coop.md` for the paradigm
and `lang/interfaces.md` for interfaces.

| Clause                     | Brings fields + methods | A usable *as* the target | Typical use           |
|----------------------------|-------------------------|--------------------------|-----------------------|
| `extends B` (class)        | yes                     | yes                      | true subtype          |
| `implements B` (class)     | yes                     | no                       | optimization / reuse  |
| `implements I` (interface) | no — class must define  | yes                      | polymorphism / typing |

- `extends` behaves like classical inheritance: `A extends B` means an `A` can be used
  anywhere a `B` is expected.
- `implements` on a **class** copies in that class's fields and methods but does **not**
  make the implementer usable as the implemented class. Mainly an optimization tool.
- `implements` on an **interface** requires the class to satisfy the interface; in return
  the class can be used as that interface. Interfaces cannot be instantiated or used like
  structs.

| Feature                           | Status      | Notes                               |
|-----------------------------------|-------------|-------------------------------------|
| Single `extends`                  | implemented |                                     |
| Multiple `implements` targets     | implemented |                                     |
| Multiple `owns` targets           | implemented |                                     |
| Single `serves` target            | implemented |                                     |
| `extends` + `implements` together | implemented |                                     |
| Multiple inheritance              | not supported |                                   |
| `super` calls                     | [UNDEC]     |                                     |
| Method overriding                 | implemented | `override` modifier on method       |

## Access modifiers
| Modifier    | Meaning                                 |
|-------------|-----------------------------------------|
| `public`    | accessible outside the class            |
| `private`   | accessible only within the class        |
| `protected` | accessible within the class and subclasses |
| `static`    | belongs to the class, not an instance   |
| `override`  | overrides a method from a superclass    |
| (default)   | [UNDEC: public or private by default?]  |

## Rules
| Rule                       | Value                                                       |
|----------------------------|-------------------------------------------------------------|
| Methods                    | supported                                                   |
| Constructors (`init`)      | supported                                                   |
| Inheritance                | supported via `extends` (structs do not have it)            |
| `serves` targets           | exactly one class or interface                              |
| `serves` + group           | not allowed                                                 |
| Protected access           | granted by `owns` and `serves` — see `lang/owns-serves.md` |
| Static members             | supported via `static` modifier                             |
| Abstract classes           | [UNDEC]                                                     |
| Generics / type parameters | [UNDEC]                                                     |
