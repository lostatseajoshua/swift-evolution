# Enum with generic cases

* Proposal: [SE-NNNN](NNNN-enum-generic-cases.md)
* Authors: [Joshua Alvarado](https://github.com/alvaradojoshua0)
* Review Manager: TBD
* Status: **PITCH**

*During the review process, add the following fields as needed:*

* Decision Notes: [Rationale](https://lists.swift.org/pipermail/swift-evolution/), [Additional Commentary](https://lists.swift.org/pipermail/swift-evolution/)
* Bugs: [SR-NNNN](https://bugs.swift.org/browse/SR-NNNN), [SR-MMMM](https://bugs.swift.org/browse/SR-MMMM)
* Previous Revision: [1](https://github.com/apple/swift-evolution/blob/...commit-ID.../proposals/NNNN-filename.md)
* Previous Proposal: [SE-XXXX](XXXX-filename.md)

## Introduction

This proposal adds a change to the enumeration type that allows an enum case to cast a generic on its associated value. 

Swift-evolution thread: [Discussion thread topic for that proposal](https://lists.swift.org/pipermail/swift-evolution/)

## Motivation

Enums currently support generics, but they are added onto the type itself. 
This can cause adverse syntax when implementing generics for associated values to be stored along each case.
The enum case holds the associated value (not the enum type itself) so should create its own value constraints.

## Proposed solution

The generic is to be casted on the case of the enum and not on the enum itself.

## Detailed design

Current implementation:

```swift
// enum with two generic types
enum Foo<T: Hashable, U: Collection> {
    case bar(obj: T)
    case baz(obj: U)
}

// U is to be casted but it is not even used
let foo: Foo<String, [String]> = .bar(obj: "hash")

// Creating an optional enum, the generics have to be casted without a value set
// The casting is really not needed as the values should be casted not the enum
var foo1: Foo<String, [String]>?

// Collections don’t look great either
var foos = [Foo<String, [String]>]()
foos.append(.bar(obj:"hash"))
```

Proposed solution

```swift
enum Foo {
    case bar<T: Hashable>(obj: T)
    case baz<U: Collection>(obj: U)
}

// generic type inferred on T
var foo: Foo = .bar(obj: "hash") 

// doesn’t need to cast the generic on the optional enum
// the associated value will hold the cast
var foo1: Foo? 

// This also gives better syntax with collections of enums with associated types
var foos = [Foo]()
foos.append(.bar(obj: "hey")
```

## Source compatibility

This may cause subtle breaking changes for areas in code with generic enum cases. 
The compiler could help with the change by finding the associated
generic and updating the case with the new syntax.

## Alternatives considered

An alternative would be to extend the `associatedtype` keyword to the enum type.

```swift
enum Foo {
    associatedtype T = Hashable
    case bar(obj: T)
}
```
