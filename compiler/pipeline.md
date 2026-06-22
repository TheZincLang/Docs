# Compiler Pipeline
Full detail: the compiler repo's `CLAUDE.md` § Architecture. This file adds what that file omits.

## Stages
Stages 1–3 are implemented. Stages 4–5 are planned and not yet wired up.

| # | Stage                      | Fn                    | Input     | Output                                                           |
|---|----------------------------|-----------------------|-----------|------------------------------------------------------------------|
| 1 | Import discovery           | `lexer.findImports()` | file path | `string[]` import paths                                          |
| 2 | Lex                        | `lexer.lexFile()`     | file path | `Token[]`                                                        |
| 3 | Parse                      | `parser.parseFile()`  | `Token[]` | `Program` AST root                                               |
| 4 | resolve imports *(planned)*| —                     | `Program` | `Program`                                                        |
| 5 | type checking *(planned)*  | `typeChecker.check()` | `Program` | void (only throws when types are wrong)                          |

The current driver (`compile.ts`) runs stage 1 across the whole import graph (BFS),
then runs stages 2–3 per file via `FileManager.buildAST()` and pretty-prints the
tokens and AST. `import` *statements* now parse into `ImportNode`s (stage 3), but
binding those imports to the exporting files' symbols (stage 4) and type checking
(stage 5) are not wired up yet — they remain design intent.


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
Interning maps live on `Lexer` — they are **per-file**, not global. Each
`FileManager` owns its own `Lexer`, so intern indices are **local to a file**: the
same identifier or string gets independent indices in different files, and an index
is only meaningful alongside the `Lexer` that produced it (its `getSymbolTable()`
reverse-maps index → spelling). Cross-file symbol resolution will need its own
mapping once imports are wired up.
