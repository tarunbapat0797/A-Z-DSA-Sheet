# Implement Stack Using Array

## Problem

Design a stack data structure using an array that supports the following operations:

- `push(x)` — Insert element `x` onto the top of the stack
- `pop()` — Remove and return the top element
- `peek()` — Return the top element without removing it
- `isEmpty()` — Check if the stack is empty

---

## Approach

A stack follows **LIFO (Last In, First Out)** order. We maintain:

- An array `arr` of fixed size to store elements
- A pointer `t` (top) initialized to `-1` to track the top of the stack

### Operations:

| Operation   | Action                               | Time Complexity |
| ----------- | ------------------------------------ | --------------- |
| `push(x)`   | Increment `t`, store `x` at `arr[t]` | O(1)            |
| `pop()`     | Return `arr[t]`, decrement `t`       | O(1)            |
| `peek()`    | Return `arr[t]` without changing `t` | O(1)            |
| `isEmpty()` | Check if `t == -1`                   | O(1)            |

**Space Complexity:** O(N) where N is the fixed size of the array

---

## Solution (JavaScript)

```javascript
class Stack {
  constructor(size = 5) {
    this.t = -1;
    this.arr = new Array(size);
  }

  push(x) {
    // Bug fix 1: condition was `>= this.arr.length` (off-by-one)
    // Bug fix 2: missing `return` — execution continued even when full
    if (this.t >= this.arr.length - 1) {
      console.log("Stack overflow: stack is full");
      return;
    }
    this.t += 1;
    this.arr[this.t] = x;
  }

  pop() {
    // Bug fix: missing `return` — t was decremented below -1 on empty stack
    if (this.t === -1) {
      console.log("Stack underflow: stack is already empty");
      return undefined;
    }
    const top = this.arr[this.t];
    this.t -= 1;
    return top;
  }

  peek() {
    if (this.t === -1) {
      console.log("Stack is empty");
      return undefined;
    }
    return this.arr[this.t];
  }

  isEmpty() {
    return this.t === -1;
  }
}
```

---

## Dry Run / Example

```javascript
const st = new Stack(5);

st.push(10); // arr: [10, _, _, _, _]   t = 0
st.push(20); // arr: [10, 20, _, _, _]  t = 1
st.push(30); // arr: [10, 20, 30, _, _] t = 2

console.log(st.peek()); // Output: 30  (top element, t unchanged)

console.log(st.pop()); // Output: 30  arr: [10, 20, _, _, _] t = 1
console.log(st.pop()); // Output: 20  arr: [10, _, _, _, _]  t = 0

console.log(st.isEmpty()); // Output: false

console.log(st.pop()); // Output: 10  arr: [_, _, _, _, _]   t = -1

console.log(st.isEmpty()); // Output: true
console.log(st.pop()); // Output: "Stack underflow: stack is already empty"
//         undefined
```

---

## Limitations of Fixed-Size Array Stack

- Size must be known in advance
- Wastes memory if stack never fills up
- Overflow is possible unlike a dynamic (linked list) stack

For a resizable stack, consider using JavaScript's built-in array (`push`/`pop`) or a linked list implementation.
