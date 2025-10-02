+++
date = '2025-09-20T10:00:00+08:00'
draft = false
title = 'Rust Memory Safety: Zero-Cost Abstractions'
tags = ['rust', 'memory-safety', 'programming', 'performance']
categories = ['fundamentals']
description = 'How Rust achieves memory safety without garbage collection'
+++

## The Memory Safety Problem

Memory bugs are among the most dangerous and common issues in systems programming:

- **Use-after-free**: Accessing memory that has been deallocated
- **Double-free**: Freeing the same memory twice
- **Buffer overflows**: Writing beyond allocated memory bounds
- **Data races**: Concurrent access without synchronization

## Rust's Solution: The Borrow Checker

Rust solves these problems at compile time through its borrow checker, which enforces rules about ownership and borrowing.

### Stack vs Heap

```rust
fn main() {
    // Stack allocated - fixed size, known at compile time
    let x = 42;

    // Heap allocated - dynamic size, runtime allocation
    let s = String::from("hello");

    // x is copied (simple type)
    let y = x;
    println!("x: {}, y: {}", x, y); // Both work

    // s is moved (heap type)
    let t = s;
    // println!("{}", s); // Error: value moved
    println!("{}", t); // Only t works
}
```

## No Null Pointer Exceptions

Rust doesn't have null. Instead, it uses `Option<T>`:

```rust
fn find_user(id: u32) -> Option<User> {
    if id == 1 {
        Some(User { name: "Alice".to_string() })
    } else {
        None
    }
}

fn main() {
    match find_user(1) {
        Some(user) => println!("Found: {}", user.name),
        None => println!("User not found"),
    }
}
```

The compiler forces you to handle the `None` case.

## Preventing Data Races

Rust's type system prevents data races at compile time:

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();
            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}
```

`Arc` provides thread-safe reference counting, and `Mutex` ensures exclusive access.

## Smart Pointers

Rust provides several smart pointer types:

```rust
use std::rc::Rc;
use std::cell::RefCell;

// Reference counting
let shared = Rc::new(vec![1, 2, 3]);
let shared2 = Rc::clone(&shared);

// Interior mutability
let data = RefCell::new(5);
*data.borrow_mut() += 1;
```

## Zero-Cost Abstractions

All these safety guarantees come with **zero runtime cost**. The checks happen at compile time:

```rust
// This high-level code...
let v = vec![1, 2, 3];
let sum: i32 = v.iter().map(|x| x * 2).sum();

// ...compiles to the same assembly as hand-written loops
```

## Unsafe Rust

When you need it, Rust provides an `unsafe` escape hatch:

```rust
unsafe {
    // Raw pointer dereference
    let mut num = 5;
    let r1 = &num as *const i32;
    println!("{}", *r1);
}
```

Use `unsafe` only when necessary, and encapsulate it in safe APIs.

## Conclusion

Rust proves that memory safety doesn't require a garbage collector or runtime overhead. Through clever use of ownership, borrowing, and lifetime tracking, Rust achieves both safety and performance - a rare combination in systems programming.
