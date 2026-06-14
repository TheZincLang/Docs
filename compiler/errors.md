# Compiler Errors
Sources: `../Zinc/src/lexer/lexerTypes.ts` (LexerError) · `../Zinc/src/parser/ParserTypes.ts` (ParserError) · `../Zinc/src/global/types/globalTypes.ts` (BugError)

## Error output format
[UNDEC]
Example:
```
[UNDEC]
```

## LexerError variants
| Variant | Message / trigger | Example input |
|---------|-------------------|---------------|
| [FILL]  | [FILL]            | [FILL]        |

## ParserError variants
| Variant | Message / trigger | Example input |
|---------|-------------------|---------------|
| [FILL]  | [FILL]            | [FILL]        |

## BugError
Thrown for internal invariant violations — always a compiler bug, not a user error.

| Trigger site | Condition |
|--------------|-----------|
| [FILL]       | [FILL]    |

## Error recovery
won’t exist, there will be an LSP server in the far future that can provide diagnostics as the user types, but for now the compiler will just throw on the first error encountered and exit with a non-zero code.