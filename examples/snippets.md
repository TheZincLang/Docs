# Code Snippets
All examples use `.zn` syntax. Verified working unless marked `[FILL]` or `[planned]`.

## Variable declaration
```zn
let x: i32 = 42
const name = "zinc"
let flag = True
```

## String template
```zn
let msg = `Hello ${name}, count = ${x + 1}`
```

## Arithmetic & operators
```zn
let a = (x * 2 + 3) % 7
let b = x << 2 | 0xFF
let c = x >= 0 ? x : -x
```

## If / else
```zn
if (x > 0) {
  return x
} else if (x < 0) {
  return -x
} else {
  return 0
}
```

## While loop
```zn
let i = 0
while (i < 10) {
  i += 1
}
```

## Switch
```zn
switch (x) {
  case 0: {
    ...
  }
  case 1: {
    ...
  }
  default: {
    ...
  }
}
```

## Enum
```zn
enum Direction { North, South, East, West }
enum Status { Ok = 0, Err = 1 }
```

## Import
```zn
import { furierTransform } from "./math"
import * as list from "./collections/list"
import types { Vector } from "./math"
```

## Function call (receiver: any expression)
```zn
let result = add(a, b)
let len = arr.length()
let c = obj.method(x, y + 1)
```

## Postfix / prefix operators
```zn
i++
--j
let v = arr[i]
let f = obj.field
```

## Function declaration
```zn
function add(a: i32, b: i32): i32 {
  return a + b
}
```

## For loop
```zn
// C-style: three ;-separated clauses, each optional
for (let i = 0; i < 10; i++) {
  ...
}

// for-in: iterate elements
for (item in items) {
  ...
}
```
## loop
```zn
loop {
  if (condition) {
    break
  }
}
```

## Array literals
```zn
let empty = []
let primes = [2, 3, 5, 7, 11]
let nested = [[1, 2], [3, 4]]
let first = [10, 20, 30][0]   // literal followed by index
```

## Lambdas / closures
```zn
let inc = (x: int): int => {
  return x + 1
}

// capture list: copy by default, `ref`/`borrow`/`move` to change mode, `*` wildcard
let adder = [base, ref log](x: int): int => {
  return x + base
}
let sumAll = [ref *](a: int, b: int) => {
  return a + b
}
```

