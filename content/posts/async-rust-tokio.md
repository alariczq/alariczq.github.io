+++
date = '2025-09-28T15:30:00+08:00'
draft = false
title = 'Getting Started with Async Rust and Tokio'
tags = ['rust', 'async', 'tokio', 'concurrency']
categories = ['tutorials']
description = 'Learn how to write asynchronous code in Rust using the Tokio runtime'
+++

## Why Async in Rust?

Asynchronous programming allows you to handle multiple tasks concurrently without blocking. Rust's async/await syntax, combined with the Tokio runtime, provides a powerful foundation for building high-performance applications.

## Setting Up Tokio

Add Tokio to your `Cargo.toml`:

```toml
[dependencies]
tokio = { version = "1.35", features = ["full"] }
```

## Your First Async Function

```rust
use tokio::time::{sleep, Duration};

#[tokio::main]
async fn main() {
    println!("Starting async operation...");

    async_task().await;

    println!("Async operation completed!");
}

async fn async_task() {
    sleep(Duration::from_secs(2)).await;
    println!("Task finished after 2 seconds");
}
```

The `#[tokio::main]` macro sets up the Tokio runtime for you.

## Concurrent Execution with join!

```rust
use tokio::join;

async fn fetch_user() -> String {
    sleep(Duration::from_secs(1)).await;
    "User data".to_string()
}

async fn fetch_posts() -> String {
    sleep(Duration::from_secs(1)).await;
    "Post data".to_string()
}

#[tokio::main]
async fn main() {
    let (user, posts) = join!(fetch_user(), fetch_posts());
    println!("User: {}, Posts: {}", user, posts);
}
```

Both functions run concurrently, completing in ~1 second instead of 2.

## Spawning Tasks

```rust
use tokio::task;

#[tokio::main]
async fn main() {
    let handle = task::spawn(async {
        sleep(Duration::from_secs(1)).await;
        "Result from spawned task"
    });

    let result = handle.await.unwrap();
    println!("{}", result);
}
```

## Error Handling

```rust
use std::io;

async fn fetch_data() -> Result<String, io::Error> {
    // Simulated async operation
    Ok("Data fetched successfully".to_string())
}

#[tokio::main]
async fn main() {
    match fetch_data().await {
        Ok(data) => println!("{}", data),
        Err(e) => eprintln!("Error: {}", e),
    }
}
```

## Best Practices

1. **Avoid blocking operations**: Use async alternatives for I/O operations
2. **Use `tokio::spawn` for CPU-bound tasks**: Prevent blocking the runtime
3. **Handle errors properly**: Use `Result` types with async functions
4. **Consider using channels**: For communication between tasks

## Conclusion

Tokio makes async programming in Rust accessible and powerful. Start small, experiment with the examples, and gradually build more complex async applications.
