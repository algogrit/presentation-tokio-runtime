layout: true

.signature[@algogrit]

---

class: center, middle

# Tokio Runtime

Gaurav Agarwal

---
class: center, middle

Tokio is an asynchronous runtime for the Rust programming language.

---
class: center, middle

It provides the building blocks needed for writing networking applications.

---
class: center, middle

It gives the flexibility to target a wide range of systems, from large servers with dozens of cores to small embedded devices.

---

At a high level, Tokio provides a few major components:

- A multi-threaded runtime for executing asynchronous code.

- An asynchronous version of the standard library.

- A large ecosystem of libraries.

---
class: center, middle

## the `Runtime`

---
class: center, middle

`#[tokio::main]`

---
class: center, middle

It transforms the `async fn main()` into a synchronous `fn main()` that initializes a runtime instance and executes the async main function.

---

Equivalent to:

```rust
fn main() {
  let mut rt = tokio::runtime::Runtime::new().unwrap();
  rt.block_on(async {
      println!("hello");
  })
}
```

---
class: center, middle

Provides ability to create `async` task and `await` on the value (a future) returned by the task.

---
class: center, middle

A future is a value that implements the `std::future::Future` trait provided by the standard library.

---
class: center, middle

They are values that contain the in-progress asynchronous computation.

With asynchronous Rust, cancellation is performed by dropping a future.

---
class: center, middle

## [Internals](https://tokio.rs/tokio/tutorial/async)

---
class: center, middle

## Spawning

---
class: center, middle

A Tokio task is an asynchronous green thread.

---
class: center, middle


They are created by passing an async block to `tokio::spawn`.

---
class: center, middle

When spawning tasks, the spawned async expression must own all of its data.

---
class: center, middle

## Shared State

---
class: center, middle

There are a couple of different ways to share state in Tokio.

---
class: center, middle

### Guard the shared state with a Mutex

---
class: center, middle

`std::sync::Mutex` vs `tokio::sync::Mutex`

---
class: center, middle

A common error is to unconditionally use `tokio::sync::Mutex` from within async code. An async mutex is a mutex that is locked across calls to `.await`.

---
class: center, middle

This will not only block the current task but it will also block all other tasks scheduled on the current thread.

---
class: center, middle

### Spawn a task to manage the state and use message passing to operate on it

---
class: center, middle

via Channels

---
class: center, middle

## Channels

---
class: center, middle

`use tokio::sync::mpsc;`

---

Tokio provides a number of channels, each serving a different purpose.

- `mpsc`: multi-producer, single-consumer channel. Many values can be sent.

- `oneshot`: single-producer, single consumer channel. A single value can be sent.

- `broadcast`: multi-producer, multi-consumer. Many values can be sent. Each receiver sees every value.

- `watch`: multi-producer, multi-consumer. Many values can be sent, but no history is kept. Receivers only see the most recent value.

---
class: center, middle

### select

---
class: center, middle

allows waiting on multiple async computations and returns when a single computation completes

---
class: center, middle

> The `select!` macro can handle more than two branches. The current limit is `64` branches.

---

- The `select!` macro randomly picks branches to check first for readiness.

- When multiple channels have pending values, a random channel will be picked to receive from.

- If when `select!` is evaluated, multiple channels have pending messages, only one channel has a value popped. All other channels remain untouched, and their messages stay in those channels until the next loop iteration. No messages are lost.

---
class: center, middle

## Streams

---
class: center, middle

A stream is an asynchronous series of values.

---
class: center, middle

It is the asynchronous equivalent to Rust's `std::iter::Iterator` and is represented by the `Stream` trait.

---
class: center, middle

Tokio provides stream support in a separate crate: `tokio-stream`.

---
class: center, middle

## I/O

---

### Related

- [Mio](https://github.com/tokio-rs/mio)

- [Bytes](https://github.com/tokio-rs/bytes)

---
class: center, middle

`use tokio::io::{self, AsyncReadExt, AsyncWriteExt};`

---
class: center, middle

*Demo*: Building an echo TCP server

---
class: center, middle

Code
https://github.com/algogrit/presentation-tokio-runtime

Slides
https://tokio-runtime.slides.algogrit.com
