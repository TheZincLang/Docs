# Functions
Status: **not yet implemented** in parser (`fn` token throws `"not implemented yet"`).

## Planned declaration syntax
```
[FILL: fn <ident> "(" [<param> {"," <param>}] ")" ["->" <type>] <block>]
[FILL: <param> ::= <ident> ":" <type>]
```

## Call syntax (working now)
```
<expr> "(" [<arg> {"," <arg>}] ")"
<arg> ::= <expr>
```
Parsed in `parsePostfix`. AST node: [FILL: check ParserTypes.ts for call node name].

## Planned features
| Feature | Status | Notes |
|---|---|---|
| Named parameters | [FILL] | |
| Default values | [FILL] | |
| Variadic (`...`) | [FILL] | |
| First-class / closures | [FILL] | |
| Overloading | [FILL] | |
| Generics | [FILL] | |

## Calling convention
[FILL: how arguments are passed — stack / registers / ABI target]