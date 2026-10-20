## Numbers

> Version review: checked against the locally synchronized documentation for the 1.2.0 upgrade. These supplementary notes are not official API documentation; concrete examples were checked with Cangjie 1.2.0; see the [validation scope and results](../EXTRA_VALIDATION.md). See the [reference documentation](../manual/source_zh_cn/basic_data_type/basic_operators.md).

### Operation on numbers

Operation on integers are mostly as usual. Make note of the following.

* Taking remainder uses `%`, only integer types are supported.
* Taking power uses `**`, only `Int64` or `Float64` are supported on the left side. If the
  left side is `Int64`, the right side must be `UInt64`, with the result being `Int64`.
  If the left side is `Float64`, the right side must be `Int64` or `Float64`, with the result
  being `Float64`.

### Priority between operations

The unary minus `-` has higher priority than taking power. This can lead to
some counterintuitive results. Use parenthesis to ensure the right evaluation.
For example:

```cangjie
main() {
    println(-10**4)     //10000
    println(-(10**4))     //-10000
}
```

### Bitwise operations

The bitwise AND and OR uses `&` and `|` as in other languages. The bitwise NOT operation
uses `!` sign (instead of `~`).

```cangjie
main() {
    let a: UInt8 = 5    //00000101
    let b: UInt8 = 9    //00001001
    println("Bitwise NOT of a: ${!a}")      // 250 (binary 11111010)
    println("Bitwise AND of a and b: ${a & b}")      // 1 (binary 00000001)
    println("Bitwise OR of a and b: ${a | b}")      // 13 (binary 00001101)
}
```

### Minimum and maximum possible value of integer

The minimum and maximum values of each integer type is accessed by properties `Min` and `Max`,
after importing `std.math.*`. For example:

```cangjie
import std.math.*

main() {
    println("${Int32.Min}")  // -2147483648
    println("${Int32.Max}")  // 2147483647
    println("${UInt32.Min}")  // 0
    println("${UInt32.Max}")  // 4294967295
}
```

For signed integer minimum values, the positive absolute value is not representable in the same type; check overflow behavior before applying `abs`.

### Absolute value

To find absolute value of a number, use `abs` after importing `std.math.*`.
For example:

```cangjie
import std.math.*

main() {
    println("${abs(-3)}")  // 3
}
```

### Converting floating point number to integer

To convert a floating point number to an integer, use syntax like `Int64(s)` (change
to desired type of target value if necessary). This conversion rounds the floating-point number toward zero.

```cangjie
println(Int64(3.4))  // prints 3
println(Int64(3.6))  // prints 3
```

To obtain better control of rounding, the functions `floor`, `ceil` and `round` are also
available after importing `std.math.*`. These functions still return floating-point numbers,
so another `Int64(.)` is needed. For example:

```cangjie
println(Int64(3.4))  // prints 3
println(Int64(-3.6))  // prints -3
println(Int64(ceil(3.4)))   // prints 4
println(Int64(ceil(-3.4)))   // prints -3
println(Int64(floor(3.4)))  // prints 3
println(Int64(floor(-3.4)))  // prints -4
println(Int64(round(3.4)))  // prints 3
println(Int64(round(-3.4)))  // prints -3
println(Int64(round(-3.6)))  // prints -4
```
