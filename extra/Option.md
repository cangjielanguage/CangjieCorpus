## Option

> Version review: checked against the locally synchronized documentation for the 1.2.0 upgrade. These supplementary notes are not official API documentation; concrete examples were checked with Cangjie 1.2.0; see the [validation scope and results](../EXTRA_VALIDATION.md). See the [reference documentation](../libs/std/core/core_package_api/core_package_enums.md#enum-optiont).

The `Option` type is defined using `enum`, which includes two constructors: `Some` and `None`.
The `Some` constructor carries a parameter, indicating that there is a value, while `None`
does not carry any parameters, indicating that there is no value. When you need to represent
that a type might have a value or might not have a value, you can choose to use the `Option` type.

### Use `getOrThrow` to extract `Some` value

If an `Option` value is already known to be `Some(v)` for some `v`, use function `getOrThrow`
to extract `v`. If the option value is `None` instead, an exception is thrown.

For example:

```cangjie
let a: Option<Int64> = Some(3)
println(a.getOrThrow())  // 3
```

### Use `getOrDefault` to extract `Some` value with default case

To extract an `Option` value when it is of the form `Some(v)`, and returning a default
value if it is `None`, use the `getOrDefault` function. For example:

```cangjie
let a: Option<Int64> = Some(3)
println(a.getOrDefault({ => 0}))  // 3
let b: Option<Int64> = None
println(b.getOrDefault({ => 0}))  // 0
```

The default argument is a zero-argument function and is evaluated only for `None`; it is not a direct default value.

### Matching on `Option`

To perform different action depending on the value of an `Option`, use
`match-case` pattern.

```cangjie
func printOption(a: Option<Int64>) {
    match (a) {
        case None => println("a is None")
        case Some(v) => println("a is some value ${v}")
    }
}

main() {
    printOption(None)       // a is None
    printOption(Some(3))    // a is some value 3
}
```

### Check if an `Option` value is `None` or `Some`

A simple way to check if an `Option` value is `None` or `Some` uses `isNone` and `isSome` functions.

```cangjie
main() {
    let b: Option<Int64> = None
    let c: Option<Int64> = Some(5)
    println(b.isNone())   // true
    println(b.isSome())   // false
    println(c.isNone())   // false
    println(c.isSome())   // true
}
```
