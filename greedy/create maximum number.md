# LeetCode 321 - Create Maximum Number

## 1. Problem
Given two arrays `nums1` and `nums2`, create the maximum possible number of length `k` by selecting digits from both arrays. The relative order of selected digits from each array must remain unchanged.

**Important:** We maximize the number lexicographically, NOT the sum of selected elements.

Example:
```cpp
nums1 = [3, 4, 6, 5]
nums2 = [9, 1, 2, 5, 8, 3]
k = 5

Output = [9, 8, 6, 5, 3]
```

## 2. Core Approach

Break the problem into 3 parts:

1. Try every valid split: take `i` elements from `nums1` and `k-i` from `nums2`.
2. Find the maximum subsequence from each array using a Greedy + Monotonic Stack.
3. Merge both subsequences optimally using Lexicographical Comparison.

---

## 3. Find the Valid Split Range

Let:
- `n1 = nums1.size()`
- `n2 = nums2.size()`
- `i` = elements selected from nums1.
- `k-i` = elements selected from nums2.

**Minimum elements from nums1:**
```cpp
int start = max(0, k - n2);
```

Why?
- We need `k` elements total.
- nums2 can provide at most `n2` elements.
- If nums2 cannot provide all `k`, the remaining elements MUST come from nums1.
- We cannot take negative elements, hence `max(0, ...)`.

**Maximum elements from nums1:**
```cpp
int end = min(k, n1);
```

Why?
- We cannot take more than `k` total elements.
- We cannot take more than `n1` elements from nums1.

Example:
```cpp
n1 = 4, n2 = 6, k = 5

start = max(0, 5-6) = 0
end = min(5, 4) = 4
```

Possible splits:

| nums1 | nums2 |
|---:|---:|
| 0 | 5 |
| 1 | 4 |
| 2 | 3 |
| 3 | 2 |
| 4 | 1 |

---

## 4. getMax() - Maximum Lexicographical Subsequence of Length K

This is NOT maximum-sum subsequence.

Example:
```cpp
nums = [3, 4, 6, 5]
k = 2

Answer = [6, 5]
```

We must preserve relative order. We cannot sort the array.

### Approach: Greedy + Monotonic Stack

- We are allowed to delete `n-k` elements.
- If current element is greater than the stack's last element, remove the smaller previous element.
- Keep removing while deletion budget remains.
- Push current element.
- Finally, keep exactly `k` elements.

```cpp
vector<int> getMax(vector<int>& nums, int k) {

    vector<int> st;

    int drop = nums.size() - k;

    for (int x : nums) {

        while (drop > 0 && !st.empty() && st.back() < x) {
            st.pop_back();
            drop--;
        }

        st.push_back(x);
    }

    st.resize(k);

    return st;
}
```

### Important: How does resize(k) work?

- If vector size is greater than `k`, `resize(k)` removes elements from the END.
- It does NOT sort the vector.
- It does NOT search for the biggest elements.
- If size is smaller than `k`, it adds default-initialized elements. That should not happen here because we process every input element and have exactly `n-k` deletions available.

Example:
```cpp
vector<int> st = {9, 8, 7, 6};

st.resize(2);

// st = [9, 8]
```

### getMax Dry Run

Input:
```cpp
nums = [3, 4, 6, 5]
k = 2
```

Initially:
```cpp
drop = 4 - 2 = 2
```

| Current x | Action | Stack | drop |
|---|---|---|---:|
| 3 | Push 3 | [3] | 2 |
| 4 | Pop 3, push 4 | [4] | 1 |
| 6 | Pop 4, push 6 | [6] | 0 |
| 5 | Push 5 | [6,5] | 0 |

Final answer: `[6,5]`

---

## 5. greater() - Compare Two Remaining Subsequences

Purpose: Check whether the remaining part of array `a` is lexicographically greater than the remaining part of array `b`.

```cpp
bool greater(vector<int>& a, int i,
             vector<int>& b, int j) {

    while (i < a.size() && j < b.size() && a[i] == b[j]) {
        i++;
        j++;
    }

    if (j == b.size()) return true;

    if (i < a.size() && a[i] > b[j]) return true;

    return false;
}
```

### Parameters

- `a`, `b`: Two subsequences.
- `i`: Starting index in `a`.
- `j`: Starting index in `b`.

Example:
```cpp
greater(a, 0, b, 0);
```
Compares both complete arrays.

```cpp
greater(a, 1, b, 2);
```
Compares only the remaining suffixes starting at those indices.

