# Min Stack

## Problem

Design a stack that supports `push`, `pop`, `top`, and `getMin` operations — where `getMin` retrieves the **minimum element in constant time**.

Implement the `MinStack` class:

- `push(val)` — Push element `val` onto the stack
- `pop()` — Remove the top element
- `top()` — Get the top element
- `getMin()` — Retrieve the minimum element in the stack

> LeetCode 155 — [Min Stack](https://leetcode.com/problems/min-stack/)

---

## Intuition

The naive approach to `getMin` is to scan the entire stack — O(N). To achieve O(1), we maintain a **parallel `minStack`** that always has the current minimum at its top.

### Key Insight

- On `push(val)`: only push `val` onto `minStack` if it is **≤ the current minimum** (i.e., ≤ `minStack.top`). This ensures `minStack.top` always reflects the minimum of the main stack.
- On `pop()`: if the popped value **equals** `minStack.top`, also pop from `minStack` — the minimum has been removed.
- On `getMin()`: simply return `minStack.top`.

---

## Approach

| Operation  | Action                                                             |
| ---------- | ------------------------------------------------------------------ |
| `push(val)` | Push to `stack`. If `minStack` is empty or `val ≤ minStack.top`, also push to `minStack`. |
| `pop()`    | Pop from `stack`. If popped value equals `minStack.top`, also pop from `minStack`. |
| `top()`    | Return `stack.top`.                                                |
| `getMin()` | Return `minStack.top`.                                             |

---

## Complexity

| | Time | Space |
|---|---|---|
| `push` | O(1) | — |
| `pop` | O(1) | — |
| `top` | O(1) | — |
| `getMin` | O(1) | — |
| **Overall** | **O(1) per operation** | **O(N)** — worst case both stacks hold N elements |

---

## Solution (JavaScript)

```javascript
var MinStack = function () {
  this.stack = [];
  this.minStack = [];
};

MinStack.prototype.push = function (val) {
  if (this.stack.length === 0 || val <= this.minStack[this.minStack.length - 1]) {
    this.minStack.push(val);
  }
  this.stack.push(val);
};

MinStack.prototype.pop = function () {
  if (this.stack.length === 0) return;
  const removed = this.stack.pop();
  if (removed === this.minStack[this.minStack.length - 1]) {
    this.minStack.pop();
  }
  return removed;
};

MinStack.prototype.top = function () {
  if (this.stack.length === 0) return;
  return this.stack[this.stack.length - 1];
};

MinStack.prototype.getMin = function () {
  return this.minStack[this.minStack.length - 1];
};
```

---

## Dry Run

```
Operations:  push(-2) → push(0) → push(-3) → getMin() → pop() → top() → getMin()

Step 1: push(-2)
  stack    = [-2]
  minStack = [-2]   ← -2 ≤ nothing (empty), so pushed

Step 2: push(0)
  stack    = [-2, 0]
  minStack = [-2]   ← 0 > -2, so NOT pushed to minStack

Step 3: push(-3)
  stack    = [-2, 0, -3]
  minStack = [-2, -3]   ← -3 ≤ -2, so pushed

Step 4: getMin()
  → minStack.top = -3  ✓

Step 5: pop()
  removed = -3
  -3 === minStack.top (-3), so also pop minStack
  stack    = [-2, 0]
  minStack = [-2]

Step 6: top()
  → stack.top = 0  ✓

Step 7: getMin()
  → minStack.top = -2  ✓
```

---

## Test Cases

```javascript
const ms = new MinStack();

// Basic min tracking
ms.push(-2); ms.push(0); ms.push(-3);
console.log(ms.getMin()); // -3
ms.pop();
console.log(ms.top());    // 0
console.log(ms.getMin()); // -2

// Duplicate minimums — min should survive one pop
const ms2 = new MinStack();
ms2.push(1); ms2.push(1);
console.log(ms2.getMin()); // 1
ms2.pop();
console.log(ms2.getMin()); // 1  ← still correct

// Single element
const ms3 = new MinStack();
ms3.push(42);
console.log(ms3.getMin()); // 42
console.log(ms3.top());    // 42
ms3.pop();
console.log(ms3.top());    // undefined (empty)

// Negative numbers
const ms4 = new MinStack();
ms4.push(5); ms4.push(3); ms4.push(7); ms4.push(2);
console.log(ms4.getMin()); // 2
ms4.pop();
console.log(ms4.getMin()); // 3
```

---

## Edge Cases

- **Duplicate minimums**: pushing the same minimum value twice — both enter `minStack` (via `≤`), so popping one doesn't lose the minimum. This is why the condition uses `≤` instead of `<`.
- **Empty stack**: `top()` and `pop()` guard with a length check and return `undefined`.
- **All same values**: `minStack` grows in sync with `stack`; still O(1) per operation.

---

## Why `≤` and not `<` in push?

If we used strict `<`, duplicate minimums would only be pushed once. Then popping the first occurrence of the minimum would incorrectly clear it from `minStack`, even though a second copy still exists in `stack`. Using `≤` guarantees `minStack` always has a valid minimum after any pop.
