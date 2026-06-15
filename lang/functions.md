# Functions
Status: **implemented** in parser (`parseFunction` → `FunctionNode`).

## Declaration syntax
```
("fn" | "func" | "function") <ident> "(" [<param> {"," <param>}] ")" [":" <type>] <block>
<param> ::= <ident> ":" <type>
```
`fn`, `func`, and `function` are interchangeable — all map to the same `Fn` TokenType with no semantic difference.

Parameter and return types are parsed by `parseType()` into a `TypeNode` (see `lang/types.md`), so a trailing `[]` (e.g. `values: int[]`, `: int[]`) becomes a nested `Array` type node rather than a modifier flag. When the return type is omitted it defaults to `Void`. Parameters and the function body share one scope, so a parameter name may not collide with a top-level local in the body or with another parameter.

AST node: `FunctionNode` (`{id, modifiers, parameters, returnType, body}`), where each parameter is `{id, type}` and `returnType` is a `TypeNode`. `async`/`export` reach the node via the shared modifier mechanism. Function names are recorded in the parser's `declaredFunctions` set and surface in the symbol table.

## Call syntax (working now)
```
<expr> "(" [<arg> {"," <arg>}] ")"
<arg> ::= <expr>
```
Parsed in `parsePostfix`. AST node: `CallNode` (`{object, arguments}`).

## Planned features
| Feature                | Status | Notes                 |
|------------------------|--------|-----------------------|
| Named parameters       | [FILL] |                       |
| Default values         | [FILL] |                       |
| Variadic (`...`)       | [FILL] |                       |
| First-class / closures | [FILL] | see `lang/lambdas.md` |
| Overloading            | [FILL] |                       |
| Generics               | [FILL] |                       |

## Calling convention
[UNDEC: how arguments are passed — stack / registers / ABI target]