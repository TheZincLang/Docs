# Owns / Serves
Status: [FILL: implemented / planned / partial]

Two keywords for permanent "has-a" composition. Both grant access to each other's
protected members. Neither affects the other class's behavior toward unrelated callers
(no action at a distance).

---

## `owns`

A class that `owns` another class automatically has an instance of that class embedded
in each of its objects.

### Access syntax
```zn
MyOwnedClass.field
MyOwnedClass.method()
```
Uses the **class name** as the accessor, not `self` or `this`.

### Example
```zn
class Engine {
  protected horsepower: i32

  fn start() { ... }
}

class Car owns Engine {
  fn describe() {
    Engine.start()
    return Engine.horsepower
  }
}
```

### Rules
| Rule                                     | Value                                              |
|------------------------------------------|----------------------------------------------------|
| Owned instance lifetime                  | tied to the owning object                          |
| Access to owned class's protected members | yes                                               |
| Owned class's behavior toward others    | unchanged — no action at a distance                |
| Multiple `owns`                          | [UNDEC: owns multiple classes / a group]           |
| `owns` a group                           | yes — bulk-owns all classes in the group           |
| `owns` an interface                      | [UNDEC]                                            |
| Owned class can be `serves`              | [UNDEC: compatible?]                               |

---

## `serves`

A class marked as serving cannot be instantiated without an owner. The owner has access
to its protected members in the same way as `owns`.

### Restriction
A class may `serves` **exactly one** target — either one class or one interface. Multiple
targets are not allowed.

### Syntax (on the served class)
```
"class" <Ident> "serves" <Ident> "{" {<member>} "}"
```

### Example
```zn
class Inventory serves Player {
  protected items: Item[]

  fn add(item: Item) { ... }
}

class Player {
  fn pickUp(item: Item) {
    Inventory.add(item)       // access via class name
  }
}
```

### Comparison to `owns`
| Aspect                             | `owns`                   | `serves`                            |
|------------------------------------|--------------------------|-------------------------------------|
| Who declares the relationship      | the owning class         | the serving (child) class           |
| Instantiable without owner         | yes                      | no                                  |
| Targets allowed                    | [UNDEC: multiple / group]| exactly one (class or interface)    |
| Access syntax in owner             | `ClassName.x`            | `ClassName.x`                       |
| Protected access granted to owner  | yes                      | yes                                 |
| Effect on other class's behavior   | none                     | none                                |

---

## Protected access
Both `owns` and `serves` relationships grant the **owning / served-to** class access to
the other class's `protected` fields and methods. This access is unidirectional from the
perspective of external callers — the owned/served class exposes nothing extra to anyone
else.

[UNDEC: exact `protected` modifier keyword — `protected` vs `prot` vs other]
