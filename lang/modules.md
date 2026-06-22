# Modules
Status: both `export` and `import` statements are parsed. Import *discovery* —
finding the files an import refers to — runs as a separate fast pre-pass via
`lexer.findImports()` and drives the BFS build walk (see `compiler/pipeline.md`);
the parser then produces an `ImportNode` for each `import` statement.

## Import
```
"import" ["types"] ("{" <name> {"," <name>} "}" | "*") ["as" <ident>] "from" <string-literal>
```
Clause order follows ES-module convention (and `lexer.findImports`): the
specifier comes first, then an optional `as` namespace, then `from "<path>"`.
`types`, `as` and `from` are contextual keywords (plain identifiers in the lexer).
A trailing `;` is optional. `as` is optional but highly recommended on wildcard
imports to avoid name collisions. `import types …` marks a type-only import.

Example:
```zn
import { furierTransform } from "./math.zn"
import * as list from "./collections/list.zn"
import types { Vector } from "./math.zn"
```
AST node: `ImportNode` (`{kind, typeOnly, names, path, alias}`), where `kind` is
`named` or `wildcard`, `names` holds the interned ids of the imported symbols
(empty for wildcard), `path` is the interned string-literal id of the module
path, and `alias` is the interned id from `as` (or `null`).

## Export
```
"export" <decl>       // export inline with declaration
"export" <ident>      // has to be a comptime constant if the declaration itself is not exported
```
Parser: the `export` case in `parseToken` parses the following declaration
(recursively via `parseToken`), appends the resulting node to
`FileManager.exports: Node[]`, and returns it so it still appears inline in the
AST. There is no separate `ExportNode` and no `Modifier.export` is attached — the
membership in `exports` is what marks a node exported.

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