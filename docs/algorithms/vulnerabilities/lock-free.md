# Lock-free Data Structures

Lock-free structures use atomic operations to avoid locks, enabling high concurrency.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* High-contention scenarios
* Real-time systems
* Multi-threaded counters
* Work queues
* Concurrent caches
```

---

## Atomic Counter

```typescript
class AtomicCounter {
  private value: number;

  constructor(initialValue: number = 0) {
    this.value = initialValue;
  }

  get(): number {
    return Atomics.load(this.value, 0);
  }

  set(newValue: number): void {
    Atomics.store(this.value, 0, newValue);
  }

  increment(): number {
    return Atomics.add(this.value, 0, 1);
  }

  decrement(): number {
    return Atomics.sub(this.value, 0, 1);
  }

  // Compare and swap
  compareAndSwap(expected: number, newValue: number): boolean {
    return Atomics.compareExchange(this.value, 0, expected, newValue) === expected;
  }
}
```

---

## Lock-free Stack

```typescript
class Node<T> {
  value: T;
  next: Node<T> | null;

  constructor(value: T) {
    this.value = value;
    this.next = null;
  }
}

class LockFreeStack<T> {
  private head: Node<T> | null = null;

  push(value: T): void {
    const node = new Node(value);
    node.next = this.head;
    this.head = node;
  }

  pop(): T | null {
    const node = this.head;
    if (!node) return null;

    this.head = node.next;
    return node.value;
  }

  isEmpty(): boolean {
    return this.head === null;
  }
}
```

---

## Compare-and-Swap (CAS) Pattern

```typescript
class CASCounter {
  private value: number;

  constructor(initialValue: number = 0) {
    this.value = initialValue;
  }

  increment(): number {
    let current = this.value;
    while (!this.cas(current, current + 1)) {
      current = this.value;
    }
    return current + 1;
  }

  private cas(expected: number, newValue: number): boolean {
    // In real implementation, use Atomics.compareExchange
    if (this.value === expected) {
      this.value = newValue;
      return true;
    }
    return false;
  }
}
```

---

## Producer-Consumer Queue

```typescript
class LockFreeQueue<T> {
  private queue: T[] = [];
  private enqueue = (item: T) => this.queue.push(item);
  private dequeue = () => this.queue.shift();

  produce(item: T): void {
    this.enqueue(item);
  }

  consume(): T | undefined {
    return this.dequeue();
  }

  size(): number {
    return this.queue.length;
  }
}
```

---

## When to Use Locks Instead

```
USE LOCKS WHEN:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Complex operations spanning multiple fields
* Need strong consistency guarantees
* Simpler code is preferred
* Contention is low

USE LOCK-FREE WHEN:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Simple atomic operations
* Very high contention
* Need maximum throughput
* Can accept weaker guarantees
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
