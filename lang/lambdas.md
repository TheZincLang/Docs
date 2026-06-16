# Lambdas
A lambda is a **callable value** — a function expression that can optionally capture variables from its enclosing scope. There is no user-facing distinction between "lambda" and "closure"; both use the same syntax. The difference is only in codegen (a lambda with no captures compiles to a plain function pointer; one with captures carries an environment).

## Syntax
```
[<capture-list>](<params>) => { <body> }
```

| Part | Description |
|---|---|
| `[<capture-list>]` | Variables captured from the enclosing scope; empty `[]` means no captures |
| `(<params>)` | Input parameters, same syntax as function parameters |
| `=> { <body> }` | Body block; `=>` separates signature from body |

### Type annotation
```
(<param-types>) => <return-type>
```

## Example
```zn
let colorPixel: (color: string) => void = [x, ref y,](color: string) => {
    setColor(x, y, color)
}
```

## Capture list
Each entry in `[...]` names a variable from the enclosing scope. The default capture mode is **copy**.

| Syntax | Meaning |
|---|---|
| `x` | Capture `x` by copy |
| `ref x` | Capture `x` by reference |
| `*` | Wildcard: applies the preceding modifier (or copy if none) to all captured variables not otherwise listed |

The wildcard `*` acts as a catch-all for any variable used in the body that is not explicitly named. A modifier placed before `*` sets the default mode for those variables.

```zn
// All captures by ref except x, which is copied explicitly
[x, ref *](msg: string) => { log(x, y, z, msg) }

// All captures by copy (default) — equivalent to [*]
[x, y](msg: string) => { log(x, y, msg) }
```

## Lifetime contract
Lambdas are a **black box for lifetime inference**:
- Ownership/borrow/ref status is encoded in the parameter types
- Outputs are always owned values
- Calling a lambda has no lifetime side-effects on its inputs beyond what the types already describe

The inference system never inspects a lambda's body to track lifetimes at the call site.

## Status
[FILL: planned / in progress / implemented]
