# Blocking vs non-blocking I/O

> Phase 1 · Tags: `os` `io` `concurrency`

## 1. The concept
- **Blocking I/O**: the calling thread waits (parked by OS) until data is ready.
- **Non-blocking I/O**: the call returns immediately with "not ready"; you poll or get notified later.
- **Async I/O**: you hand the OS a buffer + callback; the OS fills it and calls you back (true async, e.g., Linux io_uring, Windows IOCP).
- **Multiplexing** (`epoll`, `kqueue`, `select`): one thread watches many file descriptors and reacts when any are ready.

## 2. The rule / the why
- Blocking is **simple to read** but ties up a thread per connection. At 10k connections that's 10k threads → context-switch storm.
- Non-blocking/multiplexing handles 100k+ connections on a handful of threads (Netty, Nginx). The price: callback-style code and trickier debugging.
- Virtual threads (Java 21) give you blocking-style code with non-blocking-style throughput by unmounting on I/O.

## 3. Java-specific behavior
- `java.io.*` = blocking, one thread per stream.
- `java.nio.channels.*` (since 1.4) = non-blocking + `Selector` (multiplexing over `epoll`/`kqueue`).
- `java.nio.channels.AsynchronousSocketChannel` = true async with `CompletionHandler`.
- Netty wraps NIO into an event-loop framework; Spring WebFlux sits on top of Reactor + Netty.
- Virtual threads in Java 21 internally use NIO non-blocking calls while your code reads as blocking.

```java
// Old, blocking
in.read(buf);
// NIO non-blocking
channel.configureBlocking(false);
channel.register(selector, SelectionKey.OP_READ);
```

## 4. System design angle
- Nginx (event loop) holds tens of thousands of idle keep-alive connections cheaply; Apache prefork can't.
- Kafka brokers use NIO + zero-copy `sendfile()` to push from disk to socket without copying through user space.
- Database drivers are usually blocking → drove the rise of reactive frameworks; virtual threads now make that less necessary.

## 5. Common mistakes / traps
- Mixing blocking calls inside a reactive pipeline → starves the event loop (only N threads).
- Using NIO without backpressure → unbounded buffers, OOM under load.
- Believing async = fast. Async = scalable. A single call is often *slower* than blocking due to overhead.
- Forgetting that file I/O on Linux is mostly blocking even via NIO (only sockets are truly non-blocking pre-io_uring).

## 6. Revision checklist
- Blocking vs non-blocking vs async in one line each: ______
- Why one event-loop thread can handle 100k connections: ______
- Why virtual threads kill the reactive use case for most apps: ______
- The "blocking call in event loop" trap: ______
