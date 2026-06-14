# Compiler Errors
Sources: `../Zinc/src/lexer/lexerTypes.ts` (LexerError) · `../Zinc/src/parser/ParserTypes.ts` (ParserError) · `../Zinc/src/global/types/globalTypes.ts` (BugError)

## Error output format
[FILL: describe what a printed error looks like — line number, column, source snippet, caret, message?]
Example:
```
[FILL: paste a sample error output here]
```

## LexerError variants
| Variant | Message / trigger | Example input |
|---|---|---|
| [FILL] | [FILL] | [FILL] |

## ParserError variants
| Variant | Message / trigger | Example input |
|---|---|---|
| [FILL] | [FILL] | [FILL] |

## BugError
Thrown for internal invariant violations — always a compiler bug, not a user error.
| Trigger site | Condition |
|---|---|
| [FILL] | [FILL] |

## Error recovery
[FILL: does the parser attempt to recover and continue, or halt on first error?]
