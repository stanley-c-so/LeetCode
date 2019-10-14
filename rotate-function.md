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

## Initial Thoughts
