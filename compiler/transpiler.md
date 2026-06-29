# Zinc C Transpiler

Zinc compiles to C as its first backend. Each `.zn` source file maps to a `.h`/`.c` pair; the C compiler (clang/gcc) handles the rest.

## Output layout

```
out/
  foo.h      ← exported declarations (structs, enums, fn signatures)
  foo.c      ← full definitions; #includes foo.h + imported headers
  main.h
  main.c
```

One `.zn` file → one `.h` + one `.c`. No import graph tracking needed at the C level — `#include` handles it.

## File responsibilities

| File  | Contains |
|-------|----------|
| `.h`  | `export`-marked structs, enums, and function signatures |
| `.c`  | All function bodies, non-exported declarations, `#include`s for imports |

A `.zn` file that imports `foo` emits `#include "foo.h"` in its `.c`. The `.h` of the importing file never includes other `.h`s (avoids cycles).

## Type mapping

| Zinc type | C type |
|-----------|--------|
| `int` / `i32` | `int32_t` |
| `i8` / `i16` / `i64` | `int8_t` / `int16_t` / `int64_t` |
| `u8` / `u16` / `u32` / `u64` | `uint8_t` / `uint16_t` / `uint32_t` / `uint64_t` |
| `float` / `f32` | `float` |
| `double` / `f64` | `double` |
| `bool` | `bool` (`<stdbool.h>`) |
| `char` | `char` |
| `string` | `const char*` (v0 — no ownership) |
| `T[]` | `T*` + length (struct wrapper — [UNDEC] exact layout) |
| `struct Foo` | `typedef struct { ... } Foo;` |
| `enum Direction` | `typedef enum { ... } Direction;` |
| `void` | `void` |

## Name mangling

Zinc methods (`foo.bar()`) flatten to `TypeName_methodName(self, ...)` in C.

| Zinc | C |
|------|---|
| `foo.bar(x)` | `Foo_bar(foo, x)` |
| `fn bar(self: Foo)` | `void Foo_bar(Foo self, ...)` |

## Operator mapping

| Zinc | C | Notes |
|------|---|-------|
| `**` (exponentiate) | `pow(a, b)` | requires `<math.h>` |
| `loop {}` | `while (1) {}` | |
| `for x in arr` | `for (size_t i = 0; i < arr.len; i++)` | [UNDEC] array struct layout |
| `&&=`, `\|\|=` | inline if | no C equivalent; expand to `a = a && b` |

## Implementation status

| Stage | Status |
|-------|--------|
| CEmitter skeleton (`cgen.ts`) | done |
| Type emission (`emitType`) | not started |
| Functions + primitives | not started |
| Expressions (math, binary, call, field access) | not started |
| Control flow (if / while / for / switch) | not started |
| Structs | not started |
| Enums | not started |
| Header/source split (`.h` + `.c` per file) | not started |
| Import → `#include` | not started |
| Runner (`compileAndRun`) | done (skeleton) |

## Pipeline integration

`compile.ts` already resolves the full import graph via BFS. After `buildAST()` on each `FileManager`, pass `astTree` to `CEmitter`:

```
FileManager.astTree (Program)
  └─ CEmitter.emit(program) → C source string
       └─ compileAndRun(source, outDir, "clang")
            ├─ writes out/out.c
            ├─ clang out/out.c -o out/out.exe
            └─ executes out/out.exe
```

For the multi-file build, emit all `.h`/`.c` pairs first, then invoke clang once with all `.c` files.

## Compiler requirements

A C compiler must be on PATH. Set `ZINC_CC` env var to override the default (`gcc`).

| Compiler | Install |
|----------|---------|
| clang | `winget install LLVM.LLVM`, add `C:\Program Files\LLVM\bin` to PATH |
| gcc | MSYS2 → `pacman -S mingw-w64-ucrt-x86_64-gcc` |
