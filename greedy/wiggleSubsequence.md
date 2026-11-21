
# LeetCode 376 — Wiggle Subsequence
**Difficulty:** Medium | **Topics:** Greedy, DP, Subsequence Reconstruction

## 1. Problem Statement
Given an integer array `nums`, return the length of the longest wiggle subsequence.
A wiggle sequence has consecutive differences that strictly alternate between positive and negative.
**Example 1:**
```cpp
Input:  [1,7,4,9,2,5]
Output: 6
Differences: +6,-3,+5,-7,+3
```
**Example 2:**
```cpp
Input:  [1,3,5,4,2,6,8,7]
Output: 5
One longest subsequence: [1,5,2,8,7]
Differences: +4,-3,+6,-1
```
A subsequence preserves the original order but allows us to skip elements.

## 2. Build the Intuition
A peak or valley depends on direction, NOT whether the value is positive or negative.
Example: `[2,8,4,10,6]`
- 2 -> 8: Up
- 8 -> 4: Down
- 4 -> 10: Up
- 10 -> 6: Down
**Greedy observation:** We only need turning points.
Consider `[1,3,5,4,2,6,8,7]`.
Differences: `+,+,-,-,+,+,-`
- Upward movement: keep the highest element.
- Downward movement: keep the lowest element.
One longest wiggle subsequence: `[1,5,2,8,7]`.
**Why?** A higher peak gives more room for a future downward movement. A lower valley gives more room for a future upward movement.

## 3. Approach A — Find Only the Length (Two Variables)
Maintain:
- `up`: Maximum wiggle length ending with an upward movement.
- `down`: Maximum wiggle length ending with a downward movement.
Initially, both are 1 because a single element is a valid subsequence.
**Transitions:**
1. If `nums[i] > nums[i-1]`: `up = down + 1` (an upward movement extends a downward-ending sequence).
2. If `nums[i] < nums[i-1]`: `down = up + 1` (a downward movement extends an upward-ending sequence).
3. If equal: Do nothing.
**C++ Code:**
```cpp
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        int n = nums.size();
        if (n < 2) return n;
        int up = 1, down = 1;
        for (int i = 1; i < n; i++) {
            if (nums[i] > nums[i-1])
                up = down + 1;
            else if (nums[i] < nums[i-1])
                down = up + 1;
        }
        return max(up, down);
    }
};
```
**Dry Run:** `nums = [1,3,5,4,2,6,8,7]`
| Comparison | Direction | up | down |
|---|---|---:|---:|
| Start | — | 1 | 1 |
| 1 -> 3 | Up | 2 | 1 |
| 3 -> 5 | Up | 2 | 1 |
| 5 -> 4 | Down | 2 | 3 |
| 4 -> 2 | Down | 2 | 3 |
| 2 -> 6 | Up | 4 | 3 |
| 6 -> 8 | Up | 4 | 3 |
| 8 -> 7 | Down | 4 | 5 |
**Answer:** `max(up,down) = 5`
**Complexity:** Time `O(n)`, Space `O(1)`.

## 4. Approach B — Print the Actual Subsequence
The two-variable approach only returns the length. To print the subsequence, store the turning points in a vector.
**My Greedy Approach:**
- `res`: Stores the selected elements.
- `prevDiff`: Stores the direction of the last selected wiggle movement.
- Direction flips: Add the current element.
- Same direction: Replace the last element with the current element.
- Equal elements: Ignore.
**C++ Code (safe difference calculation):**
```cpp
class Solution {
public:
    vector<int> wiggleSubsequence(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return {};
        vector<int> res;
        res.push_back(nums[0]);
        long long prevDiff = 0;
        for (int i = 1; i < n; i++) {
            long long diff = (long long)nums[i] - nums[i-1];
            if (diff == 0) continue;
            if ((diff > 0 && prevDiff <= 0) ||
                (diff < 0 && prevDiff >= 0)) {
                // Direction flipped: add new turning point
                res.push_back(nums[i]);
                prevDiff = diff;
            }
            else {
                // Same direction: replace previous endpoint
                res.back() = nums[i];
            }
        }
        return res;
    }
};
```
To print:
```cpp
vector<int> res = wiggleSubsequence(nums);
for (int x : res) cout << x << " ";
```
**Note:** LeetCode 376 expects an integer return value. This is a separate reconstruction function for the follow-up: "Print one longest wiggle subsequence."

## 5. Dry Run — Reconstruction
Input: `[1,3,5,4,2,6,8,7]`
Initially: `res = [1]`, `prevDiff = 0`
| Comparison | Diff | Action | res |
|---|---:|---|---|
| Start | — | Add first element | [1] |
| 1 -> 3 | +2 | Add | [1,3] |
| 3 -> 5 | +2 | Replace endpoint | [1,5] |
| 5 -> 4 | -1 | Add | [1,5,4] |
| 4 -> 2 | -2 | Replace endpoint | [1,5,2] |
| 2 -> 6 | +4 | Add | [1,5,2,6] |
| 6 -> 8 | +2 | Replace endpoint | [1,5,2,8] |
| 8 -> 7 | -1 | Add | [1,5,2,8,7] |
**Final subsequence:** `[1,5,2,8,7]`, length = 5.
**Why update `prevDiff` only on a direction flip?** It represents the direction of the last selected wiggle movement. Replacing an endpoint does not change the direction; adding a turning point does.

## 6. Compare Both Approaches
| Feature | Length solution | Reconstruction |
|---|---|---|
| Output | Length | Actual subsequence |
| Variables | `up`, `down` | `res`, `prevDiff` |
| Time | `O(n)` | `O(n)` |
| Space | `O(1)` | `O(n)` |

## 7. Important Takeaways
1. Peak/valley depends on direction, not positive/negative values.
2. Compare adjacent elements to determine Up, Down or Equal.
3. Same-direction movement does not increase wiggle length.
4. During an upward run, keep the highest endpoint.
5. During a downward run, keep the lowest endpoint.
6. For length only, use two variables.
7. For reconstruction, store turning points.
8. Ignore equal adjacent elements.
9. A subsequence preserves original order; never sort.
10. Use `long long` for differences to avoid integer overflow.

## 8. Similar Problems to Practice
| Problem | LC | Main Learning |
|---|---:|---|
| Best Time to Buy and Sell Stock II | 122 | Greedy on consecutive increases |
| Increasing Triplet Subsequence | 334 | Maintain useful candidates |
| Longest Increasing Subsequence | 300 | Subsequence DP and binary search |
| Non-decreasing Subsequences | 491 | Generate subsequences |
| Maximum Length of Pair Chain | 646 | Greedy subsequence selection |
| Longest Common Subsequence | 1143 | DP and reconstruction |
| Longest Arithmetic Subsequence | 1027 | DP state design |
| Longest Bitonic Subsequence | 1671 | Increasing and decreasing subsequences |

**Suggested order:** LC 122 -> LC 334 -> LC 300 -> LC 1671 -> LC 1143.

## 9. Interview Explanation
"We need consecutive differences to alternate in sign. For length only, we maintain the best upward and downward wiggle lengths. An upward movement extends a downward-ending sequence, and a downward movement extends an upward-ending sequence. Equal elements do not contribute. This gives O(n) time and O(1) space. If we need the actual subsequence, we store turning points: replace the endpoint when direction continues and append when direction changes."

**Final distinction:**
- `up` and `down` -> Calculate the length.
- `res` and `prevDiff` -> Reconstruct the subsequence.
