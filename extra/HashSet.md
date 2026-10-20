## HashSet

> Version review: checked against the locally synchronized documentation for the 1.2.0 upgrade. These supplementary notes are not official API documentation; concrete examples were checked with Cangjie 1.2.0. See the [reference documentation](../libs/std/collection/collection_package_api/collection_package_class.md#class-hashsett-where-t--hashable--equatablet).

To use the HashSet type, the collection package needs to be imported:

```cangjie
import std.collection.*
```

Elements of a `HashSet` must be hashable. Hashable types include numbers, strings, but not
tuples, `Array`, or `ArrayList`.

### Initialization

Ways to initialize a `HashSet` include:

```cangjie
let a = HashSet<String>()  // Created an empty HashSet whose element type is String
let b = HashSet<String>(100)  // Created a HashSet whose capacity is 100
let c = HashSet<Int64>([0, 1, 2])  // Created a HashSet whose element type is Int64, containing elements 0, 1, 2
let d = HashSet<Int64>(c)  // Use another Collection to initialize a HashSet
let e = HashSet<Int64>(10, {x: Int64 => (x * x)})  // Created a HashSet whose element type is Int64 and size is 10. All elements are initialized by specified rule function
```

### Adding elements to a HashSet

Ways to add element to HashSet:

```cangjie
let mySet = HashSet<Int64>()
mySet.add(0)  // mySet contains elements 0
mySet.add(0)  // mySet contains elements 0
mySet.add(1)  // mySet contains elements 0, 1
let li = [2, 3]
mySet.add(all: li)  // mySet contains elements 0, 1, 2, 3
```

### Deleting elements of a HashSet

Ways to delete element to HashSet:

```cangjie
let mySet = HashSet<Int64>([0, 1, 2, 3])
mySet.remove(1)  // mySet contains elements 0, 2, 3
```

### Iterating over a HashSet

To iterate over a HashSet, use for-loop as follows:

```cangjie
for (i in mySet) {
    println(i)  // prints each element; iteration order is unspecified
}
```

### Obtaining size of a HashSet

To get the number of elements in a hashset, we can use the size attribute:

```cangjie
import std.collection.*

main() {
    let mySet = HashSet<Int64>([0, 1, 2])
    if (mySet.size == 0) {
        println("This is an empty hashset")
    } else {
        println("The size of hashset is ${mySet.size}")
    }
}
```

### Checking whether an element is in a HashSet

To determine whether an element is included in a HashSet, you can use the contains
function. Returns true if the element exists, false otherwise:

```cangjie
let mySet = HashSet<Int64>([0, 1, 2])
let a = mySet.contains(0)  // a == true
let b = mySet.contains(-1)  // b == false
```

### Taking the union of two HashSet

Use `add` function to compute union of two `HashSet`s, by adding all elements
of the second set to the first. The signature is

```cangjie
public func add(all!: Collection<T>): Unit
```

For example:

```cangjie
let a = HashSet<Int64>([1, 2, 3, 3])
let b = HashSet<Int64>([2, 3, 4, 4])
a.add(all: b)     // add all elements of b to a, so a is now union of a and b
println(a)      // contains 1, 2, 3, 4; output order is unspecified
```

### Convert an array to set

To convert an `Array` (or `ArrayList`) into a `HashSet`, use `collectHashSet` function.
For example:

```cangjie
let a: Array<Int64> = [1, 1, 2, 3, 3]
let s: HashSet<Int64> = collectHashSet(a)
println(s)      // contains 1, 2, 3; output order is unspecified
```

One common use case is finding the unique elements of an array. To return the result
as a new array, use `toArray` after calling `collectHashSet`. For example:

```cangjie
let a: Array<Int64> = [1, 1, 2, 3, 3]
let b: Array<Int64> = collectHashSet(a).toArray()
println(b)      // contains 1, 2, 3; array order is unspecified
```

Note that when calling `collectHashSet`, it is important to make sure that the type
of elements in the Array are hashable (and equatable). If the type is a generic type,
add the corresponding type constraints. For example, the following function checks
whether an array has unique elements:

```cangjie
func allUnique<T>(arr: Array<T>): Bool where T <: Hashable & Equatable<T> {
    return collectHashSet(arr).size == arr.size
}
```

Note it is important to add constraint `T <: Hashable & Equatable<T>` to the function
in order to use `collectHashSet`.
