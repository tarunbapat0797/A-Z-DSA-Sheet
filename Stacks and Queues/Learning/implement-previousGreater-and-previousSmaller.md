# Previous Greater Element & Previous Smaller Element

## Concept

For every element in an array, find:
- **Previous Greater Element (PGE):** The first element to the **left** that is **greater** than the current element. If none exists, return `-1`.
- **Previous Smaller Element (PSE):** The first element to the **left** that is **smaller** than the current element. If none exists, return `-1`.

Both are solved using a **Monotonic Stack** traversed **right to left**, storing **indices**.

---

## Why Right to Left?

Traversing right to left means we encounter elements **closer to the left of `j` last** (i.e., at the smallest `i < j`).

When we pop index `j` at step `i`, it means:
1. `arr[i]` satisfies the condition relative to `arr[j]`.
2. All elements between `i+1` and `j-1` were processed before `i` (right to left) and **did not pop `j`** — so none of them satisfied the condition.
3. Therefore `arr[i]` is the **closest** element to the left of `j` that satisfies the condition. ✅

---

## Approach

1. Initialize `result` array filled with `-1` (default: no answer found).
2. Traverse the array **right to left**.
3. Maintain a stack of indices in monotonic order.
4. For each element at index `i`:
   - **PGE:** While `arr[stack top] < arr[i]`, pop and record `arr[i]` as the previous greater for the popped index.
   - **PSE:** While `arr[stack top] > arr[i]`, pop and record `arr[i]` as the previous smaller for the popped index.
5. Push the current index onto the stack.
6. Indices remaining in the stack have no previous greater/smaller → already `-1`.

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

function prevGreater(arr) {
    let stack = [];
    let result = new Array(arr.length).fill(-1);

    for (let i = arr.length - 1; i >= 0; i--) {
        // arr[i] is the previous greater element for all smaller indices at stack top
        while (stack.length > 0 && arr[stack[stack.length - 1]] < arr[i]) {
            result[stack.pop()] = arr[i];
        }
        stack.push(i);
    }
    // Remaining indices in stack have no previous greater element → stay -1

    return result;
}

function prevSmaller(arr) {
    let stack = [];
    let result = new Array(arr.length).fill(-1);

    for (let i = arr.length - 1; i >= 0; i--) {
        // arr[i] is the previous smaller element for all larger indices at stack top
        while (stack.length > 0 && arr[stack[stack.length - 1]] > arr[i]) {
            result[stack.pop()] = arr[i];
        }
        stack.push(i);
    }
    // Remaining indices in stack have no previous smaller element → stay -1

    return result;
}

const arr = [4, 5, 2, 10, 8];
console.log(prevGreater(arr)); // [-1, -1,  5, -1, 10]
console.log(prevSmaller(arr)); // [-1,  4, -1,  2,  2]
```

---

## Dry Run — `arr = [4, 5, 2, 10, 8]`

### Previous Greater Element

Goal: for each index, find the first element to its **left** that is **greater**.

Expected: `[-1, -1, 5, -1, 10]`

| Step | `i` | `arr[i]` | Stack (indices) | Values at stack top | Action | `result` |
|------|-----|----------|-----------------|---------------------|--------|----------|
| 1    | 4   | 8        | []              | —                   | Push 4 | [-1,-1,-1,-1,-1] |
| 2    | 3   | 10       | [4]             | arr[4]=8 < 10 → pop | result[4]=10, push 3 | [-1,-1,-1,-1,10] |
| 3    | 2   | 2        | [3]             | arr[3]=10 > 2 → stop | Push 2 | [-1,-1,-1,-1,10] |
| 4    | 1   | 5        | [3,2]           | arr[2]=2 < 5 → pop  | result[2]=5 | [-1,-1,5,-1,10] |
|      |     |          | [3]             | arr[3]=10 > 5 → stop | Push 1 | [-1,-1,5,-1,10] |
| 5    | 0   | 4        | [3,1]           | arr[1]=5 > 4 → stop | Push 0 | [-1,-1,5,-1,10] |
| End  | —   | —        | [3,1,0]         | —                   | No pop, remain -1 | [-1,-1,5,-1,10] |

**Output:** `[-1, -1, 5, -1, 10]` ✅

---

### Previous Smaller Element

Goal: for each index, find the first element to its **left** that is **smaller**.

Expected: `[-1, 4, -1, 2, 2]`

| Step | `i` | `arr[i]` | Stack (indices) | Values at stack top | Action | `result` |
|------|-----|----------|-----------------|---------------------|--------|----------|
| 1    | 4   | 8        | []              | —                   | Push 4 | [-1,-1,-1,-1,-1] |
| 2    | 3   | 10       | [4]             | arr[4]=8 > 10? No → stop | Push 3 | [-1,-1,-1,-1,-1] |
| 3    | 2   | 2        | [4,3]           | arr[3]=10 > 2 → pop | result[3]=2 | [-1,-1,-1,2,-1] |
|      |     |          | [4]             | arr[4]=8 > 2 → pop  | result[4]=2, push 2 | [-1,-1,-1,2,2] |
| 4    | 1   | 5        | [2]             | arr[2]=2 > 5? No → stop | Push 1 | [-1,-1,-1,2,2] |
| 5    | 0   | 4        | [2,1]           | arr[1]=5 > 4 → pop  | result[1]=4 | [-1,4,-1,2,2] |
|      |     |          | [2]             | arr[2]=2 > 4? No → stop | Push 0 | [-1,4,-1,2,2] |
| End  | —   | —        | [2,0]           | —                   | No pop, remain -1 | [-1,4,-1,2,2] |

**Output:** `[-1, 4, -1, 2, 2]` ✅

---

## Quick Reference

| Element | Index | PGE | PSE |
|---------|-------|-----|-----|
| 4       | 0     | -1  | -1  |
| 5       | 1     | -1  | 4   |
| 2       | 2     | 5   | -1  |
| 10      | 3     | -1  | 2   |
| 8       | 4     | 10  | 2   |

---

## Next vs Previous — Comparison

| Problem | Traversal Direction | Stack Condition (pop when) |
|---------|---------------------|----------------------------|
| Next Greater   | Left → Right | `arr[top] < arr[i]` |
| Next Smaller   | Left → Right | `arr[top] > arr[i]` |
| Previous Greater | **Right → Left** | `arr[top] < arr[i]` |
| Previous Smaller | **Right → Left** | `arr[top] > arr[i]` |

The only difference between Next and Previous variants is the **direction of traversal**.
