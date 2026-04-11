# Implement Queue Using Array (Circular Queue)

## Problem

Design a queue data structure using an array that supports the following operations:

- `push(x)` / `enqueue(x)` — Insert element `x` at the rear of the queue
- `pop()` / `dequeue()` — Remove and return the front element
- `peek()` — Return the front element without removing it
- `isEmpty()` — Check if the queue is empty

---

## Approach

A queue follows **FIFO (First In, First Out)** order.

A naive array queue wastes space — once the rear reaches the end, you can't push even if front slots were freed by pops. A **circular queue** solves this by wrapping `end` back to index `0` using modulo arithmetic.

We maintain:

- An array `arr` of fixed size
- `start` — index of the front element (initialized to `-1`)
- `end` — index of the rear element (initialized to `-1`)
- `t` — current count of elements (initialized to `0`)

### Operations:

| Operation   | Action                                            | Time Complexity |
| ----------- | ------------------------------------------------- | --------------- |
| `push(x)`   | Advance `end` circularly, store `x` at `arr[end]` | O(1)            |
| `pop()`     | Read `arr[start]`, advance `start` circularly     | O(1)            |
| `peek()`    | Return `arr[start]` without changing pointers     | O(1)            |
| `isEmpty()` | Check if `t == 0`                                 | O(1)            |

**Space Complexity:** O(N) where N is the fixed size of the array

---

## Solution (JavaScript)

```javascript
class Queue {
  constructor(size = 5) {
    this.arr = new Array(size);
    this.start = -1;
    this.end = -1;
    this.t = 0;
  }

  push(x) {
    if (this.t >= this.arr.length) {
      console.log("Queue overflow: queue is full");
      return;
    }
    if (this.start === -1 && this.end === -1) {
      // First element — initialize both pointers to 0
      this.start = 0;
      this.end = 0;
    } else {
      // Wrap end circularly
      this.end = (this.end + 1) % this.arr.length;
    }
    this.arr[this.end] = x;
    this.t++;
  }

  pop() {
    if (this.t <= 0) {
      console.log("Queue underflow: queue is already empty");
      return undefined;
    }
    const front = this.arr[this.start];
    this.start = (this.start + 1) % this.arr.length;
    this.t--;
    return front; // Fix: original code only logged, never returned the value
  }

  peek() {
    if (this.t <= 0) {
      console.log("Queue is empty");
      return undefined;
    }
    return this.arr[this.start];
  }

  isEmpty() {
    return this.t === 0;
  }
}
```

---

## Dry Run / Example

```javascript
const q = new Queue(5);

q.push(10); // arr: [10,  _,  _,  _,  _]  start=0  end=0  t=1
q.push(20); // arr: [10, 20,  _,  _,  _]  start=0  end=1  t=2
q.push(30); // arr: [10, 20, 30,  _,  _]  start=0  end=2  t=3
q.push(60); // arr: [10, 20, 30, 60,  _]  start=0  end=3  t=4
q.push(70); // arr: [10, 20, 30, 60, 70]  start=0  end=4  t=5

q.push(80); // Output: "Queue overflow: queue is full"

console.log(q.peek()); // Output: 10  (front element, pointers unchanged)

console.log(q.pop()); // Output: 10  start=1  t=4
console.log(q.pop()); // Output: 20  start=2  t=3
console.log(q.pop()); // Output: 30  start=3  t=2

// Circular wrap in action: push to slots freed by pops
q.push(80); // end = (4+1)%5 = 0  arr: [80, 20, 30, 60, 70]  t=3
q.push(90); // end = (0+1)%5 = 1  arr: [80, 90, 30, 60, 70]  t=4

console.log(q.pop()); // Output: 60  (continues from start=3)
console.log(q.pop()); // Output: 70
console.log(q.pop()); // Output: 80
console.log(q.pop()); // Output: 90

console.log(q.isEmpty()); // Output: true
console.log(q.pop()); // Output: "Queue underflow: queue is already empty"
//         undefined
```

---

## Why Circular Queue?

Without circular wrapping, after pushing 5 and popping 3, the array looks like:

```
[_, _, _, 60, 70]   start=3, end=4
```

Pushing again would try `end + 1 = 5` — out of bounds — even though indices 0, 1, 2 are free. The circular queue reuses those slots via modulo: `(4+1) % 5 = 0`.

---

## Limitations of Fixed-Size Array Queue

- Maximum capacity must be known in advance
- Overflow is possible unlike a dynamic (linked list) queue

For an unbounded queue, consider a linked list implementation.
