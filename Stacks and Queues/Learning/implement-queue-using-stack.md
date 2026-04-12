# Implement Queue Using Two Stacks

## Problem

Design a queue (FIFO) using only two stacks. Support the following operations:

- `push(x)` — Insert element `x` at the rear of the queue
- `pop()` — Remove and return the front element
- `peek()` — Return the front element without removing it
- `size()` — Return the number of elements in the queue
- `isEmpty()` — Check if the queue is empty

---

## Approach 1 — Costly Push (implemented below)

Maintain two stacks `s1` and `s2`. Keep all elements in `s1` such that the **oldest element is always on top** (ready for O(1) pop/peek).

On every `push`:

1. Move all elements from `s1` → `s2`
2. Push the new element onto `s1` (now it's at the bottom)
3. Move everything back from `s2` → `s1`

```
push(10): s1=[10]
push(20): move 10 to s2 → push 20 to s1 → move back → s1=[10,20]  top=10 (front)
push(30): move 10,20 to s2 → push 30 → move back → s1=[10,20,30]  top=10 (front)
pop()   : s1.pop() → 10  ✓ FIFO
```

### Time Complexity:

| Operation | Time                                |
| --------- | ----------------------------------- |
| `push(x)` | O(N) — transfers all elements twice |
| `pop()`   | O(1)                                |
| `peek()`  | O(1)                                |
| `size()`  | O(1)                                |

**Space Complexity:** O(N)

---

## Solution (JavaScript)

```javascript
class Queue {
  constructor() {
    this.s1 = [];
    this.s2 = [];
  }

  push(val) {
    while (this.s1.length > 0) {
      this.s2.push(this.s1.pop());
    }
    this.s1.push(val);
    while (this.s2.length > 0) {
      this.s1.push(this.s2.pop());
    }
  }

  pop() {
    if (this.s1.length <= 0) {
      console.log("Queue underflow: queue is empty");
      return undefined;
    }
    return this.s1.pop();
  }

  peek() {
    if (this.s1.length <= 0) {
      console.log("Queue is empty");
      return undefined;
    }
    return this.s1[this.s1.length - 1];
  }

  size() {
    return this.s1.length;
  }

  isEmpty() {
    return this.s1.length === 0;
  }
}
```

---

## Dry Run / Example

```javascript
const q = new Queue();

q.push(10); // s1=[10]              top of s1=10 (front of queue)
q.push(20); // s1=[10,20]           top of s1=10
q.push(30); // s1=[10,20,30]        top of s1=10

console.log(q.peek()); // Output: 10  (FIFO front)
console.log(q.size()); // Output: 3
console.log(q.isEmpty()); // Output: false

console.log(q.pop()); // Output: 10   s1=[20,30]
console.log(q.pop()); // Output: 20   s1=[30]
console.log(q.pop()); // Output: 30   s1=[]

console.log(q.isEmpty()); // Output: true
console.log(q.pop()); // Output: "Queue underflow: queue is empty"   undefined
```

---

## Approach 2 — Lazy Transfer (more efficient, amortized O(1))

Instead of rearranging on every push, only transfer from `s1` to `s2` when `s2` is empty and a pop/peek is needed.

```javascript
class Queue {
  constructor() {
    this.s1 = []; // push stack (rear)
    this.s2 = []; // pop stack  (front)
  }

  push(val) {
    this.s1.push(val); // always O(1)
  }

  pop() {
    if (this.s2.length === 0) {
      // Transfer only when s2 is empty
      while (this.s1.length > 0) {
        this.s2.push(this.s1.pop());
      }
    }
    if (this.s2.length === 0) {
      console.log("Queue underflow: queue is empty");
      return undefined;
    }
    return this.s2.pop();
  }

  peek() {
    if (this.s2.length === 0) {
      while (this.s1.length > 0) {
        this.s2.push(this.s1.pop());
      }
    }
    if (this.s2.length === 0) {
      console.log("Queue is empty");
      return undefined;
    }
    return this.s2[this.s2.length - 1];
  }

  size() {
    return this.s1.length + this.s2.length;
  }

  isEmpty() {
    return this.s1.length === 0 && this.s2.length === 0;
  }
}
```

### Time Complexity (Approach 2):

| Operation | Time                                |
| --------- | ----------------------------------- |
| `push(x)` | O(1)                                |
| `pop()`   | O(N) worst case, **O(1) amortized** |
| `peek()`  | O(N) worst case, **O(1) amortized** |
| `size()`  | O(1)                                |

Each element is moved from `s1` to `s2` **at most once**, so across N operations the total work is O(N) → amortized O(1) per operation.

---

## Approach Comparison

|          | Approach 1 (Costly Push) | Approach 2 (Lazy Transfer) |
| -------- | ------------------------ | -------------------------- |
| `push`   | O(N)                     | O(1)                       |
| `pop`    | O(1)                     | O(1) amortized             |
| `peek`   | O(1)                     | O(1) amortized             |
| Best for | Pop-heavy workloads      | General use (preferred)    |
