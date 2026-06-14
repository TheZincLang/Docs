# Operators & Precedence
Highest precedence last (parser resolves lowest first in recursive descent).
Parser chain: `parseAssignment → parseTernary → parseLogicalOr → parseLogicalAnd → parseBitwiseOr → parseBitwiseXor → parseBitwiseAnd → parseEquality → parseRelational → parseShift → parseAddSub → parseMulDiv → parseUnary → parsePostfix → parsePrimary`

## Precedence table
| Level        | Operators                                                | Assoc | Parse fn          | AST node         |
|--------------|----------------------------------------------------------|-------|-------------------|------------------|
| 1 (lowest)   | `=` `+=` `-=` `*=` `/=` `%=` `&=` `\|=` `^=` `<<=` `>>=` | right | `parseAssignment` | `AssignmentExpr` |
| 2            | `? :`                                                    | right | `parseTernary`    | `TernaryExpr`    |
| 3            | `\|\|`                                                   | left  | `parseLogicalOr`  | `BinaryExpr`     |
| 4            | `&&`                                                     | left  | `parseLogicalAnd` | `BinaryExpr`     |
| 5            | `\|`                                                     | left  | `parseBitwiseOr`  | `BinaryExpr`     |
| 6            | `^`                                                      | left  | `parseBitwiseXor` | `BinaryExpr`     |
| 7            | `&`                                                      | left  | `parseBitwiseAnd` | `BinaryExpr`     |
| 8            | `==` `!=`                                                | left  | `parseEquality`   | `BinaryExpr`     |
| 9            | `<` `>` `<=` `>=`                                        | left  | `parseRelational` | `BinaryExpr`     |
| 10           | `<<` `>>`                                                | left  | `parseShift`      | `BinaryExpr`     |
| 11           | `+` `-`                                                  | left  | `parseAddSub`     | `BinaryExpr`     |
| 12           | `*` `/` `%`                                              | left  | `parseMulDiv`     | `BinaryExpr`     |
| 13           | unary `!` `-` `~` prefix `++` `--`                       | right | `parseUnary`      | `UnaryExpr`      |
| 14 (highest) | postfix `++` `--` · call `()` · index `[]` · field `.`   | left  | `parsePostfix`    | various          |
[FILL: verify all parse fn names against parser.ts]

## Operator semantics
| Operator  | Notes                                   |
|-----------|-----------------------------------------|
| `/`       | all behavior is identical to C          |
| `%`       | all behavior is identical to C          |
| `<<` `>>` | all behavior is identical to C          |
| `++` `--` | both prefix and postfix forms supported |
[FILL: add any non-obvious behaviors]