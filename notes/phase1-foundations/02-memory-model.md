# Memory model: stack, heap, virtual memory

> Phase 1 · Tags: `os` `jvm`

## 1. The concept
- **Stack**: per-thread, LIFO, stores call frames (locals, return addresses). Fast, auto-freed on return.
- **Heap**: shared region for dynamically allocated objects. Managed by GC in Java, manually in C/C++.
- **Virtual memory**: OS abstraction giving each process a private linear address space, mapped to physical RAM via the page table. Backed by disk (swap/page file) when RAM is tight.
- **Page cache**: OS-level cache of disk blocks in unused RAM — why a second read of the same file is "free".

## 2. The rule / the why
- Stack is fast because allocation is just bumping a pointer. But it's small (~1 MB/thread) and dies with the call.
- Heap exists for objects whose lifetime exceeds the call that created them.
- Virtual memory exists so processes don't see each other and so you can address more memory than you physically have.
- Without page cache: every disk read is slow. With it: most "disk" reads are RAM reads.

## 3. Java-specific behavior
- Primitives and references live on the stack; objects live on the heap.
- JVM heap is divided into Young (Eden + 2 Survivors) and Old generations (generational hypothesis: most objects die young).
- Off-heap memory: `ByteBuffer.allocateDirect`, MapDB, Netty pools — bypasses GC.
- Metaspace (since Java 8) holds class metadata, replaces PermGen, grows in native memory.
- Useful flags: `-Xms`, `-Xmx`, `-XX:MaxMetaspaceSize`, `-XX:MaxDirectMemorySize`.

## 4. System design angle
- DB engines bypass the OS page cache (PostgreSQL uses both; MySQL InnoDB has its own buffer pool) for predictable behavior.
- Memory-mapped files (`mmap`) let you "read" multi-GB files by paging on demand — used by Kafka log segments and Lucene.
- Containers: set `-XX:MaxRAMPercentage` so the JVM respects cgroup limits, otherwise OOMKilled.

## 5. Common mistakes / traps
- `StackOverflowError` from deep recursion — convert to iteration or trampoline.
- Holding references in static collections → memory leak (objects never GC'd).
- Setting `-Xmx` equal to container limit → JVM has no headroom for non-heap (Metaspace, direct buffers, thread stacks) → OOMKilled.
- Assuming `free` memory in `top` is "available" — page cache shows as used but is reclaimable.

## 6. Revision checklist
- Stack vs heap in one line: ______
- Why virtual memory is faster than you'd think on second access: ______
- One JVM flag to set in a Docker container: ______
- One classic Java memory leak pattern: ______
