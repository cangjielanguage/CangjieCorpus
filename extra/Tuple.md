## Tuple

> Version review: checked against the locally synchronized documentation for the 1.2.0 upgrade. These supplementary notes are not official API documentation; concrete examples were checked with Cangjie 1.2.0; see the [validation scope and results](../EXTRA_VALIDATION.md). See the [reference documentation](../manual/source_zh_cn/basic_data_type/tuple.md).

### Access the elements of a tuple

Elements of a tuple are accessed using the same notation `[]` as for arrays. For example:

```cangjie
var pi = (3.14, "PI")
println(pi[0])  // prints 3.140000
println(pi[1])  // prints PI
```

The index must be concrete numbers. Accessing using a variable is not allowed. For example:

```cangjie
var a = (1.0, 2.0)
for (i in 0..2) {
    println(a[i])  // compiler error
}
```

Instead, write out the print statements explicitly, since the size of the tuple is
known beforehand.

### Assignment of tuples:

```cangjie
let x: (Int64, Float64) = (3, 3.141592)
let y: (Int64, Float64, String) = (3, 3.141592, "PI")
var a: Int64
var b: String
var c: Unit
var f = { => ((1, "abc"), ())}
((a, b), c) = f()  // value of a is 1, value of b is "abc", value of c is '()'
((a, b), _) = ((2, "def"), 3.0)  // value of a is 2, value of b is "def", 3.0 is ignored
let d: (name: String, Int64) = ("banana", 5)  // Error
```

### To iterate over an array of tuples:

```cangjie
var a: Array<(Int64, Int64)> = [(2, 3), (3, 4)]
for (pair in a) {
    println("(${pair[0]}, ${pair[1]})")
}
```

### Immutability of tuples

Tuple types are immutable, i.e. once an instance of a tuple type is defined, its contents can
no longer be updated:

```cangjie
var tuple = (true, false)
tuple[0] = false  // Error, 'tuple element' can not be assigned
```

Instead, create a new tuple and copy over some of the elements:

```cangjie
var tuple = (true, false)
var tuple2 = (false, tuple[1])
```

### Type parameter of the tuple type:

```cangjie
func getFruitPrice(): (name: String, price: Int64) {
    return ("banana", 10)
}
```

### Printing tuples and arrays of tuples

Unfortunately, the tuple type does not implement `ToString`, so it does not have `toString`
functions, nor can it be used in `println` and string interpolation. To print a tuple
or array of tuples, iterate over the elements explicitly. For example:

```cangjie
let a = ("a", 3)
println("${a[0]}, ${a[1]}")  // rather than println(a)
let arr = [("a", 3), ("b", 4)]
for (pair in arr) {
    println("${pair[0]}, ${pair[1]}")
}  // rather than println(arr)
```

### Using tuple in `HashSet` and `HashMap`

Tuples are not Hashable in Cangjie, so it cannot be put into `HashSet` or be used as
keys in a `HashMap`. The following code is incorrect:

```cangjie
let set = HashSet<(Int64, Int64)>()  // compiler error: (Int64, Int64) is not Hashable
set.add((0, 0))
for (pair in set) {
    println("${pair[0]}, ${pair[1]}")
}
```

Instead, create a new structure for the pair. The structure need to implement both
`Hashable` and `Equatable`:

```cangjie
import std.collection.*

struct IntPair <: Hashable & Equatable<IntPair> {
    let x: Int64
    let y: Int64
    public init(x: Int64, y: Int64) {
        this.x = x
        this.y = y
    }
    public func hashCode(): Int64 {
        "${x}_${y}".hashCode()
    }
    public operator func ==(other: IntPair): Bool {
        this.x == other.x && this.y == other.y
    }
    public operator func !=(other: IntPair): Bool {
        !(this == other)
    }
}

main() {
    let set = HashSet<IntPair>()
    set.add(IntPair(0, 0))
    for (pair in set) {
        let (x, y) = (pair.x, pair.y)
        println("(${x}, ${y})")  // prints (0, 0)
    }
}
```
