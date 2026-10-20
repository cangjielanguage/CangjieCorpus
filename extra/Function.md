## Functions

> Version review: checked against the locally synchronized documentation for the 1.2.0 upgrade. These supplementary notes are not official API documentation; concrete examples were checked with Cangjie 1.2.0. See the [reference documentation](../manual/source_zh_cn/function/define_functions.md).

### Return value after while loop

When a function contains a while loop, sometimes it is necessary to place an
explicit return statement to make clear the return type of the function, even
if the function always returns within the loop. For example, the following
code does not compile:

```cangjie
func whileLoopEx(n: Int64): Int64 {
    var counter = n
    var result = 1
    while (true) {
        if (counter <= 0) {
            return result
        }
        counter -= 1
        result *= 2
    }                           // expected return type Int64, found Unit
}
```

Instead, add a return statement with the right type, even if it will never
be reached:

```cangjie
func whileLoopEx(n: Int64): Int64 {
    var counter = n
    var result = 1
    while (true) {
        if (counter <= 0) {
            return result
        }
        counter -= 1
        result *= 2
    }
    return 0      // OK because compiler can now check the return type is Int64
}
```
