# Groups
Status: [FILL: implemented / planned / partial]

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
