# Longest Product Equivalent Subarray

## Problem

You are given an array of positive integers `nums`.

An array `arr` is called **product equivalent** if:

```
prod(arr) == lcm(arr) * gcd(arr)
```

where:

- `prod(arr)` is the product of all elements of `arr`
- `gcd(arr)` is the GCD of all elements of `arr`
- `lcm(arr)` is the LCM of all elements of `arr`

Return the length of the longest product equivalent **subarray** of `nums`.

### Examples

| Input | Output | Explanation |
|---|---|---|
| `[1,2,1,2,1,1,1]` | `5` | `[1,2,1,1,1]` has prod = 2, gcd = 1, lcm = 2, so 2 = 2 * 1 |
| `[2,3,4,5,6]` | `3` | `[3,4,5]` has prod = 60, gcd = 1, lcm = 60 |
| `[1,2,3,1,4,5,1]` | `5` | `[3,1,4,5,1]` has prod = 60, gcd = 1, lcm = 60 |

### Constraints

- `2 <= nums.length <= 100`
- `1 <= nums[i] <= 10`

---

## Approach

The constraints are small (`n <= 100`), so brute force over all subarrays works.

1. Fix a starting index `i`.
2. Extend the end index `j` one element at a time, maintaining a running `product`, `gcd` and `lcm`.
3. Whenever `product == gcd * lcm`, update the answer with `j - i + 1`.
4. **Break early** once `product` gets too large (this also prevents overflow).

### Running updates

- `product *= x`
- `g = gcd(g, x)` (start with `g = 0`, since `gcd(0, x) = x`)
- `l = l / gcd(l, x) * x` (divide first to keep numbers small)

### Why the early break is valid

1. **Right side is bounded.**
   - `lcm` of numbers in 1..10 divides `lcm(1..10) = 2520`, so `lcm <= 2520`.
   - `gcd <= 10`.
   - So `lcm * gcd <= 25200` for every subarray, regardless of length.
2. **Left side never decreases.** Every element is `>= 1`, so the product is non-decreasing as the subarray grows.
3. **Conclusion.** Once `product > 25200`, it can never equal `lcm * gcd` for any longer subarray from the same start, so we stop.

### Why overflow is impossible

Before each multiplication, `product <= 100000` (otherwise we would have broken already). After multiplying by a value `<= 10`, it is at most `10^6`, which fits easily in `long long`.

### Complexity

- Time: `O(n^2 log M)` in the worst case, usually much less because of the early break (`M = 10`).
- Space: `O(1)`.

---

## Solution (C++)

```cpp
class Solution {
public:
    int maxLength(vector<int>& nums) {
        int n = nums.size();
        int ans = 0;
        for (int i = 0; i < n; i++) {
            long long product = 1;
            long long g = 0;
            long long l = 1;
            for (int j = i; j < n; j++) {
                product *= nums[j];
                g = gcd(g, (long long)nums[j]);
                l = l / gcd(l, (long long)nums[j]) * nums[j];
                if (product == g * l) {
                    ans = max(ans, j - i + 1);
                }
                if (product > 100000) break;  // can never equal g * l (<= 25200) again
            }
        }
        return ans;
    }
};
```

### Solution (Python)

```python
from math import gcd

class Solution:
    def maxLength(self, nums):
        n = len(nums)
        best = 0
        for i in range(n):
            p, g, l = 1, 0, 1
            for j in range(i, n):
                x = nums[j]
                p *= x
                g = gcd(g, x)
                l = l * x // gcd(l, x)
                if p == l * g:
                    best = max(best, j - i + 1)
                if p > 10**5:
                    break
        return best
```

---

## Common Mistake

The first attempt had no early break and the product overflowed `long long`:

```
runtime error: signed integer overflow: 7621912650240000000 * 5
```

With 100 elements up to 10, the product can reach `10^100`. Brute force logic was correct; it just kept computing a value that could no longer matter.

---

## Key Takeaways for Future Problems

1. **Check for overflow before coding.** If a running product, sum of powers, or factorial appears, estimate its worst case against the type's limit (`long long` is about 9.2 * 10^18).
2. **Bound the other side of the comparison.** Ask: "What is the maximum the thing I am comparing against can ever be?" Here, `lcm * gcd <= 25200`.
3. **Look for a monotonic quantity.** If one side only goes up (or only down) as the window grows, and the other side is bounded, you can stop as soon as it crosses the bound.
4. **Use constraints as hints.** Values `<= 10` implies small LCM and GCD bounds, which is what makes the cutoff possible.
5. **Do the check before the break.** Test the condition at the current `j`, then break, so you do not miss a valid subarray that ends exactly at the cutoff.
6. **Safe LCM formula.** Use `l / gcd(l, x) * x`, not `l * x / gcd(l, x)`, to avoid needless overflow.
7. **Small `n` means brute force is fine.** With `n <= 100`, an `O(n^2)` scan over subarrays is the intended solution; do not overthink it.