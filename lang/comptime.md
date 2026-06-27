# Comptime
Status: **[FILL: parses / type-checks / unimplemented — keep in sync with `../Zinc/CLAUDE.md`]**

Zinc's comptime model rests on three principles:

1. **Types are values.** A type is just an enum value at both compile time and
   runtime, so types can drive logic in either stage.
2. **Pure functions run at comptime.** Any pure function may be evaluated at
   comptime — see [pure functions](#pure-functions).
3. **Definitions are values.** Comptime code can create new definitions of any
   kind and treat existing definitions as ordinary values.

## Types
Types are enums, so a variable can be checked for its type, and an `any` type
exists.

This does **not** mean type information is shipped with every variable. Normally
a variable's type is inferred from the function or definition it came from and
carries no runtime tag. The exception is the `any` type: an `any` value is
(barring optimizer shenanigans) a pointer to a heap-allocated struct that holds
the value plus its type information, so it always carries its type at runtime. To
avoid that overhead, use [generic types](generics.md) instead of `any`.

| Aspect                       | Behavior                                          |
|------------------------------|---------------------------------------------------|
| Type representation          | enum value (same at comptime and runtime)         |
| Type check on a variable     | [FILL: syntax for checking a variable's type]     |
| `any` runtime representation | pointer to heap struct `{ value, type }`          |
| Non-`any` runtime type tag   | none — type is inferred statically                |
| Avoiding `any` overhead      | use generics                                      |
| `typeof` / reflection syntax | [UNDEC]                                            |

## Pure functions
Any operation — **including allocation** — may run in comptime code, as long as
it has no side effects. A side effect is any IO, or any read of time or machine
state (this mostly affects functions that measure time or use RNG).

A function is marked pure with the `pure` [FILL: keyword / modifier — complete
the sentence; the source cuts off at "marked as pure with the \"pure\""].

| Allowed at comptime      | Disallowed at comptime (side effects) |
|--------------------------|---------------------------------------|
| Allocation               | IO                                    |
| [FILL: other allowed ops]| Reading the clock / time measurement  |
|                          | RNG / reading machine state           |

- **Purity marker:** [FILL: exact syntax, e.g. `pure fn name(...)`]
- **Inferred vs. explicit purity:** [UNDEC: is `pure` required, or inferred?]
- **Calling a non-pure function at comptime:** [FILL: error / diagnostic]

## Definitions
Comptime code can use any already-defined class, type, function, etc. — even
when the comptime code actively modifies them.

Because comptime code is compiled before any comptime code runs, the changes
comptime code makes to its own definitions do not take effect until runtime.

- **What can be created:** [FILL: kinds of definitions comptime can emit]
- **Modifying an existing definition:** [FILL: semantics / what's observable]
- **Ordering / staging rule:** changes to definitions apply at runtime, not
  during the comptime pass. [FILL: edge cases]

## Usage
Comptime code is any code used inside a definition; it appears wherever an
expression or field is expected.

```zn
[FILL: minimal example of comptime code in a definition]
```

- **Trigger syntax:** [FILL: is there a `comptime` keyword/marker, or is it
  implicit from context? — [UNDEC] if not yet decided]
- **Valid positions:** expression position, field position, [FILL: others]
- **Invalid positions:** [FILL]

## Open questions
| Question                                   | Status   |
|--------------------------------------------|----------|
| `pure` marker exact syntax                 | [FILL]   |
| Type-check / reflection syntax             | [UNDEC]  |
| Explicit vs. inferred purity               | [UNDEC]  |
| Comptime trigger keyword                   | [UNDEC]  |
