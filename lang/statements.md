# Statements
Grammar: `<rule>` · `[optional]` · `{zero-or-more}` · `A | B` alternation

## Variable declaration
```
("let" | "const") <ident> [":" <type>] "=" <expr>
```
`let` = mutable · `const` = immutable · type annotation optional if inferable

## Assignment
```
<lvalue> <assign-op> <expr>
<assign-op> ::= "=" | "+=" | "-=" | "*=" | "/=" | "%=" | "&=" | "|=" | "^=" | "<<=" | ">>="
```
Valid lvalues: identifier, field access, index expression.

## If / else
```
"if" "(" <expr> ")" <block> ["else" (<if-stmt> | <block>)]
```
The parentheses around the condition are required. Condition is any expression —
[FILL: must it be bool, or is truthy allowed?]

## While
```
"while" "(" <expr> ")" <block>
```
Parentheses around the condition are required.

## For
```
"for" "(" <ident> "in" <expr> ")" <block>                  // for-in (ForInNode)
"for" "(" [<stmt>] ";" [<expr>] ";" [<stmt>] ")" <block>   // C-style (ForNode)
```
Two forms, same as in TS:
- **for-in** binds `<ident>` to each element of the iterable; the binding is
  scoped to the body. AST: `ForInNode` (`{variableId, iterable, body}`).
- **C-style** has three `;`-separated clauses, each optional (`for (;;)` is a
  valid infinite loop). The initializer is a statement (typically a `let`), the
  condition an expression, the update a statement. The initializer's declarations
  live in a header scope shared by the condition, update, and body. AST:
  `ForNode` (`{initializer, condition, update, body}`, any of the first three may
  be `null`).

`in` is a contextual keyword — it is a plain identifier in the lexer, recognised
only in the `for (<ident> in …)` position.

## loop
```
"loop" <block>
```
Infinite loop, can only be exited with `break` / `return` / `throw`.

## Switch / case
```
"switch" "(" <expr> ")" "{" {<case-clause>} "}"
<case-clause> ::= ("case" <expr> | "default") ":" <block>
```
Parentheses around the scrutinee are required. Each `case`/`default` body is a
brace-delimited `<block>` (`: { ... }`), not a bare statement list. `default` may
appear in any position but at most once. [UNDEC: fallthrough semantics — each case
currently parses to a single block body.]

## Break / continue
```
"break"
"continue"
```
Valid only inside `while` / `loop` / `for` / `switch` bodies.

## Return
```
"return" [<expr>]
```
Bare `return` allowed in void functions.

## Expression statement
```
<expr>
```
Any expression can appear as a statement (call, assignment, pre/postfix).

## Block
```
"{" {<stmt>} "}"
```