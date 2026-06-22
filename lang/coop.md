# COOP — Correct Object-Oriented Programming
Zinc's native OOP model. Status: **partial** — the parser accepts the class-level
clauses (`extends`, `mixin`, `implements`, `owns`, `serves`) and member modifiers, storing
targets as interned identifiers. Interfaces, groups, and all COOP semantics
(subtyping, reuse, protected-access rules) are not yet implemented.

## Philosophy
"Correct" OOP favors **many very small, highly specialized classes** over a few large
ones. Behavior is composed of large numbers of tiny, single-purpose class *snippets*
that are reused across classes rather than duplicated.

## Keywords at a glance
| Keyword      | Target                        | Subtype? | Inherits impl? | "Has-a"?      | Notes                                                                     |
|--------------|-------------------------------|----------|----------------|---------------|---------------------------------------------------------------------------|
| `extends`    | class / group                 | yes      | yes            | no            | classical subtype                                                         |
| `mixin`      | class / group                 | no       | yes            | no            | reuse, optimization                                                       |
| `implements` | interface / group             | yes      | no             | no            | must define members                                                       |
| `owns`       | class / group                 | no       | no             | yes           | owner gains access to owned's protected members                           |
| `serves`     | class or interface (one only) | no       | no             | yes (inverse) | servant gains access to owner's protected; not instantiable without owner |

## Reuse vs. subtyping
COOP separates two concerns that classical OOP conflates under "inheritance":

- **Subtyping** — caller can substitute the type. Provided by `extends` and by
  `implements` on an interface.
- **Pure reuse** — borrow another class's implementation for optimization, no
  substitutability guarantee. Provided by `mixin`.
- **Composition** — permanent "has-a" with protected-member access and no action at a
  distance. Provided by `owns` / `serves`.

## Three orthogonal relationships
Zinc deliberately keeps separate what other languages fuse into "traits". The three are
independent and combine freely.

| Relationship              | Keyword       | Brings code?         | is-a? | Runtime cost                  |
|---------------------------|---------------|----------------------|-------|-------------------------------|
| **Interface conformance** | `implements`  | no                   | yes (nominal) | none — contract only          |
| **Mixin** (code theft)    | `mixin`       | yes — fields + bodies stamped in | no    | none — monomorphized, no vtable |
| **Extends** (value-style) | `extends`     | yes — holds a real base instance | yes   | indirection / identity        |

- **Interface conformance** is nominal and contract-only: `Foo implements Drawable`.
  Demands flow *into* the class (requirements you must satisfy). Compiler-checked, adds no
  code, orthogonal to where method bodies come from. See `lang/interfaces.md`.
- **Mixin** is pure code/definition theft — the systems-language default for reuse. No
  is-a, no shared identity; the target is *not* an instance of the mixed-in class. See
  `lang/mixins.md`.
- **Extends** holds a real instance of the base and wraps/forwards, creating genuine
  substitutability. Use it when substitutability is the actual goal; it pays the
  indirection/identity cost. The opt-in for cases mixin can't serve. See `lang/classes.md`.

## Access operators: `::` vs `.`
The operator encodes **whether there is a real runtime sub-entity**.

| Operator | Meaning                                                  | Used for          | `ref` into it? |
|----------|----------------------------------------------------------|-------------------|----------------|
| `.`      | field access / traversal into a real sub-object that has storage and identity | `owns`, `extends` | yes — `ref self.ClassName` is valid |
| `::`     | namespace / compile-time name resolution, no runtime entity | mixins, module / namespace paths | no — `ref ...::...` is malformed by construction |

Mixin-origin qualification and module paths are the **same mechanism**: resolve a name in
a scope. `self` is elided at use sites (`ClassName.field` / `ClassName::field`) but is
real — passable as `this` to functions.

## Keeping signatures short
When many classes share the same set of `extends`/`mixin`/`implements`/`owns` targets, declare
a **group** and reference the group instead. See `lang/groups.md`.

## Further reading
| Topic               | File                    |
|---------------------|-------------------------|
| Class syntax        | `lang/classes.md`       |
| Interfaces          | `lang/interfaces.md`    |
| Mixins (reuse)      | `lang/mixins.md`        |
| Generics            | `lang/generics.md`      |
| Groups              | `lang/groups.md`        |
| Owns / Serves       | `lang/owns-serves.md`   |
