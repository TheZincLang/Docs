# Operators & Precedence
Highest precedence last (parser resolves lowest first in recursive descent).
Parser chain: `parseAssignment → parseTernary → parseLogicalOr → parseLogicalAnd → parseEquality → parseComparison → parseBitwiseOr → parseBitwiseXor → parseBitwiseAnd → parseShift → parseTerm → parseFactor → parseExponent → parseUnary → parsePostfix → parsePrimary`

Note: unlike C, equality (`==` `!=`) and comparison (`<` `>` …) bind **looser**
than the bitwise operators — `a & b == c` parses as `a & (b == c)`.

## Precedence table
| Level        | Operators                                                               | Assoc | Parse fn          | AST node                                  |
|--------------|-------------------------------------------------------------------------|-------|-------------------|-------------------------------------------|
| 1 (lowest)   | `=` `+=` `-=` `*=` `/=` `%=` `**=` `&=` `\|=` `^=` `<<=` `>>=` `&&=` `\|\|=` | right | `parseAssignment` | `AssignmentNode`                          |
| 2            | `? :`                                                                   | right | `parseTernary`    | `TernaryNode`                             |
| 3            | `\|\|`                                                                  | left  | `parseLogicalOr`  | `BinaryNode`                              |
| 4            | `&&`                                                                    | left  | `parseLogicalAnd` | `BinaryNode`                              |
| 5            | `==` `!=`                                                               | left  | `parseEquality`   | `BinaryNode`                              |
| 6            | `<` `>` `<=` `>=`                                                       | left  | `parseComparison` | `BinaryNode`                              |
| 7            | `\|`                                                                    | left  | `parseBitwiseOr`  | `BitwiseNode`                             |
| 8            | `^`                                                                     | left  | `parseBitwiseXor` | `BitwiseNode`                             |
| 9            | `&`                                                                     | left  | `parseBitwiseAnd` | `BitwiseNode`                             |
| 10           | `<<` `>>`                                                               | left  | `parseShift`      | `BitwiseNode`                             |
| 11           | `+` `-`                                                                 | left  | `parseTerm`       | `MathNode`                                |
| 12           | `*` `/` `%`                                                             | left  | `parseFactor`     | `MathNode`                                |
| 13           | `**`                                                                    | left  | `parseExponent`   | `MathNode`                                |
| 14           | unary `!` `-` `~` prefix `++` `--`                                      | right | `parseUnary`      | `UnaryNode`                               |
| 15 (highest) | postfix `++` `--` · call `()` · index `[]` · field `.`                  | left  | `parsePostfix`    | `PostfixNode` / `CallNode` / `FieldAccessNode` |

## Operator semantics
| Operator  | Notes                                                          |
|-----------|----------------------------------------------------------------|
| `/`       | all behavior is identical to C                                  |
| `%`       | all behavior is identical to C                                  |
| `<<` `>>` | all behavior is identical to C                                  |
| `**`      | exponentiation; currently parses left-associative (`a**b**c` = `(a**b)**c`) |
| `++` `--` | both prefix and postfix forms supported                        |