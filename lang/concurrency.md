# Concurrency

Zinc's concurrency model is built on three generic type primitives and the `async` function modifier. The ownership and lifetime system (see `lang/memory.md`) enforces safety across all of them — no separate rules are needed for concurrent code.

## Primitives at a glance

| Primitive    | Hot/cold | Purpose                         |
|--------------|----------|---------------------------------|
| `Mutex<T>`   | —        | Safe shared mutable state       |
| `Task<T>`    | Cold     | Lazy deferred computation       |
| `Promise<T>` | Hot      | Already-running async value     |

---

## `Mutex<T>`

A type wrapper that makes it impossible to access the inner value without going through a lock. Enforces correct mutual-exclusion at the type level.

```zn
let m: Mutex<int> = Mutex(0)
```

Access is only possible through the lock API:

```zn
let guard = m.lock()   // acquires the lock
guard.val += 1
// lock released when guard goes out of scope (RAII)
```

| Method     | Returns          | Notes                                                          |
|------------|------------------|----------------------------------------------------------------|
| `.lock()`  | `MutexGuard<T>`  | Blocks until the lock is acquired; releases automatically on drop |

[UNDEC: `tryLock()` returning `MutexGuard<T> | null` for a non-blocking attempt]

`MutexGuard<T>` exposes the inner value through a `.val` field (`borrow T`). The guard cannot outlive the `Mutex<T>` it came from — enforced by lifetime inference.

---

## `Task<T>`

A cold, lazy computation wrapping a zero-argument lambda. Constructing a `Task` does not start it; execution is controlled by which method you call.

```zn
let t: Task<int> = Task(fn () => {
  heavyCompute()
})
// nothing is running yet
```

### Methods

| Method        | Returns      | Behavior                                                                              |
|---------------|--------------|---------------------------------------------------------------------------------------|
| `.run()`      | `Promise<T>` | Spawns the task on the async runtime; returns immediately with a hot `Promise<T>`     |
| `.runSync()`  | `T`          | Calls the lambda directly on the current thread with no async machinery; returns `T`  |
| `.await()`    | `T`          | Shorthand for `.run()` then immediately `await`ing; valid only inside `async` context |

```zn
let p: Promise<int> = t.run()      // spawn, get Promise
let result: int     = t.runSync()  // call inline, get value directly
let result: int     = t.await()    // spawn and wait (inside async fn)
```

The lambda passed to `Task` may capture variables from the surrounding scope. The existing ownership and lifetime rules apply unchanged — the compiler validates captures the same way it does for any lambda.

---

## `Promise<T>`

A hot, already-running async value. Once a `Promise<T>` exists, its computation is in flight.

### `await` keyword

`await` is a unary prefix expression operator. It is only valid on a `Promise<T>` and must appear inside an `async` function body. It suspends the current async context until the Promise settles and produces the resolved value of type `T`.

```zn
let p: Promise<string> = fetchData()
let result: string = await p

// inline
let result: string = await fetchData()
```

### Instance methods

| Method                    | Returns         | Behavior                                                    |
|---------------------------|-----------------|-------------------------------------------------------------|
| `.then(fn(T): U)`         | `Promise<U>`    | Transforms the resolved value; chains a new Promise         |
| `.catch(fn(err): U)`      | `Promise<T\|U>` | Handles a rejection; [UNDEC: error type — see note below]   |
| `.finally(fn(): void)`    | `Promise<T>`    | Runs cleanup regardless of outcome; passes the value through |

### Static methods

| Method                              | Returns                       | Behavior                                                     |
|-------------------------------------|-------------------------------|--------------------------------------------------------------|
| `Promise.all(Promise<T>[])`         | `Promise<T[]>`                | Resolves when all resolve; rejects as soon as any rejects    |
| `Promise.allSettled(Promise<T>[])` | `Promise<SettledResult<T>[]>` | Waits for all regardless of rejection                        |
| `Promise.race(Promise<T>[])`        | `Promise<T>`                  | Resolves/rejects with the first to settle                    |
| `Promise.any(Promise<T>[])`         | `Promise<T>`                  | Resolves with the first to resolve; rejects if all reject    |
| `Promise.resolve(T)`                | `Promise<T>`                  | Wraps an already-known value in a settled Promise            |
| `Promise.reject(err)`               | `Promise<never>`              | Returns an already-rejected Promise                          |

[UNDEC: `Promise.all` with heterogeneous element types — depends on tuple support (`lang/types.md` §Tuple)]

### Error handling

An `async fn` that `throw`s causes its returned `Promise` to reject. `.catch()` intercepts that rejection. This maps directly to TypeScript semantics and is consistent with Zinc's existing `throw` (which gives a call-site type `Never`).

[UNDEC: if Zinc adopts a `Result<T, E>` type, `Promise` may additionally support a union-based error path alongside throw-based rejection]

---

## `async fn`

Marking a function `async` changes its call semantics: calling it **immediately spawns it on the async runtime** and returns a `Promise<T>` where `T` is the declared return type. The body begins executing concurrently from the call point.

```zn
async fn fetchUser(id: int): User {
  let res = await http.get(`/users/${id}`)
  return await res.json<User>()
}

// caller:
let p: Promise<User> = fetchUser(42)   // spawns immediately
let user: User = await p
```

This is equivalent to wrapping the body in a `Task` and immediately calling `.run()`.

### Parameters

Parameters of any ownership kind are valid on `async fn`. Lifetime inference determines what is safe — a `ref` or `borrow` parameter must remain valid for the full duration of the function's async execution, and the compiler rejects cases it cannot prove.

### Return type in source vs at call site

Inside the body, `return x` where `x: T` is the normal form. At the call site, the expression has type `Promise<T>`. No explicit `Promise<T>` appears in the declaration — the `async` modifier is the only annotation needed.

---

## Channels

[UNDEC: typed inter-task message passing primitive (`chan<T>` or similar). Likely included; design pending.]

---

## Interaction with the ownership model

No concurrency-specific rules are added beyond what the ownership and lifetime system already enforces:

- Values captured by a `Task` lambda follow normal lambda capture rules; the compiler validates their lifetimes the same way it does everywhere else.
- `.runSync()` is a plain call; normal single-threaded lifetime rules apply.
- `Mutex<T>` is the only mechanism for sharing mutable state across concurrent tasks in safe Zinc code.