# Implement Stack Using Linked List

## Problem

Design a stack data structure using a linked list that supports the following operations:

- `push(x)` — Insert element `x` onto the top of the stack
- `pop()` — Remove and return the top element
- `peek()` — Return the top element without removing it
- `isEmpty()` — Check if the stack is empty

---

## Approach

A stack follows **LIFO (Last In, First Out)** order.

Using a linked list instead of an array removes the fixed-size limitation — the stack grows and shrinks dynamically with each push/pop.

We maintain a `top` pointer that always points to the most recently pushed node. Each node holds a `data` value and a `next` pointer to the node below it.

```
push(10) → push(20) → push(30)

top
 ↓
[30] → [20] → [10] → null
```

### Operations:

| Operation   | Action                                           | Time Complexity |
| ----------- | ------------------------------------------------ | --------------- |
| `push(x)`   | Create new node, point it to `top`, update `top` | O(1)            |
| `pop()`     | Save `top.data`, advance `top` to `top.next`     | O(1)            |
| `peek()`    | Return `top.data` without changing `top`         | O(1)            |
| `isEmpty()` | Check if `top === null`                          | O(1)            |

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

class Stack {
  constructor() {
    this.top = null;
    this.size = 0;
  }

  push(x) {
    const node = new Node(x);
    node.next = this.top;
    this.top = node;
    this.size++;
  }

  pop() {
    if (this.top === null) {
      console.log("Stack underflow: stack is empty");
      return undefined;
    }
    const val = this.top.data;
    this.top = this.top.next;
    this.size--;
    return val;
  }

  peek() {
    if (this.top === null) {
      console.log("Stack is empty");
      return undefined;
    }
    return this.top.data;
  }

  isEmpty() {
    return this.size === 0;
  }
}
```

---

## Dry Run / Example

```javascript
const st = new Stack();

st.push(10);
// top → [10] → null   size=1

st.push(20);
// top → [20] → [10] → null   size=2

st.push(30);
// top → [30] → [20] → [10] → null   size=3

console.log(st.peek()); // Output: 30  (top unchanged)
console.log(st.isEmpty()); // Output: false

console.log(st.pop()); // Output: 30  top → [20] → [10] → null   size=2
console.log(st.pop()); // Output: 20  top → [10] → null           size=1
console.log(st.pop()); // Output: 10  top → null                  size=0

console.log(st.isEmpty()); // Output: true
console.log(st.peek()); // Output: "Stack is empty"   undefined
console.log(st.pop()); // Output: "Stack underflow: stack is empty"   undefined
```

---

## Array vs Linked List Stack

|                   | Array Stack               | Linked List Stack       |
| ----------------- | ------------------------- | ----------------------- |
| Size              | Fixed (overflow possible) | Dynamic (no overflow)   |
| Memory            | Pre-allocated block       | Per-node allocation     |
| Cache performance | Better (contiguous)       | Worse (scattered nodes) |
| Push/Pop          | O(1)                      | O(1)                    |

Prefer a linked list stack when the maximum size is unknown or highly variable.
