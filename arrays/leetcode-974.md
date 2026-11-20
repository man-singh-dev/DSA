# 974. Subarray Sums Divisible by K

**Difficulty:** Medium
**Topics:** Array, Hash Table / Counting, Prefix Sum
**Final Complexity:** Time `O(n)` | Space `O(k)`

---

## Problem

Ek integer array `nums` aur integer `k` diya hai. Wo **non-empty subarrays** ki count return karo jinka sum `k` se divisible ho.

```
Input:  nums = [4,5,0,-2,-3,1], k = 5
Output: 7
```

Note: `nums` me negative numbers bhi ho sakte hain.

---

## Core Idea (jo maine use kiya)

Subarray `[l..r]` ka sum:

```
sum(l..r) = prefix[r] - prefix[l-1]
```

Ye sum `k` se divisible tabhi hoga jab:

```
(prefix[r] - prefix[l-1]) % k == 0
=> prefix[r] % k == prefix[l-1] % k
```

Yaani **dono prefix ka remainder same** hona chahiye.

So problem ban jaati hai:
> Har index `r` pe dekho, pehle kitne prefixes ka remainder wahi tha jo abhi ka hai. Utne hi valid subarrays `r` pe end honge.

### Steps

1. `count[k]` array rakho: `count[rem]` = ab tak kitne prefixes ka remainder `rem` aaya.
2. `count[0] = 1` se start karo (empty prefix, remainder 0). Isse wo subarrays count hote hain jo index 0 se start hote hain aur khud divisible hain.
3. Har element pe:
   - `prefix = (prefix + x) % k`, negative ho to positive banao
   - `ans += count[prefix]`
   - `count[prefix]++`

**Important:** pehle `ans` me add karo, phir `count++`. Warna khud ke saath pair ho jayega (empty subarray count ho jayegi).

---

## Code (C++)

```cpp
class Solution {
public:
    int subarraysDivByK(vector<int>& nums, int k) {
        // sum = prefix[r] - prefix[l-1]; divisible tabhi jab dono prefix ka remainder same ho
        vector<int> count(k, 0); // ab tak kitne prefix ka remainder r aya hai
        count[0] = 1;
        int prefix = 0;
        int ans = 0;
        for (int x : nums) {
            prefix = ((prefix + x) % k + k) % k; // negative remainder ko positive banao
            ans += count[prefix];
            count[prefix]++;
        }
        return ans;
    }
};
```

---

## Dry Run

`nums = [4,5,0,-2,-3,1], k = 5`

| x  | prefix % k | count[prefix] (before) | ans |
|----|-----------|------------------------|-----|
| -  | 0 (init)  | count[0] = 1           | 0   |
| 4  | 4         | 0                      | 0   |
| 5  | 4         | 1                      | 1   |
| 0  | 4         | 2                      | 3   |
| -2 | 2         | 0                      | 3   |
| -3 | 4         | 3                      | 6   |
| 1  | 0         | 1                      | 7   |

Answer = **7**

---

## Gotchas

- **Negative remainder:** C++ me `-3 % 5 = -3`. Isliye `((x % k) + k) % k` use karo. Python me ye automatic handle hota hai.
- **`count[0] = 1`** bhoolna nahi, nahi to prefix-se-start-hone wale subarrays miss ho jaayenge.
- **Overflow:** prefix ko har step pe mod karte rehne se overflow nahi hota.
- Ye **sliding window nahi chalega** kyunki negative numbers hain (monotonicity nahi hoti).

---

## Generalised Pattern: "Prefix Sum + HashMap / Counting"

### Trigger signals (ye dikhe to is pattern ka socho)

- "Count / find **subarray** with sum = something / divisible by something"
- Array me **negative numbers** hain (sliding window fail)
- Condition `prefix[r] - prefix[l-1]` ke form me likh sakte ho

### Template

Har question me ek hi sawal poochho:
> "Index `r` pe, mujhe pehle ke prefix `l-1` me kya chahiye?"

