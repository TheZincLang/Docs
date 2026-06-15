# Compiler Pipeline
Full detail: `../Zinc/CLAUDE.md` § Architecture. This file adds what CLAUDE.md omits.

## Stages
| # | Stage            | Fn                    | Input     | Output                                                           |
|---|------------------|-----------------------|-----------|------------------------------------------------------------------|
| 1 | Import discovery | `lexer.findImports()` | file path | `string[]` import paths                                          |
| 2 | Lex              | `lexer.lexFile()`     | file path | `Token[]`                                                        |
| 3 | Parse            | `parser.parseFile()`  | `Token[]` | `Program` AST root                                               |
| 4 | resolve imports  | handled in compile.ts | `Program` | `Program`                                                        |
| 5 | type checking    | typeChecker.check()   | `Program` | void (doesn´t output anything, only throws when types are wrong) |


## Entry points
| File                                  | Purpose                                                             |
|---------------------------------------|---------------------------------------------------------------------|
| `src/main.ts`                         | CLI — reads path arg, calls `compile()`                             |
| `src/compile.ts`                      | BFS import walk, creates `FileManager` per file, calls `buildAST()` |
| `src/file/fileManager/fileManager.ts` | Per-file context; owns lexer, parser, tokens, AST                   |

## FileManager ownership
```
FileManager
  ├── Lexer        (holds back-ref to FileManager)
  ├── Parser       (holds back-ref to FileManager)
  ├── Token[]
  ├── Program      (AST root)
  └── exports: Node[]
```

## Identifier / string interning scope
Interning maps live on `Lexer` — they are **per-file**, not global.
[FILL: confirm — are intern indices stable across files, or local only?]
