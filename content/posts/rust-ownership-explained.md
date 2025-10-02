+++
date = '2025-10-02T20:44:04+08:00'
draft = false
title = 'Understanding Rust Ownership: A Comprehensive Guide'
tags = ['rust', 'programming', 'memory-management']
categories = ['tutorials']
description = 'Deep dive into Rust ownership system and how it ensures memory safety'
+++

## Introduction

Rust's ownership system is one of its most distinctive features. It enables memory safety without needing a garbage collector, making Rust both safe and fast.

## The Three Rules of Ownership

1. Each value in Rust has an owner
2. There can only be one owner at a time
3. When the owner goes out of scope, the value will be dropped

## Move Semantics

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1; // s1 is moved to s2

    // println!("{}", s1); // This would cause a compile error
    println!("{}", s2); // This works fine
}
```

When we assign `s1` to `s2`, Rust considers `s1` to be no longer valid. This prevents double-free errors.

## Borrowing

Instead of transferring ownership, we can borrow references:

```rust
fn calculate_length(s: &String) -> usize {
    s.len()
}

fn main() {
    let s1 = String::from("hello");
    let len = calculate_length(&s1);
    println!("The length of '{}' is {}.", s1, len);
}
```

## Mutable References

```rust
fn main() {
    let mut s = String::from("hello");
    change(&mut s);
    println!("{}", s);
}

fn change(s: &mut String) {
    s.push_str(", world");
}
```

The key restriction: you can have only one mutable reference to a particular piece of data in a scope.

## Conclusion

Ownership is what makes Rust unique. While it may seem restrictive at first, it prevents entire categories of bugs at compile time.
