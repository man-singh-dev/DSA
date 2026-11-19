
# LeetCode 179 — Largest Number

**Difficulty:** Medium  
**Topics:** Greedy + Sorting + Custom Comparator + Strings

**Problem Link:** https://leetcode.com/problems/largest-number/

---

## 1. Problem Explanation

We are given an array of non-negative integers.

We need to rearrange these numbers to form the **largest possible number**.

### Example 1

```cpp
nums = [10, 2]
```

Possible combinations:
- 102
- 210

Answer: `"210"`

### Example 2

```cpp
nums = [3, 30, 34, 5, 9]
```

Answer: `"9534330"`

**Important:** Normal descending sorting does not always produce the largest possible number.

---

## 2. My Approach / Core Idea

Initially, I thought of sorting the numbers in descending order.

But this doesn't work!

Example:

```cpp
nums = [3, 30, 34]
```

Normal descending sorting:

```cpp
[34, 30, 3]
```

Result:

```cpp
34303
```

But the better arrangement is:

```cpp
[3, 34, 30]
```

Result:

```cpp
33430
```

Therefore, we need a different comparison technique.

### Key Observation: Compare by Concatenation

Instead of comparing individual numbers, compare the two possible combinations.

For two elements `a` and `b`:

1. Make `a + b`.
2. Make `b + a`.
3. Whichever combination produces the larger number should come first.

Example:

```cpp
a = "3";
b = "30";
```

Compare:

```cpp
a + b = "330"
b + a = "303"
```

Since `330 > 303`, put `3` before `30`.

### Main Condition

```cpp
a + b > b + a
```

This is the most important line of the entire problem.

---

## 3. Technique Used

**Greedy + Custom Sorting**

Why Greedy?

- We choose the better ordering between two numbers.
- We always prefer the arrangement that creates a larger concatenated result.
- Custom sorting applies this comparison repeatedly to arrange the entire array.

---

## 4. C++ Solution

```cpp
class Solution {
public:
    string largestNumber(vector<int>& nums) {

        vector<string> arr;

        // Step 1: Convert integers into strings
        for(int x : nums) {
            arr.push_back(to_string(x));
        }

        // Step 2: Custom sorting
        sort(arr.begin(), arr.end(), [](string a, string b) {
            return a + b > b + a;
        });

        // Step 3: Handle leading zeros
        if(arr[0] == "0") {
            return "0";
        }

        // Step 4: Join all strings
        string ans = "";

        for(string x : arr) {
            ans += x;
        }

        return ans;
    }
};
```

### C++ Code Explanation

**Step 1: Integer to String**

```cpp
vector<string> arr;

for(int x : nums) {
    arr.push_back(to_string(x));
}
```

Why?

We need to concatenate numbers to compare them.

`to_string()` converts an integer into a string.

Example:

```cpp
int x = 30;
string s = to_string(x);
```

Now:

```cpp
s = "30";
```

**Step 2: Custom Comparator**

```cpp
sort(arr.begin(), arr.end(), [](string a, string b) {
    return a + b > b + a;
});
```

- `sort()` sorts the array.
- `a` and `b` are two strings being compared.
- `a+b` means concatenate a followed by b.
- `b+a` means concatenate b followed by a.
- If `a+b` is greater, a comes first.

**Step 3: Handle Zeros**

```cpp
if(arr[0] == "0") {
    return "0";
}
```

Example:

```cpp
nums = [0, 0, 0]
```

After sorting:

```cpp
arr = ["0", "0", "0"]
```

Joining them would produce `"000"`.

But the correct answer is `"0"`.

Since the array is sorted in descending concatenation order, if the first element is `"0"`, all elements must be zero.

**Step 4: Join Strings**

```cpp
string ans = "";

for(string x : arr) {
    ans += x;
}
```

- `ans` starts as an empty string.
- The loop takes every string from `arr`.
- `+=` adds each string to the end of `ans`.

Example:

```cpp
arr = ["9", "5", "34", "3", "30"]
```

Result:

```cpp
ans = "9534330";
```

---

## 5. Java Solution

```java
class Solution {
    public String largestNumber(int[] nums) {

        // Step 1: Convert integers into strings
        String[] arr = new String[nums.length];

        for(int i = 0; i < nums.length; i++) {
            arr[i] = String.valueOf(nums[i]);
        }

        // Step 2: Custom sorting
        Arrays.sort(arr, (a, b) -> {
            return (b + a).compareTo(a + b);
        });

        // Step 3: Handle leading zeros
        if(arr[0].equals("0")) {
            return "0";
        }

        // Step 4: Join all strings
        StringBuilder ans = new StringBuilder();

        for(String x : arr) {
            ans.append(x);
        }

        return ans.toString();
    }
}
```

### Java Code Explanation

**Step 1: Integer to String**

```java
arr[i] = String.valueOf(nums[i]);
```

`String.valueOf()` converts an integer into a string.

Example:

```java
int x = 30;
String s = String.valueOf(x);
```

Now:

```java
s = "30";
```

**Step 2: Custom Comparator**

```java
Arrays.sort(arr, (a, b) -> {
    return (b + a).compareTo(a + b);
});
```

This may look different from C++, but it does the same thing.

- `a+b` creates one combination.
- `b+a` creates the other combination.
- `compareTo()` compares strings lexicographically.
- We reverse the comparison using `(b+a).compareTo(a+b)` so that the larger concatenation comes first.

