# Modules

## Import
```
"import" "{" <exportedModule> { "," <exportedModule> "}" "from" <string-literal> ["as" <ident>]
"import" * "from" <string-literal> ["as" <ident>] //"as" is optional, but highly recommended to avoid name collisions
```
Example:
```zn
import { furierTransform } from "./math.zn"
import * from "./collections/list.zn" as list
```

## Export
```
"export" <decl>       // export inline with declaration
"export" <ident>      // has to be a comptime constant if the declaration itself is not exported
```
[FILL: check parser.ts for exact export handling]

## Resolution
1. `lexer.findImports()` pre-scans each file for import paths (fast pass, no full lex)
2. `compile.ts` walks the import graph BFS — one `FileManager` per file
3. `FileManager.exports: Node[]` holds the file's exported nodes

## Rules
| Rule             | Value                                                                            |
|------------------|----------------------------------------------------------------------------------|
| Circular imports | allowed if the dependency chain of imported modules isn´t circular               |
| Re-exports       | supported                                                                        |
| Name collision   | "as" can be used to give an imported module a namespace to avoid name collisions |
| Implicit exports | only explicit exports are availible from outside                                 |