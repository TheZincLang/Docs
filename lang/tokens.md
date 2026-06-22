# Tokens
Source of truth: the compiler repo's `src/lexer/lexerTypes.ts` (`TokenType` enum)

## Keywords
Source of truth: `KEYWORDS_MAP` in the lexer's `const.ts`.

| Keyword                    | TokenType    | Notes                          |
|----------------------------|--------------|--------------------------------|
| `fn` / `func` / `function` | `Fn`         | three interchangeable spellings |
| `let`                      | `Let`        | mutable variable               |
| `const`                    | `Const`      | immutable variable; `const enum` |
| `enum`                     | `Enum`       |                                |
| `struct`                   | `Struct`     |                                |
| `class`                    | `Class`      |                                |
| `extends`                  | `Extends`    | class clause                   |
| `mixin`                    | `Mixin`      | class clause — code reuse      |
| `implements`               | `Implements` | class clause — interfaces      |
| `owns`                     | `Owns`       | class clause                   |
| `serves`                   | `Serves`     | class clause                   |
| `init`                     | `Init`       | class constructor              |
| `public`                   | `Public`     | member modifier                |
| `private`                  | `Private`    | member modifier                |
| `protected`                | `Protected`  | member modifier                |
| `static`                   | `Static`     | member modifier                |
| `override`                 | `Override`   | member modifier                |
| `async`                    | `Async`      | declaration modifier           |
| `import`                   | `Import`     |                                |
| `export`                   | `Export`     |                                |
| `if`                       | `If`         |                                |
| `else`                     | `Else`       |                                |
| `switch`                   | `Switch`     |                                |
| `case`                     | `Case`       |                                |
| `default`                  | `Default`    |                                |
| `for`                      | `For`        | C-style and `for`-in           |
| `while`                    | `While`      |                                |
| `loop`                     | `Loop`       | infinite loop                  |
| `break`                    | `Break`      |                                |
| `continue`                 | `Continue`   |                                |
| `return`                   | `Return`     |                                |

`in`, `from`, `as`, and `types` are **contextual keywords** — they are not in
`KEYWORDS_MAP`, so the lexer emits them as ordinary `Identifier` tokens. The
parser recognises them only by spelling in specific positions: `in` inside a
`for (<ident> in …)` header, and `types` / `as` / `from` inside an `import`
statement (see `lang/statements.md` and `lang/modules.md`).

Booleans are **capitalized**: `True` / `False` lex directly to `BoolLiteral`
(`token.data` = 1 / 0), not to dedicated keyword tokens. There is **no `null`
keyword** in the lexer (a nullable value is the union `T | null` — see
`lang/types.md` — and that syntax is not yet parsed).

## Literals
| Kind            | Example                        | TokenType                                       | `token.data`                          |
|-----------------|--------------------------------|-------------------------------------------------|---------------------------------------|
| Integer         | `42`, `0xFF`, `0b1010`, `0o17` | `IntLiteral`                                     | numeric value                         |
| Double          | `3.14`, `1e9`, `2.0d`          | `DoubleLiteral`                                 | numeric value (default for decimals)  |
| Float           | `3.14f`, hex floats            | `FloatLiteral`                                  | numeric value (`f`/`F` suffix or hex) |
| Char            | `'a'`                          | `CharLiteral`                                   | char code point                       |
| String          | `"hello"`                      | `StringLiteral`                                 | string intern index                   |
| String template | `` `hi ${x}` ``                | `StringTemplate` (+ `StringTemplateStart`/`End`) | intern index of each literal span     |
| Boolean         | `True`, `False`                | `BoolLiteral`                                   | 1 / 0                                 |
| Identifier      | `foo`                          | `Identifier`                                     | ident intern index                    |

A bare decimal (`3.14`, `1e9`) lexes to `DoubleLiteral`; an `f`/`F` suffix and
hex floats (`0x1.8p1`) produce `FloatLiteral`, and a `d`/`D` suffix forces
`DoubleLiteral`.

## String templates
Syntax: `` `text ${expr} text` ``
Lexed as a sequence rather than one token: a `StringTemplate` token for each
literal span, with each interpolation bracketed by `StringTemplateStart` … the
expression's tokens … `StringTemplateEnd`. A template with no interpolations is a
single `StringTemplate` token. At `${` the lexer pushes a
`ScopeExitOperation(closeStringTemplate)` onto its scope stack, so the matching
`}` emits `StringTemplateEnd` instead of `StackClose`. Literal spans are interned
in the string-literal map (`token.data` = intern index).

## Interning
- Identifiers: `Lexer.identifierMap: Map<string, number>` — `token.data` = intern index
- String literals (and template spans): `Lexer.stringLiteralMap: Map<string, number>` — `token.data` = intern index
- Numbers/chars: `token.data` = literal numeric value directly (for floats, there will be a bitcast once the compiler is self-hosting)

## Operators & punctuation
See `lang/operators.md` for the full list with precedence.
