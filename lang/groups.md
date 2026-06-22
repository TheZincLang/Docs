# Groups
Status: **parses** — the `group` keyword and group declarations parse to a
`GroupNode` (a list of referenced type ids). The group name resolves when used in
an `extends`/`implements`/`owns` clause. Expanding a group reference into its
members (and rejecting it in `serves` / type-annotation position) is a semantic
concern and is not implemented — there is no type checker yet.

A group is a named collection of classes used to keep class signatures concise when
bulk-applying `extends`, `implements`, or `owns`.

## Syntax
```
"group" <Ident> "{" {<Ident> ","} "}"
```

## Example
```zn
group Renderable {
  Drawable,
  Colorable,
  Transformable,
}

class Sprite implements Renderable {
  // equivalent to: implements Drawable, Colorable, Transformable
}
```

## Valid positions
| Keyword     | Can reference a group |
|-------------|-----------------------|
| `extends`   | yes                   |
| `implements`| yes                   |
| `owns`      | yes                   |

## Rules
| Rule                             | Value                                  |
|----------------------------------|----------------------------------------|
| Nesting groups within groups     | [UNDEC]                                |
| Group contains interfaces        | [UNDEC]                                |
| Group used in `serves`           | no — a class/interface may serve only one |
| Group is a type                  | no — not usable as a type annotation   |
