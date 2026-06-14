# Code Snippets
All examples use `.zn` syntax. Verified working unless marked `[FILL]` or `[planned]`.

## Variable declaration
```zn
let x: i32 = 42
const name = "zinc"
let flag = true
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
if x > 0 {
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
  case 0:
    ...
  case 1:
    ...
  default:
    ...
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
import * from "./collections/list" as list
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

## Function declaration [planned]
```zn
function add(a: i32, b: i32): i32 {
  return a + b
}
```

## For loop [planned]
```zn
for(let i = 0; i < 10; i++) {
  ...
}
```
## loop [planned]
```zn
loop {
  if (condition) {
    break
  }
}
```

