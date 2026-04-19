# Next Greater Element & Next Smaller Element

## Concept

For every element in an array, find:
- **Next Greater Element (NGE):** The first element to the **right** that is **greater** than the current element. If none exists, return `-1`.
- **Next Smaller Element (NSE):** The first element to the **right** that is **smaller** than the current element. If none exists, return `-1`.

Both problems are solved efficiently using a **Monotonic Stack** that stores **indices**.

---

## Approach

1. Initialize `result` array filled with `-1` (default: no answer found).
2. Traverse the array left to right.
3. Maintain a stack of indices where the corresponding values are in monotonic order.
4. For each element at index `i`:
   - **NGE:** While the element at the stack's top index is **less than** `arr[i]`, pop and record `arr[i]` as the answer.
   - **NSE:** While the element at the stack's top index is **greater than** `arr[i]`, pop and record `arr[i]` as the answer.
5. Push the current index onto the stack.
6. Indices remaining in the stack after the loop already have `-1` (no next greater/smaller exists).

---

## Complexity

| | |
|---|---|
| **Time Complexity** | O(n) — each index is pushed and popped at most once |
| **Space Complexity** | O(n) — stack and result array |

---

## Code

```javascript
// Bug fix: arr must be passed as a parameter, not used as a free variable

function nextGreater(arr) {
    let stack = [];
    let result = new Array(arr.length).fill(-1);

    for (let i = 0; i < arr.length; i++) {
        // arr[i] is the next greater element for all indices at top of stack
        while (stack.length > 0 && arr[stack[stack.length - 1]] < arr[i]) {
            result[stack.pop()] = arr[i];
        }
        stack.push(i);
    }
    // Remaining indices in stack have no next greater element → stay -1

    return result;
}

function nextSmaller(arr) {
    let stack = [];
    let result = new Array(arr.length).fill(-1);

    for (let i = 0; i < arr.length; i++) {
        // arr[i] is the next smaller element for all indices at top of stack
        while (stack.length > 0 && arr[stack[stack.length - 1]] > arr[i]) {
            result[stack.pop()] = arr[i];
        }
        stack.push(i);
    }
    // Remaining indices in stack have no next smaller element → stay -1

    return result;
}

const arr = [4, 5, 2, 10, 8];
console.log(nextGreater(arr)); // [5, 10, 10, -1, -1]
console.log(nextSmaller(arr)); // [2,  2, -1,  8, -1]
```

---

## Dry Run — `arr = [4, 5, 2, 10, 8]`

### Next Greater Element

Goal: for each index, find the first element to its right that is **greater**.

Expected: `[5, 10, 10, -1, -1]`

| Step | `i` | `arr[i]` | Stack (indices) | Values at stack top | Action | `result` |
|------|-----|----------|-----------------|---------------------|--------|----------|
| 1    | 0   | 4        | []              | —                   | Push 0 | [-1,-1,-1,-1,-1] |
| 2    | 1   | 5        | [0]             | arr[0]=4 < 5 → pop  | result[0]=5, push 1 | [5,-1,-1,-1,-1] |
| 3    | 2   | 2        | [1]             | arr[1]=5 > 2        | Push 2 | [5,-1,-1,-1,-1] |
| 4    | 3   | 10       | [1,2]           | arr[2]=2 < 10 → pop | result[2]=10 | [5,-1,10,-1,-1] |
|      |     |          | [1]             | arr[1]=5 < 10 → pop | result[1]=10, push 3 | [5,10,10,-1,-1] |
| 5    | 4   | 8        | [3]             | arr[3]=10 > 8       | Push 4 | [5,10,10,-1,-1] |
| End  | —   | —        | [3,4]           | —                   | No pop, remain -1 | [5,10,10,-1,-1] |

**Output:** `[5, 10, 10, -1, -1]` ✅

---

### Next Smaller Element

Goal: for each index, find the first element to its right that is **smaller**.

Expected: `[2, 2, -1, 8, -1]`

| Step | `i` | `arr[i]` | Stack (indices) | Values at stack top | Action | `result` |
|------|-----|----------|-----------------|---------------------|--------|----------|
| 1    | 0   | 4        | []              | —                   | Push 0 | [-1,-1,-1,-1,-1] |
| 2    | 1   | 5        | [0]             | arr[0]=4 < 5        | Push 1 | [-1,-1,-1,-1,-1] |
| 3    | 2   | 2        | [0,1]           | arr[1]=5 > 2 → pop  | result[1]=2 | [-1,2,-1,-1,-1] |
|      |     |          | [0]             | arr[0]=4 > 2 → pop  | result[0]=2, push 2 | [2,2,-1,-1,-1] |
| 4    | 3   | 10       | [2]             | arr[2]=2 < 10       | Push 3 | [2,2,-1,-1,-1] |
| 5    | 4   | 8        | [2,3]           | arr[3]=10 > 8 → pop | result[3]=8, push 4 | [2,2,-1,8,-1] |
| End  | —   | —        | [2,4]           | —                   | No pop, remain -1 | [2,2,-1,8,-1] |

**Output:** `[2, 2, -1, 8, -1]` ✅

---

## Quick Reference

| Element | Index | NGE | NSE |
|---------|-------|-----|-----|
| 4       | 0     | 5   | 2   |
| 5       | 1     | 10  | 2   |
| 2       | 2     | 10  | -1  |
| 10      | 3     | -1  | 8   |
| 8       | 4     | -1  | -1  |
