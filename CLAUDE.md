# Zinc Docs — Agent Index
Separate repo. Companion compiler repo: `../Zinc` (CLAUDE.md: `../Zinc/CLAUDE.md`)

## File map
| Topic                                             | File                   |
|---------------------------------------------------|------------------------|
| Language overview, goals, paradigm, status        | `lang/overview.md`     |
| Token types, literals, keywords, string templates | `lang/tokens.md`       |
| Type system, TypeKind enum, rules                 | `lang/types.md`        |
| Operators, precedence table, semantics            | `lang/operators.md`    |
| Statement grammar (all statement forms)           | `lang/statements.md`   |
| Function declaration and call syntax              | `lang/functions.md`    |
| Import/export, module resolution                  | `lang/modules.md`      |
| Enum syntax and AST representation                | `lang/enums.md`        |
| Compiler error codes and messages                 | `compiler/errors.md`   |
| Pipeline stages and orchestration                 | `compiler/pipeline.md` |
| Annotated .zn code examples                       | `examples/snippets.md` |

## Conventions
- `[FILL]` = unfilled placeholder; fill with actual value, then remove the marker
- `[UNDEC]` = undecided / open design question; discuss and decide, then replace with actual value and remove the marker
- Grammar: `<rule>` non-terminal · `[x]` optional · `{x}` zero-or-more · `|` alternation
- Tables preferred over prose; no decorative content
- Keep implementation-status fields in sync with `../Zinc/CLAUDE.md`