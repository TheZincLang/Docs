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

A servant class is a helper that latches onto an **existing** object at runtime and
operates on it from the outside. It cannot be constructed directly — it must be attached
via `addServant`.

### Restriction
A class may `serves` **exactly one** target — either one class or one interface. Multiple
targets are not allowed.

### Declaration syntax
```
"class" <Ident> "serves" <Ident> "{" {<member>} "}"
```

### Instantiation syntax
```
<expr> "." "addServant" "<" <Ident> ">" "(" [<arg> {"," <arg>}] ")"
```
`args` are passed to the servant's constructor. The servant is attached to the object
`<expr>` evaluates to.

### Example
```zn
class DebugOverlay serves Entity {
  fn printState() {
    // Entity's protected fields are accessible here
    print(Entity.x, Entity.y, Entity.health)
  }
}

class Entity {
  protected x: f32
  protected y: f32
  protected health: i32
}

// attach at runtime
let e = Entity()
e.addServant<DebugOverlay>()
e.DebugOverlay.printState()
```
[UNDEC: exact call syntax for invoking servant methods after attachment — `e.DebugOverlay.method()` or other]

### Comparison to `owns`
| Aspect                           | `owns`                    | `serves`                              |
|----------------------------------|---------------------------|---------------------------------------|
| Who declares the relationship    | the owning class          | the serving (helper) class            |
| Instantiation                    | automatic with the owner  | explicit `addServant<T>(args)` call   |
| Instantiable standalone          | yes                       | no                                    |
| Targets allowed                  | [UNDEC: multiple / group] | exactly one (class or interface)      |
| Who gains protected access       | owner → owned's protected | servant → owner's protected           |
| Effect on owner's behavior       | none                      | none                                  |

---

## Protected access
The direction differs between the two keywords:

| Keyword | Who gains access            | To whose protected members |
|---------|-----------------------------|----------------------------|
| `owns`  | the **owner**               | the **owned** class        |
| `serves`| the **servant**             | the **owner** class        |

`serves` grants the servant access to its owner's internals so it can do its job —
not the other way around. The owner class's behavior toward all other callers is
completely unchanged (no action at a distance).

[UNDEC: exact `protected` modifier keyword — `protected` vs `prot` vs other]
