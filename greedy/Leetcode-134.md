
# LeetCode 134 – Gas Station

**Pattern:** Greedy Algorithm + Array  
**Difficulty:** Medium

## 1. What is the question asking?

We are given two arrays:

- `gas[i]` = Petrol available at station `i`.
- `cost[i]` = Petrol required to travel from station `i` to the next station.

The car starts with an empty tank. We can start from any station.

Find the starting index from which we can complete one full circular journey. If impossible, return `-1`.

## 2. My Approach: Net Fuel + Greedy

First, calculate the net fuel at every station:

`netFuel[i] = gas[i] - cost[i]`

Example:

```cpp
gas  = [1, 2, 3, 4, 5]
cost = [3, 4, 5, 1, 2]

netFuel = [-2, -2, -2, 3, 3]
```

Maintain three variables:

- `tank`: Current petrol balance from our possible starting point.
- `total`: Overall petrol balance of all stations.
- `start`: Our potential starting index.

### Greedy Logic

1. Add the current station's net fuel to `tank`.
2. Add the same value to `total`.
3. If `tank` becomes negative, our current starting point is invalid.
4. Set `start = i + 1`.
5. Reset `tank = 0` and try from the next station.
6. At the end, if `total < 0`, return `-1`; otherwise return `start`.

### Why does this work?

Suppose we start at station 2 and our tank becomes negative at station 5.

Then stations 2, 3, 4 and 5 cannot be valid starting points.

We can directly skip them and try station 6.

This is called **Greedy Elimination**.

## 3. Complete C++ Code

```cpp
class Solution {
public:
    int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {

        int tank = 0;
        int total = 0;
        int start = 0;

        for (int i = 0; i < gas.size(); i++) {

            // Petrol gained or lost at current station
            int diff = gas[i] - cost[i];

            // Overall petrol balance
            total += diff;

            // Current journey petrol balance
            tank += diff;

            // If petrol becomes negative
            if (tank < 0) {

                // Try the next station
                start = i + 1;

                // Reset tank
                tank = 0;
            }
        }

        // Check whether completing the journey is possible
        if (total < 0) {
            return -1;
        }

        return start;
    }
};
```

## 4. Dry Run

Input:

```cpp
gas  = [1, 2, 3, 4, 5]
cost = [3, 4, 5, 1, 2]
```

First calculate net fuel:

```cpp
netFuel = [-2, -2, -2, 3, 3]
```

Initial values:

```cpp
tank = 0;
total = 0;
start = 0;
```

| Index | Diff | Tank | Total | Action |
|---|---:|---:|---:|---|
| 0 | -2 | -2 | -2 | Reset tank, start = 1 |
| 1 | -2 | -2 | -4 | Reset tank, start = 2 |
| 2 | -2 | -2 | -6 | Reset tank, start = 3 |
| 3 | +3 | 3 | -3 | Continue |
| 4 | +3 | 6 | 0 | Continue |

Final values:

```cpp
tank = 6;
total = 0;
start = 3;
```

Since `total >= 0`, return `start`.

**Output: 3**

## 5. Complexity Analysis

- Time Complexity: O(n) — Only one traversal.
- Space Complexity: O(1) — Only three variables.

We don't need a separate `netFuel` vector because we can directly calculate `gas[i] - cost[i]`.

## 6. Important Learning From This Problem

The main learning is **Greedy Elimination**.

Whenever a running balance becomes negative, try to prove whether multiple starting possibilities can be eliminated together.

Instead of checking every possible starting point, we skip all the impossible ones.

Ask yourself:

"Can I eliminate multiple possibilities at once instead of checking each one individually?"

This is how you develop Greedy problem-solving intuition.

## 7. Similar LeetCode Questions to Practise

Solve these in order:

| Order | Problem | LeetCode No. | Pattern |
|---|---|---|---|
| 1 | Jump Game | 55 | Greedy Reachability |
| 2 | Maximum Subarray | 53 | Running Sum / Kadane's |
| 3 | Jump Game II | 45 | Greedy Range Expansion |
| 4 | Partition Labels | 763 | Greedy Boundaries |
| 5 | Candy | 135 | Two-pass Greedy |
| 6 | Minimize the Maximum Difference of Pairs | 2616 | Sorting + Greedy + Binary Search |

### Practice Links

1. https://leetcode.com/problems/jump-game/
2. https://leetcode.com/problems/maximum-subarray/
3. https://leetcode.com/problems/jump-game-ii/
4. https://leetcode.com/problems/partition-labels/
5. https://leetcode.com/problems/candy/
6. https://leetcode.com/problems/minimize-the-maximum-difference-of-pairs/

## 8. How to Recognize This Pattern in Future Problems

Look for these clues:

- You need to choose a valid starting point.
- A running balance changes at every index.
- You need to find whether a complete journey or sequence is possible.
- Once one attempt fails, you may be able to eliminate multiple candidates.
- You want to avoid checking every starting point individually.

**Practice Strategy:**

1. First, think of the brute-force approach.
2. Identify what makes a particular attempt fail.
3. Check whether that failure proves other attempts are also invalid.
4. Skip those possibilities using Greedy.
5. Dry-run your optimized code before submitting.