For example:

```java
a = "3";
b = "30";
```

```java
b + a = "303";
a + b = "330";
```

`"303".compareTo("330")` returns a negative value, so `a` comes before `b`.

**Step 3: Handle Zeros**

```java
if(arr[0].equals("0")) {
    return "0";
}
```

In Java, use `.equals()` to compare string contents.

**Step 4: Join Strings**

```java
StringBuilder ans = new StringBuilder();

for(String x : arr) {
    ans.append(x);
}

return ans.toString();
```

- `StringBuilder` efficiently builds a string.
- `append()` adds each string to the result.
- `toString()` converts the StringBuilder into a String.

---

## 6. VERY IMPORTANT: When to Use to_string() / String.valueOf()?

I often get confused about which conversion to use and when.

Remember this table:

| Language | Conversion | Meaning |
|---|---|---|
| C++ | `to_string(123)` | Integer to String |
| C++ | `stoi("123")` | String to Integer |
| C++ | `stoll("12345678901")` | String to Long Long |
| C++ | `to_string(12.5)` | Double to String |
| Java | `String.valueOf(123)` | Integer to String |
| Java | `Integer.parseInt("123")` | String to int |
| Java | `Long.parseLong("12345678901")` | String to long |
| Java | `Double.parseDouble("12.5")` | String to double |

### Easy Memory Trick

**C++**

- `to_string()` = Convert TO string.
- `stoi()` = String TO integer.
- `stoll()` = String TO long long.

**Java**

- `String.valueOf()` = Convert to String.
- `Integer.parseInt()` = String to int.
- `Long.parseLong()` = String to long.
- `Double.parseDouble()` = String to double.

### When Do We Convert Integer to String?

Use conversion when:
- We need to concatenate numbers.
- We need to compare numbers based on their digits.
- We need to manipulate individual characters or digits.
- We need to build a number as a string to avoid integer overflow.

### When Do We Convert String to Integer?

Use conversion when:
- We receive a numeric value as a string.
- We need to perform arithmetic operations.
- We need to store the value in an integer variable.

**Important:** In this problem, we return a String, not an integer, because the resulting number can be larger than the range of `int` or `long long`.

---

## 7. Important C++ and Java String Operations

| Operation | C++ | Java |
|---|---|---|
| Integer to String | `to_string(x)` | `String.valueOf(x)` |
| String to Integer | `stoi(s)` | `Integer.parseInt(s)` |
| Concatenate | `a + b` | `a + b` |
| Add to end | `ans += x` | `ans += x` |
| String length | `s.length()` | `s.length()` |
| First character | `s[0]` | `s.charAt(0)` |
| Compare contents | `a == b` | `a.equals(b)` |
| Empty string | `string ans = "";` | `String ans = "";` |
| Efficient building | `string += x` | `StringBuilder.append(x)` |

**Important Java Note:**

Never use `==` to compare String contents.

Use:

```java
a.equals(b)
```

---

## 8. Complexity

Let:
- `n` = number of elements.
- `k` = maximum number of digits in an element.

**Time Complexity:** O(n log n × k)

Sorting requires O(n log n) comparisons, and each concatenation comparison takes O(k) time.

**Space Complexity:** O(n × k) for storing the strings, excluding sorting implementation overhead.

---

## 9. Related LeetCode Questions

### Custom Sorting + Comparator

1. [Sort Array by Increasing Frequency — 1636](https://leetcode.com/problems/sort-array-by-increasing-frequency/)
2. [Largest Number — 179](https://leetcode.com/problems/largest-number/)
3. [Relative Sort Array — 1122](https://leetcode.com/problems/relative-sort-array/)
4. [Sort Characters By Frequency — 451](https://leetcode.com/problems/sort-characters-by-frequency/)

### Greedy + Sorting

5. [Largest Perimeter Triangle — 976](https://leetcode.com/problems/largest-perimeter-triangle/)
6. [Queue Reconstruction by Height — 406](https://leetcode.com/problems/queue-reconstruction-by-height/)
7. [Non-overlapping Intervals — 435](https://leetcode.com/problems/non-overlapping-intervals/)
8. [Minimum Number of Arrows to Burst Balloons — 452](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/)

### String Manipulation + Conversion

9. [Add Strings — 415](https://leetcode.com/problems/add-strings/)
10. [Multiply Strings — 43](https://leetcode.com/problems/multiply-strings/)
11. [String to Integer (atoi) — 8](https://leetcode.com/problems/string-to-integer-atoi/)
12. [Compare Version Numbers — 165](https://leetcode.com/problems/compare-version-numbers/)

---

## 10. Final Revision Notes

**Problem:** LeetCode 179 — Largest Number

**Technique:** Greedy + Custom Sorting

**Key Observation:** Compare `a+b` with `b+a`, not individual numbers.

**C++ Conversion:** `to_string(x)`

**Java Conversion:** `String.valueOf(x)`

**Custom Comparator:**

C++:
```cpp
return a + b > b + a;
```

Java:
```java
return (b + a).compareTo(a + b);
```

**Zero Handling:**

C++:
```cpp
if(arr[0] == "0") return "0";
```

Java:
```java
if(arr[0].equals("0")) return "0";
```

**Joining:**
- C++: `ans += x`
- Java: `ans.append(x)`

**One-line Memory Trick:**

Don't compare individual numbers. Compare which concatenation produces the bigger number, sort accordingly, and join all strings.
