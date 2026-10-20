## Sorting

> Version review: checked against the locally synchronized documentation for the 1.2.0 upgrade. These supplementary notes are not official API documentation; concrete examples were checked with Cangjie 1.2.0; see the [validation scope and results](../EXTRA_VALIDATION.md). See the [reference documentation](../libs/std/sort/sort_package_api/sort_package_funcs.md).

Sorting is available for `Array`, `ArrayList`, and `List` after importing `std.sort.*`. Use `stable: true` when equal elements must retain their original order.
The basic signature of the `sort` function is

```cangjie
public func sort<T>(data: Array<T>, stable!: Bool = false, descending!: Bool = false): Unit where T <: Comparable<T>
```

An example is shown in the following:

```cangjie
import std.sort.*

main() {
    let arr = [1, 2, 3, 3, 2, 1]
    sort(arr)
    println(arr)
}
```

The `sort` function modifies the array in-place and returns `Unit`. To obtain a new
array that is the sorted version of the old array, make a copy of the old array
first. For example:

```cangjie
let a = [1, 2, 3, 3, 2, 1]
let b = a.clone()
sort(b)
println(a)  // [1, 2, 3, 3, 2, 1]
println(b)  // [1, 1, 2, 2, 3, 3]
```

As `sort` returns `Unit`, it is invalid to access elements of a sorted array.
For example, the following is **incorrect**:

```cangjie
let b = sort(a)
println(b[0])  // compile error: access index 0 on Unit
```

The following is probably intended:

```cangjie
let b = a.clone()
sort(b)
println(b[0])
```

### Sorting from highest to lowest


To sort from highest to lowest, we use the `sort` function in the following way:

```cangjie
let arr = [1, 2, 3, 3, 2, 1]
sort(arr, descending: true)
println(arr)  // [3, 3, 2, 2, 1, 1]
```

### Sorting with custom ordering

Sorting with custom ordering (including the case where the type of values does not
implement `Comparable` interface), use the `sort` function with the following signature:

```cangjie
public func sort<T>(data: Array<T>, by!: (T, T) -> Ordering, stable!: Bool = false, descending!: Bool = false): Unit
```

For example, to sort a list of integer pairs, use a custom comparison function as follows:

```cangjie
let pairs = [(1, 2), (2, 1), (0, 2), (0, 3), (2, 0)]
sort(pairs, by: {p1, p2 =>
    match (p1[0].compare(p2[0])) {
        case Ordering.LT => Ordering.LT
        case Ordering.GT => Ordering.GT
        case _ => p1[1].compare(p2[1])
    }
})
for (pair in pairs) {
    print("(${pair[0]}, ${pair[1]}) ")  // (0, 2) (0, 3) (1, 2) (2, 0) (2, 1)
}
println()
```
