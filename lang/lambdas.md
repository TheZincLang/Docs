# Lambdas
Status: Parsed (`LambdaNode`); semantic analysis and codegen pending.

A lambda is a callable value — a function expression that optionally captures variables from its enclosing scope. There is no user-facing distinction between "lambda" and "closure"; both use the same syntax. The difference is internal: a lambda with no captures compiles to a plain function pointer; one with captures carries an environment.

## Syntax
```
["[" [<capture> {"," <capture>}] "]"] "(" [<param> {"," <param>}] ")" [":" <return-type>] "=>" <block>
<capture> ::= [<modifier>] <ident> | [<modifier>] "*"
<modifier> ::= "copy" | "ref" | "borrow" | "bor" | "move"
<param>    ::= <ident> ":" <type>
```

The capture list `[...]` is optional — omitting it is equivalent to no captures.
The return type annotation `: <return-type>` is optional; when omitted the return
type defaults to `void` and is otherwise inferred by a later pass.

## Type annotation
```
"(" [<param-type> {"," <param-type>}] ")" "=>" <return-type>
```

## Example
```zn
let colorPixel: (color: string) => void = [x, ref y](color: string) => {
    setColor(x, y, color)
}

// with an explicit return type on the value:
let add = (a: int, b: int): int => {
    return a + b
}
```

## Capture list
Variables in `[...]` are captured from the enclosing scope. Any ownership operator from `lang/memory.md` may be used as a modifier; the default is `copy`.

| Syntax | Capture mode |
|---|---|
| `x` | copy (default) |
| `copy x` | copy — explicit |
| `ref x` | read-only reference |
| `borrow x` / `bor x` | mutable borrow |
| `move x` | transfer ownership; original is invalidated after capture |
| `*` | wildcard — applies the preceding modifier (or `copy` if none) to all variables used in the body that are not explicitly listed |

```zn
[ref *]           // everything by ref
[x, ref *]        // x by copy, everything else by ref
[move x, copy *]  // move x, copy everything else
```

## Lifetime contract
Lambdas are a black box for lifetime inference:
- Ownership/borrow/ref status is encoded in the parameter types
- Outputs are always owned values
- Calling a lambda has no lifetime side-effects on its inputs beyond what the types already describe
