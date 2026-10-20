## HashMap

> Version review: checked against the locally synchronized documentation for the 1.2.0 upgrade. These supplementary notes are not official API documentation; concrete examples were checked with Cangjie 1.2.0. See the [reference documentation](../libs/std/collection/collection_package_api/collection_package_class.md#class-hashmapk-v-where-k--hashable--equatablek).

To use the HashMap type, the collection package needs to be imported:

```cangjie
import std.collection.*
```

Keys of a `HashMap` must be hashable. Hashable types include numbers, strings, but not
tuples, `Array`, or `ArrayList`. There are no constraints on values of `HashMap`.

### Initialization

Ways to initialize an `HashMap` include:

```cangjie
let a = HashMap<String, Int64>()  // empty HashMap whose key type is String and value is Int64
let b = HashMap<String, Int64>([("a", 0), ("b", 1), ("c", 2)])  //   creates map {"a": 0, "b": 1, "c": 2}
let c = HashMap<String, Int64>(b)  // use another Collection to initialize a HashMap
let d = HashMap<String, Int64>(10)  // created a HashMap whose key type is String and value is Int64 and capacity is 10
let e = HashMap<Int64, Int64>(10, {x: Int64 => (x, x * x)})  // creates entries (0, 0), (1, 1), ..., (9, 81)

```
### Access elements

When we want to access the element corresponding to the specified key, we can use the subscript syntax to access it (the type of the subscript must be the key type). Using a non-existent key as an index will trigger a runtime exception.

```cangjie
let map = HashMap<String, Int64>([("a", 0), ("b", 1), ("c", 2)])
let a = map["a"]  // a == 0
let b = map["b"]  // a == 1
let c = map["d"]  // Runtime exceptions
```

To access elements while allowing for the possibility of non-existent key, one suggested way is using 'get'
together with `getOrDefault` on the option, for example:

```cangjie
let m = HashMap<String, Int64>([("a", 1), ("b", 2)])
println(m.get("a").getOrDefault({ => 0}))  // 1
println(m.get("c").getOrDefault({ => 0}))  // 0
```

### Iteration

To iterate over a HashMap, use for-loop as follows:

```cangjie
let map = HashMap<String, Int64>([("a", 0), ("b", 1), ("c", 2)])
for ((k, v) in map) {
    println("The key is ${k}, the value is ${v}")
}
```

To get the size of a HashMap mp, use `mp.size`

### Basic operations

To determine whether a key `K` is included in the HashMap, we can use the contains function: `mp.contains(K)`, Returns true if the key exists, false otherwise.

If you need to add a single key-value pair to the end of your HashMap, use the `add` function with the signature

```cangjie
public func add(key: K, value: V): Option<V>
```

If you want to add multiple key-value pairs at the same time, you can also use the `add` function with the signature

```cangjie
public func add(all!: Collection<(K, V)>): Unit
```

When the key does not exist, `add(key, value)` inserts it and returns `None`. When it exists, `add` replaces its value and returns the previous value in `Some`. Iteration order is unspecified:

```cangjie
let map = HashMap<String, Int64>()
map.add("a", 0)  // map contains the element ("a", 0)
map.add("b", 1)  // map contains the elements ("a", 0), ("b", 1)
let map2 = HashMap<String, Int64>([("c", 2), ("d", 3)])
map.add(all: map2)  // map contains the elements ("a", 0), ("b", 1), ("c", 2), ("d", 3)
```

Way to delete elements in a HashMap:
```cangjie
let map = HashMap<String, Int64>([("a", 0), ("b", 1), ("c", 2), ("d", 3)])
map.remove("d")  // map contains the elements ("a", 0), ("b", 1), ("c", 2)
```
