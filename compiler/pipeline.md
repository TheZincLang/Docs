# Compiler Pipeline
Full detail: `../Zinc/CLAUDE.md` § Architecture. This file adds what CLAUDE.md omits.

## Stages
| # | Stage | Fn | Input | Output |
|---|---|---|---|---|
| 1 | Import discovery | `lexer.findImports()` | file path | `string[]` import paths |
| 2 | Lex | `lexer.lexFile()` | file path | `Token[]` |
| 3 | Parse | `parser.parseFile()` | `Token[]` | `Program` AST root |
| 4 | [FILL: typecheck?] | [FILL] | `Program` | [FILL] |
| 5 | [FILL: codegen?] | [FILL] | [FILL] | [FILL] |

## Post-parse (current state)
Currently the pipeline stops after parse. Output: pretty-printed token list + AST to stdout.
[FILL: update when typechecking / codegen is added]

## Entry points
| File | Purpose |
|---|---|
| `src/main.ts` | CLI — reads path arg, calls `compile()` |
| `src/compile.ts` | BFS import walk, creates `FileManager` per file, calls `buildAST()` |
| `src/file/fileManager/fileManager.ts` | Per-file context; owns lexer, parser, tokens, AST |

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
