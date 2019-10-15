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

## Approach 1: Naive, Brute Force Solution

A straightforward, brute force method might iterate through the array, `A`, with a `for` loop, and at each position `i`, do the following steps:

1. find `B(i)` (the result from rotating `A` by `i` positions), and
2. calculate `F(i)` by multiplying each element of `B(i)` by `i`.

Step (1) could be achieved by actually rotating `A` using successive `.pop()` and `.unshift()` operations. Even better, `B(i)` can be composed with `[...A.slice(i), ...A.slice(0, i)]` (ES6 spread operator) or `A.slice(i).concat(A.slice(0, i))`.

Step (2) could be achieved with another `for` loop, or with a `.reduce()` operation.

The resulting values of `F(i)` might be saved inside another array, for example, until all values of `F` are found. `Math.max()` could then be used to find the greatest value of `F(0), F(1), ..., F(n-1)` as required by the problem. However, since the problem only requires the greatest value of `F`, a more space-efficient method would be to have a single variable that keeps track of the greatest value of `F` so far as the array is traversed.

### Example Code

```js
var maxRotateFunction = function(A) {

  // edge case: empty array input
  if (!A.length) return 0;

  // iterate through array, determining B(i) and F(i)
  const F = [];
  for (let i = 0; i < A.length; i++) {
    const B = [...A.slice(i), ...A.slice(0, i)];
    F[i] = B.reduce((sum, num, idx) => sum + num * idx, 0);     // initialValue of 0 is required!
  }

  // return highest value of F(i)
  return Math.max(...F);
}
```

### Time Complexity

Steps (1) and (2) both involve making a pass through the array (with length `n`). Since both of these steps are themselves inside a `for` loop (that makes `n` iterations), the time complexity is `O(n^2)`.

### Space Complexity

As the rotated arrays `B(i)` are constructed one by one, additional space proportional to the original input (size `n`) is created. Additionally, if an array is created to retain each value of `F`, then additional space is required. Either way, `O(n)` additional space is used by this method. Note that even though `n` rotations of the array are created, at no point in time do all `n` rotations need to coexist. Because of block scoping, the same space will be reused to hold each rotation. (It is even possible to achieve `O(1)` additional space if the original input array is allowed to be modified.)

## Approach 2: Space-Optimized Brute Force Method Using Pointers

The above brute force method can be implemented without actually rotating the arrays. By using `for` loops, we can calculate each value of `F` and save the highest value found after each iteration.

### Example Code

```js
var maxRotateFunction = function(A) {

  // edge case: empty array input
  if (!A.length) return 0;

  // iterate through 'offset' values (simulating rotating the array)
  let maxSoFar = -Infinity;
  for (let offset = 0; offset < A.length; offset++) {
    let F = 0;
    for (let i = 0; i < A.length; i++) {
      F += i * A[(offset + i) % A.length];    // modulo helps to 'wrap around' the end of the array
    }
    maxSoFar = Math.max(maxSoFar, F);
  }
  return maxSoFar;
}
```

### Time Complexity

Time complexity is `O(n^2)` because of the nested `for` loops. This makes sense: every rotation has to be considered, and for each rotation, every element needs to be analyzed.

### Space Complexity

Thanks to the use of pointers, no extra space dependent on `n` needs to be used. Thus the space complexity is `O(1)` extra space.

## Approach 3: Linear Time Solution

Let's explore the example given in the problem, where `A = [4, 3, 2, 6]`. Suppose we calculate `F(0) = (0 * 4) + (1 * 3) + (2 * 2) + (3 * 6) = 25`. Is there a way we can calculate `F(1), ..., F(n-1)` without actually reconstructing the rotated arrays or making any more passes through the array for each rotation?

Consider the change that occurs when transitioning from `F(i)` to `F(i + 1)`. For example, if transitioning from `F(0)` to `F(1)`, note that `F(1) = (0 * 6) + (1 * 4) + (2 * 3) + (3 * 2) = 16`. Compared to `F(0) = (0 * 4) + (1 * 3) + (2 * 2) + (3 * 6) = 25`, `F(1)` has an extra `4`, an extra `3`, and an extra `2`, but there are three fewer `6`s. In other words, `F(1) = F(0) + 4 + 3 + 2 - (3 * 6) = 16`. Similarly, `F(2) = F(1) + 6 + 4 + 3 - (3 * 2) = 23`, etc.

Taking another look at our new definition of `F(1)`, we can rewrite this as `F(1) = F(0) + (6 + 4 + 3 + 2) - (4 * 6)` or simply `F(0) + S - (4 * 6)` where `S` is the sum of all numbers in the array - this will be useful because `S` is constant for every possible rotation of the array.

As to the term being subtracted (e.g. `(4 * 6)`), the multiplier (`4`) is the length of the array, and the element being multiplied (`6`) is the number that would have been the final element of the array just before the `i`th rotation. When `i = 1`, the index value of the element being subtracted is `A.length - 1`. When `i = 2`, the index value of the element being subtracted is `A.length - 2`. The general case is `A.length - i` for `i > 0`. (We do not have to worry about going out of bounds, as we do not need to consider `i = 0` if we already know `F(0)`.)

Thus, we can now solve this problem with the following three steps:

1. determine `S`,
2. determine `F(0)`, and
3. iterate through the array starting at `i = 1`, calculating `F(i) = F(i - 1) + S - (A.length * A[A.length - i])`.

How can we optimize for time? Step (3) cannot be done until `F(0)` and `S` are found - each of which individually require one pass through the array. In fact, we can accomplish steps (1) and (2) simultaneously while making the first pass through the array. Thereafter, we can then tackle step (3) on a second pass.

### Example Code

```js
var maxRotateFunction = function(A) {
  const L = A.length;   // using L to be less verbose

  // iterate through array, determining F(0) and sum (sum of all numbers in A)
  let sum = 0;
  let F0 = 0;
  for (let i = 0; i < L; i++) {
    sum += A[i];
    F0 += i * A[i];
  }

  // iterate through array (starting at i === 1), determining F(i), and checking if it is greater than maxSoFar
  let maxSoFar = F0;
  let Fprevious = F0;
  for (let i = 1; i < L; i++) {
    const Fcurrent = Fprevious + sum - L * A[L - i];
    maxSoFar = Math.max(maxSoFar, Fcurrent);
    Fprevious = Fcurrent;
  }

  // return the highest value of F(i) captured by maxSoFar
  return maxSoFar;
}
```

### Time Complexity

Each of the two passes through the array will take linear time. Thus, the time complexity is `O(n)`.

### Space Complexity

The variables created by this algorithm do not vary in size based on `n`. Thus, this solution uses `O(1)` additional space.