
# Container With Most Water — Two Pointers

## 1. My Approach / Core Idea

**Start from both ends, calculate the current area, store the maximum, and move one pointer according to a condition.**

### Steps:

1. Initialize `left = 0` and `right = n - 1`.
2. Calculate the current container:
   - `currentHeight = min(height[left], height[right])`
   - `currentWidth = right - left`
   - `currentArea = currentHeight * currentWidth`
3. Update the maximum: `maxWater = max(maxWater, currentArea)`.
4. Compare the heights at both pointers:
   - If `height[left] < height[right]`, move `left++`.
   - Otherwise, move `right--`.
5. Continue while `left < right`.
6. Return `maxWater`.

### Why move the smaller-height pointer?

- Water level depends on the smaller wall.
- Width decreases whenever we move a pointer.
- Moving the taller wall cannot improve the area while the shorter wall remains the limiting height.
- Therefore, move the smaller wall in search of a taller one.

**Remember: Calculate → Update Maximum → Move the limiting pointer → Repeat.**

---

## 2. Code

```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int left = 0;
        int right = height.size() - 1;
        int maxWater = 0;

        while (left < right) {
            int currentHeight = min(height[left], height[right]);
            int currentWidth = right - left;
            int currentArea = currentHeight * currentWidth;

            maxWater = max(maxWater, currentArea);

            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }

        return maxWater;
    }
};
```

---

## 3. Pattern Used: Two Pointers (Opposite Direction)

### When to use this pattern?

- One pointer starts at the beginning.
- Another pointer starts at the end.
- We calculate something using both pointers.
- We update an answer (maximum, minimum, count, etc.).
- A condition decides which pointer should move.

### General Template

```cpp
int left = 0;
int right = n - 1;
int ans = initialValue;

while (left < right) {

    // 1. Calculate using left and right

    // 2. Update the answer

    // 3. Move one pointer based on a condition
}

return ans;
```

### Complexity

- Time Complexity: O(n)
- Space Complexity: O(1)

---

## 4. Related LeetCode Questions (Increasing Difficulty)

| Order | Problem | Concept |
|---|---|---|
| 1 | Two Sum II | Move pointers based on sum |
| 2 | Valid Palindrome | Compare both ends |
| 3 | Reverse String | Swap both ends |
| 4 | Container With Most Water | Calculate area and move smaller pointer |
| 5 | 3Sum | Sorting + Two Pointers |
| 6 | 3Sum Closest | Track closest answer |
| 7 | Trapping Rain Water | Left and right maximum boundaries |
| 8 | Boats to Save People | Pair lightest and heaviest |
| 9 | Valid Triangle Number | Count valid combinations |

### LeetCode Links

1. [Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
2. [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)
3. [Reverse String](https://leetcode.com/problems/reverse-string/)
4. [Container With Most Water](https://leetcode.com/problems/container-with-most-water/)
5. [3Sum](https://leetcode.com/problems/3sum/)
6. [3Sum Closest](https://leetcode.com/problems/3sum-closest/)
7. [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)
8. [Boats to Save People](https://leetcode.com/problems/boats-to-save-people/)
9. [Valid Triangle Number](https://leetcode.com/problems/valid-triangle-number/)

---

## 5. Questions to Ask Yourself Before Coding

1. Where should left and right start?
2. What am I calculating using both pointers?
3. Do I need to update maximum, minimum, count, or a boolean answer?
4. Which pointer should move, and WHY?
5. Should I move one pointer or both?


