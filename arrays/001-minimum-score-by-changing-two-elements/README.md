# Minimum Score by Changing Two Elements

**Topic:** Arrays | **Difficulty:** Medium | **Pattern:** Sorting + Greedy (Two Extremes) | **Source/Link:** [LeetCode 2567](https://leetcode.com/problems/minimum-score-by-changing-two-elements/)

---

## Problem

You are given a **0-indexed** integer array `nums`.

- The **score** of `nums` is defined as `max(nums) - min(nums)`.
- You can change at most **2 elements** (to any value you choose).

Return the **minimum possible score** after changing at most 2 elements.

**Example 1:**
```
Input:  nums = [1, 4, 3]
Output: 0
Explanation: Change nums[1] to 1 → [1, 1, 3] → score = 3 - 1 = 2.
             Or change both to 1 → score = 0.
```

**Example 2:**
```
Input:  nums = [1, 4, 7, 8, 5]
Output: 3
Explanation: After sorting: [1, 4, 5, 7, 8]
             Option A (remove 2 smallest): 8 - 5 = 3
             Option B (remove 2 largest):  5 - 1 = 4
             Option C (remove 1 each end): 7 - 4 = 3
             Minimum = 3
```

---

## My Approach (idea in plain words)

Sort the array. After sorting, the score is simply `nums[n-1] - nums[0]` (the gap between the two extremes). Changing 2 elements effectively lets us "eliminate" 2 extreme values. There are only three meaningful cases:

| Case | Elements removed | Remaining range |
|------|-----------------|-----------------|
| A | 2 smallest | `nums[n-1] - nums[2]` |
| B | 2 largest | `nums[n-3] - nums[0]` |
| C | 1 smallest + 1 largest | `nums[n-2] - nums[1]` |

Answer = `min(A, B, C)`.

> **Why only 3 cases?** Any other removal of 2 elements is dominated by one of these three — removing from the extremes always shrinks the range the most.

---

## What I Learned

**Core pattern — Sorting + Greedy (Two Extremes):**
Whenever a problem asks you to *minimize the maximum difference (range)* after changing or removing a limited number of elements, ask yourself:

1. **Can sorting help me identify the extremes?** After sorting, the range is always `nums[n-1] - nums[0]` — no scanning needed.
2. **How many extremes can I remove?** With 2 changes, there are exactly **3 cases** (remove 2 from left, 2 from right, or 1 from each end). Enumerate all cases; take the minimum.

**Don't memorize the three formulas — understand *why* they work:**
- Sorting collapses a messy range problem into a simple index-arithmetic problem.
- After sorting, the "dangerous" elements (the ones making the range large) are always at the two ends, so only the ends need to be touched.

**Recognition keywords for this pattern:**
> minimum range · maximum difference · minimize score · modify/change/remove k elements · minimize the largest difference

---

## Complexity

| | |
|---|---|
| **Time** | O(n log n) — dominated by sorting |
| **Space** | O(1) or O(n) depending on sort implementation (in-place vs. not) |

---

## Edge Cases

| Case | Behaviour |
|------|-----------|
| `n ≤ 2` | We can zero out the score entirely (change both extremes to the same value) → answer is **0** |
| `n = 3` | Only one element stays; the other two form both extremes → answer is **0** |
| All elements equal | Score is already 0; no changes needed |
| `k = 0` changes | Return `nums[n-1] - nums[0]` directly after sorting |

---

## Related Problems

| # | Problem | Pattern |
|---|---------|---------|
| [16](https://leetcode.com/problems/3sum-closest/) | 3Sum Closest | Sorting + Two Pointers |
| [15](https://leetcode.com/problems/3sum/) | 3Sum | Sorting + Two Pointers |
| [1984](https://leetcode.com/problems/minimum-difference-between-highest-and-lowest-of-k-scores/) | Minimum Difference Between Highest and Lowest of K Scores | Sorting + Sliding Window |
| [462](https://leetcode.com/problems/minimum-moves-to-equal-array-elements-ii/) | Minimum Moves to Equal Array Elements II | Sorting + Greedy |
| [2616](https://leetcode.com/problems/minimize-the-maximum-difference-of-pairs/) | Minimize the Maximum Difference of Pairs | Sorting + Binary Search + Greedy |
| [1671](https://leetcode.com/problems/minimum-number-of-removals-to-make-mountain-array/) | Minimum Number of Removals to Make Mountain Array | Greedy + DP |
| [2541](https://leetcode.com/problems/minimum-operations-to-make-array-equal-ii/) | Minimum Operations to Make Array Equal II | Greedy + Math |

---

## Practice Roadmap (sorted by recommended order)

Work through these in order to build intuition for the **Sorting + Greedy** family:

| Order | Problem | # | Pattern |
|-------|---------|---|---------|
| 1 | [3Sum Closest](https://leetcode.com/problems/3sum-closest/) | 16 | Sorting + Two Pointers |
| 2 | [3Sum](https://leetcode.com/problems/3sum/) | 15 | Sorting + Two Pointers |
| 3 | [Min Difference Between Highest and Lowest of K Scores](https://leetcode.com/problems/minimum-difference-between-highest-and-lowest-of-k-scores/) | 1984 | Sorting + Sliding Window |
| 4 | [Min Moves to Equal Array Elements II](https://leetcode.com/problems/minimum-moves-to-equal-array-elements-ii/) | 462 | Sorting + Greedy |
| 5 | [Minimize the Maximum Difference of Pairs](https://leetcode.com/problems/minimize-the-maximum-difference-of-pairs/) | 2616 | Sorting + Binary Search + Greedy |
| 6 | [Min Removals to Make Mountain Array](https://leetcode.com/problems/minimum-number-of-removals-to-make-mountain-array/) | 1671 | Greedy + DP |
| 7 | [Min Operations to Make Array Equal II](https://leetcode.com/problems/minimum-operations-to-make-array-equal-ii/) | 2541 | Greedy + Math |
