# Tokens
Source of truth: `../Zinc/src/lexer/lexerTypes.ts` — `TokenType` enum

## Keywords
| Keyword    | TokenType  | Notes                                              |
|------------|------------|----------------------------------------------------|
| `let`      | `Let`      | mutable variable                                   |
| `const`    | `Const`    | immutable variable                                 |
| `fn`       | `Fn`       | function declaration (parser: not yet implemented) |
| `if`       | `If`       |                                                    |
| `else`     | `Else`     |                                                    |
| `while`    | `While`    |                                                    |
| `for`      | `For`      | parser: not yet implemented                        |
| `break`    | `Break`    |                                                    |
| `continue` | `Continue` |                                                    |
| `return`   | `Return`   |                                                    |
| `switch`   | `Switch`   |                                                    |
| `case`     | `Case`     |                                                    |
| `default`  | `Default`  |                                                    |
| `enum`     | `Enum`     |                                                    |
| `import`   | `Import`   |                                                    |
| `export`   | `Export`   |                                                    |
| `true`     | `True`     |                                                    |
| `false`    | `False`    |                                                    |
| `null`     | `Null`     |                                                    |
[FILL: remaining keywords from TokenType]

## Literals
| Kind       | Example   | TokenType       | `token.data`        |
|------------|-----------|-----------------|---------------------|
| Integer    | `42`      | `IntLiteral`    | numeric value       |
| Float      | `3.14`    | `FloatLiteral`  | numeric value       |
| Char       | `'a'`     | `CharLiteral`   | char code point     |
| String     | `"hello"` | `StringLiteral` | string intern index |
| Identifier | `foo`     | `Identifier`    | ident intern index  |
[FILL: verify against TokenType — add any missing literal kinds]

## String templates
Syntax: `` `text ${expr} text` ``
- Lexer uses `ScopeExitOperation` bitmask to distinguish `}` closing an interpolation vs. a block
- [FILL: token sequence emitted for template spans and interpolation boundaries]

## Interning
- Identifiers: `Lexer.identMap: Map<string, number>` — `token.data` = intern index
- String literals: `Lexer.stringMap: Map<string, number>` — `token.data` = intern index
- Numbers/chars: `token.data` = literal numeric value directly

## Operators & punctuation
See `lang/operators.md` for the full list with precedence.
[FILL: list punctuation tokens — `{`, `}`, `(`, `)`, `[`, `]`, `:`, `,`, `.`]