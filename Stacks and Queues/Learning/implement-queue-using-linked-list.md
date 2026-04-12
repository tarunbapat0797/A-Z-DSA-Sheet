# Implement Queue Using Linked List

## Problem

Design a queue data structure using a linked list that supports the following operations:

- `push(x)` / `enqueue(x)` — Insert element `x` at the rear of the queue
- `pop()` / `dequeue()` — Remove and return the front element
- `peek()` — Return the front element without removing it
- `isEmpty()` — Check if the queue is empty

---

## Approach

A queue follows **FIFO (First In, First Out)** order.

Using a linked list removes the fixed-size limitation of an array queue — the queue grows and shrinks dynamically. We maintain two pointers:

- `head` — points to the front node (element to be dequeued next)
- `tail` — points to the rear node (where new elements are appended)

```
push(10) → push(20) → push(30)

head                      tail
 ↓                          ↓
[10] → [20] → [30] → null

pop() removes from head (FIFO)
push() appends at tail
```

### Operations:

| Operation   | Action                                               | Time Complexity |
| ----------- | ---------------------------------------------------- | --------------- |
| `push(x)`   | Create new node, link to `tail.next`, advance `tail` | O(1)            |
| `pop()`     | Save `head.data`, advance `head` to `head.next`      | O(1)            |
| `peek()`    | Return `head.data` without changing pointers         | O(1)            |
| `isEmpty()` | Check if `head === null`                             | O(1)            |

**Space Complexity:** O(N) — one node allocated per element

---

## Solution (JavaScript)

```javascript
class Node {
  constructor(data) {
    this.data = data;
    this.next = null;
  }
}

class Queue {
  constructor() {
    this.head = null;
    this.tail = null;
    this.size = 0;
  }

  push(x) {
    const node = new Node(x);
    if (this.tail === null) {
      this.head = node;
      this.tail = node;
    } else {
      this.tail.next = node;
      this.tail = node;
    }
    this.size++;
  }

  pop() {
    if (this.head === null) {
      console.log("Queue underflow: queue is empty");
      return undefined;
    }
    const val = this.head.data;
    this.head = this.head.next;
    if (this.head === null) {
      this.tail = null;
    }
    this.size--;
    return val;
  }

  peek() {
    if (this.head === null) {
      console.log("Queue is empty");
      return undefined;
    }
    return this.head.data;
  }

  isEmpty() {
    return this.size === 0;
  }
}
```

---

## Dry Run / Example

```javascript
const q = new Queue();

q.push(10);
// head → [10] → null ← tail   size=1

q.push(20);
// head → [10] → [20] → null ← tail   size=2

q.push(30);
// head → [10] → [20] → [30] → null ← tail   size=3

console.log(q.peek()); // Output: 10  (head unchanged, FIFO front)
console.log(q.isEmpty()); // Output: false

console.log(q.pop()); // Output: 10   head → [20] → [30] → null   size=2
console.log(q.pop()); // Output: 20   head → [30] → null           size=1
console.log(q.pop()); // Output: 30   head → null, tail → null     size=0
// tail is reset to null when queue empties (Bug fix 8)

console.log(q.isEmpty()); // Output: true

// Push after full drain — works correctly because tail was reset
q.push(40);
// head → [40] → null ← tail   size=1
console.log(q.pop()); // Output: 40

console.log(q.peek()); // Output: "Queue is empty"   undefined
console.log(q.pop()); // Output: "Queue underflow: queue is empty"   undefined
```

---

## The Stale Tail Bug (Bug #8) — Illustrated

Without resetting `tail` on full drain:

```
// After popping all elements:
head = null,  tail → [30]  ← stale pointer to freed node

// Now push(40):
// tail !== null → goes to else branch
tail.next = node(40)   // writes to the OLD disconnected node!
tail = node(40)
// head is still null — the new node is unreachable

console.log(q.pop());  // "Queue is empty" — data silently lost
```

With the fix (`if (this.head === null) this.tail = null`), after full drain both pointers are `null`, so the next push correctly enters the first-element branch and sets `head = tail = node`.

---

## Array vs Linked List Queue

|                       | Array Queue (Circular)    | Linked List Queue       |
| --------------------- | ------------------------- | ----------------------- |
| Size                  | Fixed (overflow possible) | Dynamic (no overflow)   |
| Memory                | Pre-allocated block       | Per-node allocation     |
| Cache performance     | Better (contiguous)       | Worse (scattered nodes) |
| Push/Pop              | O(1)                      | O(1)                    |
| Circular logic needed | Yes (modulo wrapping)     | No                      |

Prefer a linked list queue when the maximum size is unknown or highly variable.
