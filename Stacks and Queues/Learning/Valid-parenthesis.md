# Valid Parentheses

## Problem

Given a string `s` containing only the characters `(`, `)`, `{`, `}`, `[`, and `]`, determine if the input string is **valid**.

A string is valid if:
1. Every open bracket is closed by the **same type** of bracket
2. Open brackets are closed in the **correct order**
3. Every close bracket has a **corresponding open bracket**

> LeetCode 20 — [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)

---

## Intuition

Brackets must be closed in **LIFO order** — the most recently opened bracket must be closed first. That's exactly what a **stack** models.

- When we see an **opening bracket**, push it onto the stack — we're waiting for its match.
- When we see a **closing bracket**, check if the top of the stack is its matching opener. If yes, pop (matched pair found). If no, the string is invalid.
- At the end, the stack must be **empty** — every opener was matched.

A **lookup map** (`close → expected open`) makes the matching check a single O(1) lookup instead of multiple `if/else` comparisons.

---

## Approach

1. Initialise an empty `stack` and a map `{ ')':'(', '}':'{', ']':'[' }`.
2. Iterate through each character in `s`:
   - **Opening bracket** → push onto stack.
   - **Closing bracket** → if `stack.top === map[char]`, pop. Otherwise return `false`.
3. Return `stack.length === 0`.

---

## Complexity

| | Complexity |
|---|---|
| **Time** | O(N) — single pass through the string |
| **Space** | O(N) — stack holds at most N/2 openers (e.g. `"((((("`) |

---

## Solution (JavaScript)

```javascript
var isValid = function (s) {
  const stack = [];
  const map = {
    ')': '(',
    '}': '{',
    ']': '[',
  };

  for (let i = 0; i < s.length; i++) {
    if (s[i] === '(' || s[i] === '[' || s[i] === '{') {
      stack.push(s[i]);
    } else {
      if (stack[stack.length - 1] === map[s[i]]) {
        stack.pop();
      } else {
        return false;
      }
    }
  }

  return stack.length === 0;
};
```

---

## Dry Run

```
Input: "({[]})"

i=0  char='('  → opening → stack: ['(']
i=1  char='{'  → opening → stack: ['(', '{']
i=2  char='['  → opening → stack: ['(', '{', '[']
i=3  char=']'  → closing → map[']'] = '[', stack.top = '[' ✓ → pop → stack: ['(', '{']
i=4  char='}'  → closing → map['}'] = '{', stack.top = '{' ✓ → pop → stack: ['(']
i=5  char=')'  → closing → map[')'] = '(', stack.top = '(' ✓ → pop → stack: []

stack is empty → return true ✓
```

```
Input: "([)]"

i=0  char='('  → opening → stack: ['(']
i=1  char='['  → opening → stack: ['(', '[']
i=2  char=')'  → closing → map[')'] = '(', stack.top = '[' ✗ → return false ✓
```

---

## Test Cases

```javascript
console.log(isValid("()"));        // true  — simple pair
console.log(isValid("()[]{}"));    // true  — multiple valid pairs
console.log(isValid("({[]})"));    // true  — nested brackets
console.log(isValid("(]"));        // false — wrong closing type
console.log(isValid("([)]"));      // false — interleaved brackets
console.log(isValid("{[]"));       // false — unclosed opener
console.log(isValid("]"));         // false — closer with empty stack
console.log(isValid(""));          // true  — empty string is valid
```

---

## Edge Cases

| Input | Output | Reason |
|---|---|---|
| `"]"` | `false` | Closing bracket with empty stack — `stack.top` is `undefined`, which never equals `map[']']` |
| `"((("` | `false` | Stack is non-empty at end — openers were never matched |
| `""` | `true` | Empty string: loop doesn't run, `stack.length === 0` |
| `"()"` × 10000 | `true` | Large input handled in O(N) |

---

## Why Return `stack.length === 0` and Not Just `true`?

Returning `true` at the end without checking the stack would incorrectly accept strings like `"((("` — all openers, no closers, loop completes without ever returning `false`. The empty stack check catches **unmatched openers**.
