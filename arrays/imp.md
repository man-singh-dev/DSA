# C++ STL: unordered_set and unordered_map

## 1. unordered_set
An unordered_set stores **unique elements only**. Duplicate elements are automatically ignored. It uses hashing and does not maintain sorted order.

```cpp
#include <bits/stdc++.h>
using namespace std;

unordered_set<int> st;
st.insert(10);
st.insert(20);
st.insert(10); // ignored
```
Set contains: {10, 20} (order is not guaranteed).

### Important Operations
| Operation | Syntax | Meaning |
|---|---|---|
| Insert | `st.insert(x)` | Insert element |
| Search | `st.find(x)` | Returns iterator |
| Exists | `st.count(x)` | Returns 1 or 0 |
| Delete | `st.erase(x)` | Delete element |
| Size | `st.size()` | Number of unique elements |
| Empty | `st.empty()` | Check if empty |
| Clear | `st.clear()` | Remove all elements |

### Understanding find()
```cpp
if(st.find(10) != st.end()) cout << "Found";
else cout << "Not found";
```
- `find(x)` returns an iterator to x if present.
- `end()` represents the position after the last element.
- If find returns end(), the element does not exist.
- Alternative: `st.count(x) > 0`.

### Use Case 1: Find Duplicate Elements
Problem: Check if an array contains any duplicate.
Input: [1, 2, 3, 4, 2] → Output: true
```cpp
bool containsDuplicate(vector<int>& nums) {
    unordered_set<int> st;
    for(int x : nums) {
        if(st.find(x) != st.end()) return true;
        st.insert(x);
    }
    return false;
}
```
Idea: Before inserting, check whether the element already exists.

### Use Case 2: Check Whether an Element Exists
Input: arr = [10, 20, 30, 40], target = 30 → true
```cpp
bool exists(vector<int>& nums, int target) {
    unordered_set<int> st(nums.begin(), nums.end());
    return st.count(target) > 0;
}
```
Idea: Use set when you only care about existence, not frequency or index.

### Use Case 3: Find Distinct Elements
Input: [1, 2, 2, 3, 3, 4] → [1, 2, 3, 4]
```cpp
vector<int> distinctElements(vector<int>& nums) {
    unordered_set<int> st(nums.begin(), nums.end());
    vector<int> ans;
    for(int x : st) ans.push_back(x);
    return ans;
}
```
Note: Output order is not guaranteed.

## 2. unordered_map
An unordered_map stores **key-value pairs**. Each key is unique, but multiple keys can have the same value.
Syntax: `unordered_map<key_type, value_type> mp;`

Example:
```cpp
unordered_map<char, int> mp;
mp['a'] = 3;
mp['b'] = 2;
mp['c'] = 5;
```
Here, character is the key and integer is the value.

### Important Operations
| Operation | Syntax | Meaning |
|---|---|---|
| Insert/update | `mp[key] = value` | Set value |
| Increment | `mp[key]++` | Increase value |
| Access | `mp[key]` | Access value |
| Search | `mp.find(key)` | Find key |
| Exists | `mp.count(key)` | Returns 1 or 0 |
| Delete | `mp.erase(key)` | Delete key-value pair |
| Size | `mp.size()` | Number of keys |
| Clear | `mp.clear()` | Remove all pairs |

### Important: mp[key]++
```cpp
unordered_map<char, int> mp;
mp['a']++;
mp['a']++;
mp['b']++;
```
Result: a → 2, b → 1.
If a key does not exist, `mp[key]` creates it with a default value (0 for int), then increments it.

### Important Difference: find() vs []
```cpp
mp.find('z'); // Searches without inserting
mp['z'];      // Creates z with default value 0 if absent
```
In a map, `find()` searches for the KEY, not the value.
```cpp
if(mp.find('a') != mp.end()) cout << "Found";
```

### Iterating Through a Map
```cpp
for(auto it : mp) {
    cout << it.first << " -> " << it.second << endl;
}
```
- `it.first` = key
- `it.second` = value

## 3. Important unordered_map Use Cases

### Use Case 4: Count Frequency of Characters
Input: "banana"
Output: b → 1, a → 3, n → 2
```cpp
unordered_map<char, int> mp;
for(char ch : s) mp[ch]++;
```
Idea: Character → Frequency.

### Use Case 5: Count Frequency of Numbers
Input: [1, 2, 1, 3, 2, 1]
Output: 1 → 3, 2 → 2, 3 → 1
```cpp
unordered_map<int, int> mp;
for(int x : nums) mp[x]++;
```
Idea: Number → Frequency.

