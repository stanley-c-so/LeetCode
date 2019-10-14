# LeetCode: Rotate Function

- Number: 396
- URL: https://leetcode.com/problems/rotate-function/
- Difficulty: Medium

## The problem

Given:
- an array of integers, `A`,
- its length, `n`,
- `B(k)` defined as the array resulting from rotating the array `A` by `k` positions clockwise, and
- a "rotation function" `F` defined as `F(k) = (0 * B(k)[0]) + (1 * B(k)[1]) + ... + ((n-1) * B(k)[n-1])`,

your task is to calculate the maximum value of `F(0), F(1), ..., F(n-1)`.

Note: `n` is guaranteed to be less than 105.

Example:

```
A = [4, 3, 2, 6]

F(0) = (0 * 4) + (1 * 3) + (2 * 2) + (3 * 6) = 0 + 3 + 4 + 18 = 25
F(1) = (0 * 6) + (1 * 4) + (2 * 3) + (3 * 2) = 0 + 4 + 6 + 6 = 16
F(2) = (0 * 2) + (1 * 6) + (2 * 4) + (3 * 3) = 0 + 6 + 8 + 9 = 23
F(3) = (0 * 3) + (1 * 2) + (2 * 6) + (3 * 4) = 0 + 2 + 12 + 12 = 26
```

So the maximum value of `F(0), F(1), F(2), F(3)` is `F(3) = 26`.

## Naive Solution

A straightforward, brute force method might iterate through the array, `A`, with a `for` loop, and at each position `i`, do the following steps:

1. find `B(i)` (the result from rotating `A` by `i` positions),
2. calculate `F` by multiplying each element of `B(i)` by `i`.

Step (1) could be achieved by actually rotating `A` using successive `.pop()` and `.unshift()` operations. Even better, `B(i)` can be composed with `[...A.slice(i), ...A.slice(0, i)]` (ES6 spread operator) or `A.slice(i).concat(A.slice(0, i))`.

Step (2) could be achieved with another `for` loop, or with a `.reduce()` operation.

The resulting values of `F(i)` might be saved inside another array, for example, until all values of `F` are found. `Math.max()` could then be used to find the greatest value of `F(0), F(1), ..., F(n-1)` as required by the problem. However, since the problem only requires the greatest value of `F`, a more space-efficient method would be to have a single variable that keeps track of the greatest value of `F` so far as the array is traversed.

### Example Code

```js
var maxRotateFunction = function(A) {
  if (!A.length) return 0;    // edge case: empty array input
  const F = [];
  for (let i = 0; i < A.length; i++) {
    const B = [...A.slice(i), ...A.slice(0, i)];
    F[i] = B.reduce((sum, num, idx) => sum + num * idx, 0);   // initialValue of 0 is required!
  }
  return Math.max(...F);
}
```


### Time Complexity

Steps (1) and (2) both involve making a pass through the array (with length `n`). Since both of these steps are themselves inside a `for` loop (that makes `n` iterations), the time complexity is `O(n^2)`.

### Space Complexity

As the rotated arrays `B(i)` are constructed one by one, additional space proportional to the original input (size `n`) is created. Additionally, if an array is created to retain each value of `F`, then additional space is required. Either way, `O(n)` additional space is used by this method. Note that even though `n` rotations of the array are created, at no point in time do all `n` rotations need to coexist. Because of block scoping, the same space will be reused to hold each rotation. (It is even possible to achieve `O(1)` additional space if the original input array is allowed to be modified.)

## Linear Time Solution

asdf