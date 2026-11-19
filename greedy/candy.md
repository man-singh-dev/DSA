
# LeetCode 135 — Candy

**Difficulty:** Hard  
**Topic:** Greedy + Array  
**Technique:** Two Passes (Left to Right + Right to Left)

## 1. Problem Explanation

We are given an array `ratings[]` representing the ratings of children.

We have to distribute candies according to these rules:

1. Every child must get at least 1 candy.
2. A child with a higher rating than their immediate neighbor must get more candies than that neighbor.
3. We need to return the minimum total number of candies required.

**Goal:** Find the minimum total candies while satisfying both conditions.

---

## 2. My Approach / Core Idea

My initial idea was:

- Start from the right side.
- Give the last child 1 candy.
- Move towards the left.
- If the current rating is greater than the next rating, give current child one more candy.
- Otherwise, give the current child 1 candy.

**Problem with this approach:**

Checking only from right to left cannot handle increasing ratings properly.

Therefore, we need TWO PASSES.

### Correct Approach: Greedy + Two Passes

**Step 1: Initialize**

Give every child 1 candy.

```cpp
vector<int> candies(n, 1);
```

**Step 2: Left to Right**

Check whether the current child has a higher rating than the previous child.

Condition:

```cpp
if(ratings[i] > ratings[i-1])
```

Then:

```cpp
candies[i] = candies[i-1] + 1;
```

This handles increasing ratings.

**Step 3: Right to Left**

Now check whether the current child has a higher rating than the next child.

Condition:

```cpp
if(ratings[i] > ratings[i+1])
```

Then:

```cpp
candies[i] = max(candies[i], candies[i+1] + 1);
```

This handles decreasing ratings.

**Step 4: Calculate Total**

Add all the candies and return the sum.

---

## 3. WHY DO WE USE MAX()?

This is the most important concept in this question.

```cpp
candies[i] = max(candies[i], candies[i+1] + 1);
```

**Reason:**

- First pass already assigns candies according to the left neighbor.
- Second pass assigns candies according to the right neighbor.
- We must not decrease the candies assigned in the first pass.
- Therefore, we take the maximum of both requirements.

### Remember:

`max(existing candies, candies required by right neighbor)`

**Example:**

If:
- Already assigned = 4
- Right neighbor condition requires = 3

Then:

`max(4, 3) = 4`

We keep 4 because it already satisfies the stronger requirement.

**Important Greedy Concept:**

When multiple conditions impose minimum requirements, take the maximum of those requirements.

---

## 4. C++ Code

```cpp
class Solution {
public:
    int candy(vector<int>& ratings) {
        int n = ratings.size();

        vector<int> candies(n, 1);

        // First Pass: Left to Right
        for(int i = 1; i < n; i++) {
            if(ratings[i] > ratings[i-1]) {
                candies[i] = candies[i-1] + 1;
            }
        }

        // Second Pass: Right to Left
        for(int i = n-2; i >= 0; i--) {
            if(ratings[i] > ratings[i+1]) {
                candies[i] = max(candies[i], candies[i+1] + 1);
            }
        }

        // Calculate total candies
        int ans = 0;

        for(int x : candies) {
            ans += x;
        }

        return ans;
    }
};
```

---

## 5. Pattern / Technique to Remember

### Greedy + Two Passes

**When to use this technique?**

- When an element has conditions involving both its left and right neighbors.
- When checking only one direction is insufficient.
- When we need to satisfy multiple minimum requirements.
- When we can process the array in two directions and preserve previous results using `max()`.

### General Steps

1. Initialize all values with the minimum possible value.
2. Traverse Left to Right and satisfy left-side conditions.
3. Traverse Right to Left and satisfy right-side conditions.
4. Use `max()` to preserve the stronger requirement.
5. Calculate the final answer.

### Complexity

- Time Complexity: O(n) — Two traversals of the array.
- Space Complexity: O(n) — Extra candies array.

---

## 6. Related LeetCode Problems You Should Solve

| Order | Problem | Main Concept |
|---|---|---|
| 1 | [Increasing Triplet Subsequence](https://leetcode.com/problems/increasing-triplet-subsequence/) | Greedy |
| 2 | [Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/) | Greedy |
| 3 | [Jump Game](https://leetcode.com/problems/jump-game/) | Greedy + Maximum Reach |
| 4 | [Jump Game II](https://leetcode.com/problems/jump-game-ii/) | Greedy |
| 5 | [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/) | Greedy + Sorting |
| 6 | [Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/) | Greedy + Intervals |
| 7 | [Gas Station](https://leetcode.com/problems/gas-station/) | Greedy |
| 8 | [Wiggle Subsequence](https://leetcode.com/problems/wiggle-subsequence/) | Greedy |
| 9 | [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) | Two Pointers + Greedy |

---

## 7. Questions to Ask Yourself Before Coding

1. Does every element have a minimum starting value?
2. Does the current element depend on its left neighbor?
3. Does it also depend on its right neighbor?
4. Can I satisfy one condition in the first pass?
5. Can I satisfy the other condition in the second pass?
6. Will updating directly overwrite an already valid answer?
7. Should I use `max()` to preserve the stronger requirement?

---

## 8. Final Revision Notes

**Problem:** LeetCode 135 — Candy

**Pattern:** Greedy + Two Passes

**Left to Right:** Handle increasing ratings.

**Right to Left:** Handle decreasing ratings.

**max():** Never decrease the candies already assigned.

**Time:** O(n)

**Space:** O(n)

**One-line memory trick:**

First satisfy the left neighbor, then satisfy the right neighbor without destroying the previous assignment.