### Use Case 6: Store Element → Index (Two Sum)
LeetCode 1. Input: nums = [2, 7, 11, 15], target = 9 → [0, 1].
Observation: needed = target - current number. Check whether needed was seen earlier.
```cpp
vector<int> twoSum(vector<int>& nums, int target) {
    unordered_map<int, int> mp; // number -> index
    for(int i = 0; i < nums.size(); i++) {
        int needed = target - nums[i];
        if(mp.find(needed) != mp.end())
            return {mp[needed], i};
        mp[nums[i]] = i;
    }
    return {};
}
```
Dry run: For 2, needed = 7; not found, store 2 → 0. For 7, needed = 2; found at index 0. Return [0,1].
Important: Check needed BEFORE storing current number to avoid using the same element twice.

### Use Case 7: Find Most Frequent Element
Input: [1, 2, 2, 3, 3, 3, 4] → 3
```cpp
int mostFrequent(vector<int>& nums) {
    unordered_map<int, int> mp;
    for(int x : nums) mp[x]++;
    int maxFreq = 0, ans = -1;
    for(auto it : mp) {
        if(it.second > maxFreq) {
            maxFreq = it.second;
            ans = it.first;
        }
    }
    return ans;
}
```
Idea: First build frequency map, then find the key with maximum value.

### Use Case 8: Check Whether Two Strings Are Anagrams
LeetCode 242. Anagrams contain the same characters with the same frequencies.
Input: s = "listen", t = "silent" → true
```cpp
bool isAnagram(string s, string t) {
    if(s.size() != t.size()) return false;
    unordered_map<char, int> mp;
    for(char ch : s) mp[ch]++;
    for(char ch : t) mp[ch]--;
    for(auto it : mp) {
        if(it.second != 0) return false;
    }
    return true;
}
```
Idea: Increase frequency for first string, decrease for second. All frequencies must become zero.

## 4. unordered_set vs unordered_map

| Feature | unordered_set | unordered_map |
|---|---|---|
| Stores | Unique elements | Key-value pairs |
| Duplicate keys/elements | Not allowed | Keys unique |
| Main purpose | Existence / uniqueness | Store associated information |
| Insert | st.insert(x) | mp[key] = value |
| Search | st.find(x) | mp.find(key) |
| Access value | Not applicable | mp[key] |
| Example | Have I seen 5? | 5 appeared at index 2? |

### Most Important Recognition Rule
- Only need to check whether an element exists? → unordered_set.
- Need to store element → frequency? → unordered_map.
- Need to store element → index? → unordered_map.
- Need to remove duplicates? → unordered_set.
- Need to compare character frequencies? → unordered_map.

## 5. Time and Space Complexity

Average-case complexity (worst-case can be O(n) for individual hash operations).

| Operation | unordered_set | unordered_map |
|---|---|---|
| Insert | O(1) | O(1) |
| Search | O(1) | O(1) |
| Delete | O(1) | O(1) |
| Access by key | N/A | O(1) |
| Iterate all elements | O(n) | O(n) |

Space: O(n), where n is the number of stored elements/keys.
Note: O(1) means average-case, not guaranteed worst-case.

## 6. Common Mistakes
1. Assuming unordered containers maintain sorted or insertion order. They do not guarantee iteration order.
2. Using `mp[key]` just to check existence. It can create a new key. Use `find()` or `count()`.
3. Confusing key and value. `mp.find(x)` searches for key x, not value x.
4. Assuming map values must be unique. Different keys can have the same value.
5. Forgetting that set ignores duplicate insertions.
6. Using unordered_map when only existence is needed. A set is simpler.
7. Assuming every hash operation is always O(1). Worst case can be O(n).

## 7. Related LeetCode Practice

### unordered_set
| Problem | LeetCode No. | Main idea |
|---|---:|---|
| Contains Duplicate | 217 | Detect repeated values |
| Intersection of Two Arrays | 349 | Find common distinct elements |
| Happy Number | 202 | Detect repeated states |
| Longest Consecutive Sequence | 128 | Fast existence checks |

### unordered_map
| Problem | LeetCode No. | Main idea |
|---|---:|---|
| Two Sum | 1 | Number → Index |
| Majority Element | 169 | Number → Frequency |
| Valid Anagram | 242 | Character → Frequency |
| First Unique Character in a String | 387 | Character → Frequency |
| Top K Frequent Elements | 347 | Number → Frequency |
| Subarray Sum Equals K | 560 | Prefix sum → Frequency |
| Isomorphic Strings | 205 | Character mapping |

## 8. Quick Self-Test
1. Check if an array contains duplicates. → unordered_set.
2. Count each character in a string. → unordered_map.
3. Find two indices whose values add to target. → unordered_map.
4. Get unique elements from an array. → unordered_set.
5. Check whether two strings are anagrams. → unordered_map.
6. Find the most frequent number. → unordered_map.

## Final Revision
**unordered_set:** Stores unique elements. Use it for existence checks, duplicates, and distinct elements.

**unordered_map:** Stores key-value pairs. Use it for frequency, index, mappings, and associations.

Both use hashing and provide average O(1) insertion, search, and deletion.