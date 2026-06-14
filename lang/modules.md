# Modules

## Import
```
"import" <string-literal>
```
Example: `import "./math"` — path is relative, no `.zn` extension needed.

## Export
```
"export" <decl>       // export inline with declaration
"export" <ident>      // [FILL: verify — re-export by name?]
```
[FILL: check parser.ts for exact export handling]

## Resolution
1. `lexer.findImports()` pre-scans each file for import paths (fast pass, no full lex)
2. `compile.ts` walks the import graph BFS — one `FileManager` per file
3. `FileManager.exports: Node[]` holds the file's exported nodes

## Rules
| Rule | Value |
|---|---|
| Circular imports | [FILL: allowed? error? silently ignored?] |
| Re-exports | [FILL: supported?] |
| Qualified access | [FILL: `module.name` syntax?] |
| Name collision | [FILL: error? shadowing?] |
| Implicit exports | [FILL: everything exported, or only explicit?] |