| Condition on subarray | Pehle ka prefix kaisa chahiye | Store kya karein |
|---|---|---|
| sum == K | `prefix[r] - K` | `map[prefix] -> count` |
| sum % K == 0 | `prefix[r] % K` (same remainder) | `count[remainder]` |
| sum % K == 0 aur length >= 2 | same remainder, index door ho | `map[rem] -> first index` |
| longest subarray sum == K | `prefix[r] - K` | `map[prefix] -> first index` |
| equal 0s and 1s | 0 ko -1 banao, sum == 0 | `map[prefix] -> first index` |
| XOR == K | `prefix_xor[r] ^ K` | `map[xor] -> count` |
| sum % P == target | `(prefix[r] - target) % P` | `map[rem] -> last index` |

### Rules of thumb

1. **Count chahiye** -> `map[value] -> frequency`
2. **Longest chahiye** -> `map[value] -> first index` (sirf pehli baar store karo)
3. **Shortest chahiye** -> `map[value] -> last index` (har baar update karo)
4. Hamesha `map[0] = 1` (count) ya `map[0] = -1` (index) se start karo.
5. **Lookup pehle, insert baad me.**
6. Divisibility wale questions me **remainder** store karo, sum nahi. Negative handle karo.
7. Agar sab elements non-negative hain aur sum <= target type ho, to **sliding window** bhi try karo (faster/less space).

### Generic code skeleton

```cpp
unordered_map<int,int> mp;
mp[0] = 1;                 // empty prefix
int prefix = 0, ans = 0;
for (int x : nums) {
    prefix += x;           // ya mod / xor / transform
    int need = prefix - K; // jo pehle chahiye
    if (mp.count(need)) ans += mp[need];
    mp[prefix]++;
}
```

---

## Related LeetCode Problems (is topic ko ace karne ke liye)

### Level 1: Basics (pehle ye karo)
| # | Problem | Kya seekhoge |
|---|---|---|
| 560 | Subarray Sum Equals K | Is pattern ka base. `prefix - K` lookup |
| 523 | Continuous Subarray Sum | Same remainder + length >= 2 (first index store) |
| 525 | Contiguous Array | 0 -> -1 trick, longest with sum 0 |
| 1493 / 724 | Find Pivot Index | Simple prefix sum warm-up |

### Level 2: Variations
| # | Problem | Kya seekhoge |
|---|---|---|
| 930 | Binary Subarrays With Sum | 560 ka binary version |
| 1248 | Count Number of Nice Subarrays | Odd count ko sum bana do |
| 1524 | Number of Sub-arrays With Odd Sum | Parity prefix counting |
| 437 | Path Sum III | Same idea tree par (backtrack karna mat bhoolna) |
| 1497 | Check If Array Pairs Are Divisible by k | Remainder pairing (r aur k-r) |
| 1442 | Count Triplets That Equal XOR | Prefix XOR version |

### Level 3: Harder / Tricky
| # | Problem | Kya seekhoge |
|---|---|---|
| 1590 | Make Sum Divisible by P | Remainder + shortest subarray remove (last index) |
| 2845 | Count of Interesting Subarrays | Modular counting with prefix |
| 1371 | Longest Substring Containing Vowels in Even Counts | Prefix + bitmask state |
| 1074 | Number of Submatrices That Sum to Target | 2D ko 1D prefix problem me reduce karo |
| 862 | Shortest Subarray with Sum at Least K | Negative numbers + monotonic deque |

### Suggested order

`560 -> 974 (done) -> 523 -> 525 -> 930 -> 1248 -> 1524 -> 437 -> 1590 -> 1442 -> 1371 -> 1074 -> 862`

---

## Quick Revision (one-liner)

> **Subarray sum ka condition prefix difference me convert karo, phir jo pehle chahiye usko hashmap/array me count/index ke saath store karo. Divisibility ho to remainder store karo, aur negative remainder ko fix karna mat bhoolna.**