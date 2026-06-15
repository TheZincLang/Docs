# COOP — Correct Object-Oriented Programming
Zinc's native OOP model. Status: [FILL: implemented / planned / partial]

## Philosophy
"Correct" OOP favors **many very small, highly specialized classes** over a few large
ones. Behavior is composed from large numbers of tiny, single-purpose class *snippets*
that are reused across classes rather than duplicated.

## Reuse vs. subtyping
COOP separates two concerns that classical OOP conflates under "inheritance":

| Goal                                   | Mechanism                      |
|----------------------------------------|--------------------------------|
| Use `A` wherever its target is expected | `extends` (class) · `implements` (interface) |
| Pull in another class's fields/methods only, without subtyping | `implements` (class) |

- **Subtyping** — caller can substitute the type. Provided by `extends` and by
  `implements` on an interface.
- **Pure reuse** — borrow the implementation for optimization, with no substitutability
  guarantee. Provided by `implements` on a class.

Full mechanics: `lang/classes.md`. Interfaces: `lang/interfaces.md`.
