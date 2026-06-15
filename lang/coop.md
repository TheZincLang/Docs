# COOP — Correct Object-Oriented Programming
Zinc's native OOP model. Status: [FILL: implemented / planned / partial]

## Philosophy
"Correct" OOP favors **many very small, highly specialized classes** over a few large
ones. Behavior is composed of large numbers of tiny, single-purpose class *snippets*
that are reused across classes rather than duplicated.

## Keywords at a glance
| Keyword      | Target                        | Subtype? | Inherits impl? | "Has-a"?      | Notes                                                                     |
|--------------|-------------------------------|----------|----------------|---------------|---------------------------------------------------------------------------|
| `extends`    | class / group                 | yes      | yes            | no            | classical subtype                                                         |
| `implements` | class / group                 | no       | yes            | no            | reuse, optimization                                                       |
| `implements` | interface / group             | yes      | no             | no            | must define members                                                       |
| `owns`       | class / group                 | no       | no             | yes           | owner gains access to owned's protected members                           |
| `serves`     | class or interface (one only) | no       | no             | yes (inverse) | servant gains access to owner's protected; not instantiable without owner |

## Reuse vs. subtyping
COOP separates two concerns that classical OOP conflates under "inheritance":

- **Subtyping** — caller can substitute the type. Provided by `extends` and by
  `implements` on an interface.
- **Pure reuse** — borrow another class's implementation for optimization, no
  substitutability guarantee. Provided by `implements` on a class.
- **Composition** — permanent "has-a" with protected-member access and no action at a
  distance. Provided by `owns` / `serves`.

## Keeping signatures short
When many classes share the same set of `extends`/`implements`/`owns` targets, declare
a **group** and reference the group instead. See `lang/groups.md`.

## Further reading
| Topic               | File                    |
|---------------------|-------------------------|
| Class syntax        | `lang/classes.md`       |
| Interfaces          | `lang/interfaces.md`    |
| Groups              | `lang/groups.md`        |
| Owns / Serves       | `lang/owns-serves.md`   |
