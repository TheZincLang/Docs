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
"if" <expr> <block> ["else" (<if-stmt> | <block>)]
```
Condition is any expression — [FILL: must it be bool, or is truthy allowed?]

## While
```
"while" <expr> <block>
```

## For (not yet implemented)
```
"for" "("  <ident> "in" <expr> ")" <block>
"for" "(" <stmt> "," <expr> "," <stmt> ")" <block>
```
basically the same as in TS

## loop loop (not yet implemented)
```
"loop" <block>
```
Infinite loop, can only be exited with `break` / `return` / `throw`.

## Switch / case
```
"switch" <expr> "{" {<case-clause>} ["default" ":" {<stmt>}] "}"
<case-clause> ::= "case" <expr> ":" {<stmt>}
```
fallthrough allowed if no `break` at end of case body.

## Break / continue
```
"break"
"continue"
```
Valid only inside `while` / `for` / `switch` bodies.

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