### Line-by-line explanation

**1. While loop**
```cpp
while (i < a.size() && j < b.size() && a[i] == b[j]) {
    i++;
    j++;
}
```
- Both arrays should have remaining elements.
- Current elements should be equal.
- If equal, move both pointers forward.
- Equal digits cannot decide which sequence is greater.

**2. First condition**
```cpp
if (j == b.size()) return true;
```
If `b` finishes first, `a` is at least as good.

Example:
```cpp
a = [9, 8]
b = [9]
```
After comparing 9 with 9, `b` finishes. So `a` is greater.

If both finish together, they are equal. Returning true is also valid.

**3. Second condition**
```cpp
if (i < a.size() && a[i] > b[j]) return true;
```
If `a` still has elements and its first unequal element is greater, return true.

**4. Finally**
```cpp
return false;
```
Otherwise, `b` is greater.

### greater() Dry Run 1

```cpp
a = [6, 7, 5]
b = [6, 5, 9]

greater(a, 0, b, 0);
```

- Compare 6 and 6: equal, so `i=1, j=1`.
- Compare 7 and 5: not equal.
- `b` has not ended.
- `7 > 5`, so return true.

Therefore, `a` is greater.

### greater() Dry Run 2

```cpp
a = [5, 6, 7, 8]
b = [5, 6, 9, 1]
```

- 5 == 5, advance.
- 6 == 6, advance.
- Compare 7 and 9.
- 7 > 9 is false.
- Return false.

Therefore, `b` is greater.

### Prefix Cases to Remember

| a | b | greater(a,0,b,0) |
|---|---|---|
| [9,8] | [9] | true |
| [9] | [9,8] | false |
| [6,5] | [6,5] | true |

---

## 6. merge() - Merge Two Subsequences Optimally

At every step:
1. Compare remaining suffixes using `greater()`.
2. Take the next element from the lexicographically greater suffix.
3. Advance that array's pointer.
4. Continue until both arrays are exhausted.

```cpp
vector<int> merge(vector<int>& a, vector<int>& b) {

    vector<int> result;

    int i = 0, j = 0;

    while (i < a.size() || j < b.size()) {

        if (greater(a, i, b, j)) {
            result.push_back(a[i]);
            i++;
        }
        else {
            result.push_back(b[j]);
            j++;
        }
    }

    return result;
}
```

### merge() Dry Run

```cpp
a = [6,5]
b = [9,8,3]
```

| Remaining a | Remaining b | Chosen | Result |
|---|---|---:|---|
| [6,5] | [9,8,3] | 9 | [9] |
| [6,5] | [8,3] | 8 | [9,8] |
| [6,5] | [3] | 6 | [9,8,6] |
| [5] | [3] | 5 | [9,8,6,5] |
| [] | [3] | 3 | [9,8,6,5,3] |

Final merged result: `[9,8,6,5,3]`.

### Why compare the whole suffix instead of only current digits?

Example:
```cpp
a = [6,7]
b = [6,5]
```

Both current digits are 6.

If we compare only current elements, we cannot decide.

But comparing the complete suffix:
- 6 == 6
- 7 > 5

Therefore, choose from `a`.

---

## 7. Main Function - Try Every Split

For every valid split:

1. Take `i` elements from nums1.
2. Take `k-i` elements from nums2.
3. Find maximum subsequences using `getMax()`.
4. Merge them using `merge()`.
5. Compare candidate with current answer using `greater()`.
6. Update answer if candidate is greater.

```cpp
vector<int> maxNumber(vector<int>& nums1,
                      vector<int>& nums2, int k) {

    int n1 = nums1.size();
    int n2 = nums2.size();

    int start = max(0, k - n2);
    int end = min(k, n1);

    vector<int> ans;

    for (int i = start; i <= end; i++) {

        vector<int> a = getMax(nums1, i);

        vector<int> b = getMax(nums2, k - i);

        vector<int> candidate = merge(a, b);

        if (greater(candidate, 0, ans, 0)) {
            ans = candidate;
        }
    }

    return ans;
}
```

---

## 8. Complete C++ Solution

