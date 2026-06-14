# Functions
Status: **not yet implemented** in parser (`fn` token throws `"not implemented yet"`).

## Planned declaration syntax
```
("fn" | "func" | "function") <ident> "(" [<param> {"," <param>}] ")" [":" <type>] <block>
<param> ::= <ident> ":" <type>
```
`fn`, `func`, and `function` are interchangeable — all map to the same `Fn` TokenType with no semantic difference.

## Call syntax (working now)
```
<expr> "(" [<arg> {"," <arg>}] ")"
<arg> ::= <expr>
```
Parsed in `parsePostfix`. AST node: [FILL: check ParserTypes.ts for call node name].

## Planned features
| Feature                | Status | Notes |
|------------------------|--------|-------|
| Named parameters       | [FILL] |       |
| Default values         | [FILL] |       |
| Variadic (`...`)       | [FILL] |       |
| First-class / closures | [FILL] | see `lang/lambdas.md` |
| Overloading            | [FILL] |       |
| Generics               | [FILL] |       |

## Calling convention
[UNDEC: how arguments are passed — stack / registers / ABI target]