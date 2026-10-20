## String

> Version review: checked against the locally synchronized documentation for the 1.2.0 upgrade. These supplementary notes are not official API documentation; concrete examples were checked with Cangjie 1.2.0; see the [validation scope and results](../EXTRA_VALIDATION.md). See the [reference documentation](../libs/std/core/core_package_api/core_package_structs.md#struct-string).

### Visit characters in String as Rune values:

Use the `runes()` method to iterate characters in the String as Rune values.
For example, to determine whether a string consists entirely of lower-case letters,
we can write:

```cangjie
func isLowerWord(s: String): Bool {
    for (ch in s.runes()) {
        if (ch < r'a' || ch > r'z') {
            return false
        }
    }
    return true
}

main() {
    println(isLowerWord("hello"))  // true
    println(isLowerWord("Hello"))  // false
}
```

### String initialization

Ways to initialize an `String` include:

```cangjie
// Single and double quotes, use of escape characters
let s1: String = ""
let s2 = 'Hello Cangjie Lang'
let s3 = "\"Hello Cangjie Lang\""
let s4 = 'Hello Cangjie Lang\n'

// Triple quotes for multi-line strings
let s5: String = """
    """
let s6 = '''
    Hello,
    Cangjie Lang'''

// Use the same number of # symbol before and after a string, then the
// content of the string is not escaped.
let s7: String = #""#
let s8 = ##'\n'##
let s9 = ###"
    Hello,
    Cangjie
    Lang"###
```

### Size

`str.size` returns the number of UTF-8 bytes, not the number of Unicode scalar values or user-perceived characters. To count Rune values, use `str.toRuneArray().size` or `str.runes() |> count` after importing `std.collection.*`. Neither count is a grapheme-cluster count.

For example, `"你好".size` is 6, while `"你好".toRuneArray().size` is 2.

### Join

To join a list of  strings, use `String.join` function. For example:

```cangjie
let s = String.join(["a", "b", "c"], delimiter: ",")
println(s)  // a,b,c
```

### The operations supported by the string type

The string type supports comparison using relational operators and concatenation using the + operator.

```cangjie
main() {
    let s1 = "abc"
    var s2 = "abd"
    println(s1 == s2)  // false
    println(s1 + s2)  // abcabd
    println(s1 < s2)  // true
    println(s2 < s1)  // false
}
```

### ToString interface

User-defined types can implement the `ToString` interface, so it has a `toString` method and can
be used in print statement. For example, the following defines class `Point` that implements `ToString`:

```cangjie
class Point <: ToString {
    let x: Int64
    let y: Int64
    public init(x: Int64, y: Int64) {
        this.x = x
        this.y = y
    }
    public func toString(): String {
        "Point(${this.x}, ${this.y})"
    }
}

main() {
    println(Point(2, 3))  // Point(2, 3)
    println("Point is: ${Point(1, 2)}")  // Point is: Point(1, 2)
}
```

For types without a `ToString` implementation, print their components explicitly. Tuple printing is described in [Tuple](Tuple.md).

### Converting integers to String

Integers and other primitive types can be converted into `String` using the `toString` method,
since they implement the `ToString` interface. For example:

```cangjie
let N = 123
let strN = N.toString()
println("strN = ${strN}, second Rune is ${Rune(strN[1])}")  // strN = 123, second Rune is 2
```

### String interpolation

Any expressions that can be printed (implemented `ToString` interface) can be interpolated inside
a string using the `${...}` syntax. For example:

```cangjie
main() {
    let fruit = "apples"
    let count = 10
    let s3 = "There are ${count * count} ${fruit}"
    println(s3)  // There are 100 apples
    
    let r = 2.4
    let s4 = "Area of circle with radius ${r} is ${let PI = 3.141592; PI * r * r}"
    println(s4)  // Area of circle with radius 2.400000 is 18.095570
}
```

### Parsing a string to integer

To parse a string into integers or other primitive types, import the `std.convert.*` package,
then use `parse` function. For example:

```cangjie
import std.convert.*

main() {
    let v = Int64.parse("123")
    let w = Int64.parse("234")
    println("v = ${v}, w = ${w}, v + w = ${v + w}")  // v = 123, w = 234, v + w = 357
}
```

Integer parsing supports an explicit radix through `std.convert.*`:

```cangjie
let binary = Int64.parse("1010", radix: 2)  // 10
let optional = Int64.tryParse("102", radix: 2)  // None: invalid binary digit
```

`parse` throws for invalid input; use `tryParse` when a failed conversion should return `None`.

### Slice of a String

To obtain a slice (substring) of a string, use range indexing. Indices are UTF-8 byte offsets and slice boundaries must lie on valid character boundaries. The ASCII examples below therefore also match character positions:

```cangjie
main() {
    let words = "Hello, World"
    println(words[2..])  // llo, World
    println(words[..5])  // Hello
    println(words[2..5])  // llo
    println(words[2..=5])  // llo,
}
```

### Convert `String` to array of Runes

To obtain the list of characters (of type Rune) from a string for further processing,
use either `runes` or `toRuneArray`.

The function `toRuneArray` converts string to `Array<Rune>`, so it is possible to
directly access the `Rune` value at particular index. For example:

```cangjie
let s = "hello"
let arr = s.toRuneArray()
println(arr)  // [h, e, l, l, o]
println(arr[1])  // e
```

The function `runes` returns an `Iterator<Rune>`, and is more well-suited
for further stream-based processing, for example in for loops and as inputs to
functions like `all` and `filter`. For example:

```cangjie
import std.collection.*

main() {
    let s = "hello"
    for (c in s.runes()) {
        print("${c} ")
    }  // h e l l o
    println()
    println(s.runes() |> filter {c => c == r'l'} |> count)  // 2
}
```