```cpp
class Solution {
public:

    vector<int> getMax(vector<int>& nums, int k) {

        vector<int> st;

        int drop = nums.size() - k;

        for (int x : nums) {

            while (drop > 0 && !st.empty() && st.back() < x) {
                st.pop_back();
                drop--;
            }

            st.push_back(x);
        }

        st.resize(k);

        return st;
    }


    bool greater(vector<int>& a, int i,
                 vector<int>& b, int j) {

        while (i < a.size() && j < b.size() && a[i] == b[j]) {
            i++;
            j++;
        }

        if (j == b.size()) return true;

        if (i < a.size() && a[i] > b[j]) return true;

        return false;
    }


    vector<int> merge(vector<int>& a, vector<int>& b) {

        vector<int> result;

        int i = 0, j = 0;

        while (i < a.size() || j < b.size()) {

            if (greater(a, i, b, j)) {
                result.push_back(a[i]);
                i++;
            }
            else {
                result.push_back(b[j]);
                j++;
            }
        }

        return result;
    }


    vector<int> maxNumber(vector<int>& nums1,
                          vector<int>& nums2, int k) {

        int n1 = nums1.size();
        int n2 = nums2.size();

        int start = max(0, k - n2);
        int end = min(k, n1);

        vector<int> ans;

        for (int i = start; i <= end; i++) {

            vector<int> a = getMax(nums1, i);

            vector<int> b = getMax(nums2, k - i);

            vector<int> candidate = merge(a, b);

            if (greater(candidate, 0, ans, 0)) {
                ans = candidate;
            }
        }

        return ans;
    }
};
```

---

## 9. Things I Got Stuck On - Revision Checklist

- [ ] `start = max(0, k-n2)`: Minimum elements we MUST take from nums1 because nums2 may not have enough.
- [ ] `end = min(k,n1)`: Maximum elements we CAN take from nums1.
- [ ] `drop = nums.size()-k`: Number of elements we are allowed to delete.
- [ ] `st.back() < x`: Remove a smaller previous digit when a bigger current digit can improve an earlier position.
- [ ] `drop > 0`: Never delete more than the allowed number.
- [ ] `resize(k)`: If vector is longer than k, removes elements from the END. It does not sort or find the largest values.
- [ ] `greater(a,i,b,j)`: Compares suffixes starting at `i` and `j`, not necessarily complete arrays.
- [ ] `while` in greater(): Skip equal leading digits; the first unequal digit decides.
- [ ] `if (j == b.size()) return true`: If b ends first, a is at least as good. Equal suffixes can also return true.
- [ ] `merge()`: Compare complete remaining suffixes, not just `a[i]` and `b[j]`.
- [ ] `greater(candidate,0,ans,0)`: Compare the newly merged candidate with the best answer so far.
- [ ] This is maximum lexicographical subsequence, NOT maximum-sum subsequence.
- [ ] Do not sort either input array because original relative order must be preserved.

## 10. Common Mistakes

1. Taking globally largest k digits and sorting them: breaks relative order.
2. Comparing only `a[i]` and `b[j]` during merge: fails when current digits are equal.
3. Forgetting `drop > 0`: may delete too many elements.
4. Forgetting `st.resize(k)`: may return more than k elements.
5. Trying only one split: optimal answer may require a different distribution between arrays.
6. Treating getMax() as maximum-sum subsequence: it maximizes lexicographical order.

## 11. Complexity

Let:
- `n = nums1.size()`
- `m = nums2.size()`
- `k` = required output length.

- `getMax()` = O(n) or O(m).
- One merge = O(k²) worst case because suffix comparisons can take O(k), and happen up to k times.
- Number of splits = at most `min(k,n)+1`.
- Auxiliary space = O(k) for subsequences, merged candidate and answer (excluding input).

## 12. Similar Problems to Practice

1. **LeetCode 402 - Remove K Digits:** Monotonic stack + greedy deletion.
2. **LeetCode 1673 - Find the Most Competitive Subsequence:** Maximum subsequence of fixed length.
3. **LeetCode 316 - Remove Duplicate Letters:** Monotonic stack with constraints.
4. **LeetCode 1081 - Smallest Subsequence of Distinct Characters:** Related monotonic stack.
5. **LeetCode 321 again:** Implement getMax(), greater(), and merge() independently.

## 13. My Revision Order

1. First write `getMax()` from memory.
2. Dry-run `greater()` on equal-prefix examples.
3. Write `merge()` and explain why it compares suffixes.
4. Finally write the split loop in `maxNumber()`.
5. Solve the complete problem without looking at notes.

**Main takeaway:** LeetCode 321 combines Greedy + Monotonic Stack + Two Pointers + Lexicographical Comparison. Understand the responsibility of each function instead of memorising the complete code.
