# LeetCode 316 – Remove Duplicate Letters

**Topic:** Greedy + Monotonic Stack + Hashing  
**Difficulty:** Medium  
**Pattern:** Lexicographically Smallest Subsequence

---

## 1. Problem Statement

Given a string `s`, remove duplicate letters so that every letter appears exactly once.

The resulting string must be the **smallest in lexicographical order** among all possible results.

### Example 1

Input:
```cpp
s = "bcabc"
```

Output:
```cpp
"abc"
```

Explanation:
- Every character should appear exactly once.
- We need the lexicographically smallest possible string.
- Therefore, the answer is `"abc"`.

### Example 2

Input:
```cpp
s = "cbacdcbc"
```

Output:
```cpp
"acdb"
```

Explanation:
- Remove duplicate characters.
- Maintain the smallest possible lexicographical order.
- Every distinct character must be present.

---

## 2. My Initial Approach (Important)

Mera initial thought tha:

1. Ek `unordered_map` ya `unordered_set` use karenge.
2. First iteration mein store karenge ki kaunsa character kis-kis index par aa raha hai.
3. Second iteration mein string traverse karenge.
4. Agar current character chhota hai aur pehle se answer mein koi bada character present hai, toh check karenge ki woh bada character future mein dobara aa raha hai ya nahi.
5. Agar dobara aa raha hai, toh usko remove kar denge aur current smaller character insert kar denge.

### Improvement in my approach

Humein saare positions store karne ki zarurat nahi hai.

Sirf har character ka **last occurrence** store karna sufficient hai.

Why?

Humein bas ye check karna hai:

"Kya jis character ko main remove kar raha hoon, woh future mein dobara aayega?"

Agar haan, toh safely remove kar sakte hain.

---

## 3. Main Intuition / Key Observation

We need three things:

| Data Structure | Purpose |
|---|---|
| `unordered_map` | Store last occurrence of each character |
| `unordered_set` | Track characters already present in answer |
| `string ans` | Acts like a stack |

### Three important conditions

```cpp
while(!ans.empty() &&
      ans.back() > ch &&
      last[ans.back()] > i)
```

Let's understand each condition.

**Condition 1:**

```cpp
!ans.empty()
```

Stack empty nahi hona chahiye, warna compare karne ke liye koi character nahi hoga.

**Condition 2:**

```cpp
ans.back() > ch
```

Current character, answer ke last character se lexicographically smaller hona chahiye.

Example:

```cpp
'b' > 'a'
```

Toh agar current character `'a'` hai aur answer ka last character `'b'` hai, toh `'b'` ko remove karne ka option consider karenge.

**Condition 3:**

```cpp
last[ans.back()] > i
```

Jo character remove kar rahe hain, woh future mein dobara aana chahiye.

Agar dobara nahi aayega toh remove nahi kar sakte, warna woh character final answer se completely missing ho jayega.

### Final greedy rule

Agar current character chhota hai AND previous bigger character future mein dobara available hai, toh bigger character ko remove kar do.

---

## 4. Dry Run

Input:

```cpp
s = "bcabc"
```

### Step 1: Store last occurrence

```cpp
b -> 3
c -> 4
a -> 2
```

### Step 2: Traverse the string

Initially:

```cpp
ans = ""
seen = {}
```

| Index | Current | Operation | Answer |
|---|---|---|---|
| 0 | b | Insert b | b |
| 1 | c | Insert c | bc |
| 2 | a | Remove c, remove b, insert a | a |
| 3 | b | Insert b | ab |
| 4 | c | Insert c | abc |

### Understand index 2 carefully

Current character = `'a'`

Answer = `"bc"`

Now compare:

1. `'c' > 'a'` → TRUE
2. Last occurrence of c = 4, current index = 2 → TRUE

Therefore remove c.

Now:

1. `'b' > 'a'` → TRUE
2. Last occurrence of b = 3, current index = 2 → TRUE

Therefore remove b.

Now insert a.

Final answer:

```cpp
"abc"
```

---

## 5. C++ Solution

```cpp
class Solution {
public:
    string removeDuplicateLetters(string s) {

        // Step 1: Store last occurrence
        unordered_map<char, int> last;

        for(int i = 0; i < s.size(); i++) {
            last[s[i]] = i;
        }

        // Step 2: Maintain answer and visited characters
        string ans = "";
        unordered_set<char> seen;

        // Step 3: Traverse string
        for(int i = 0; i < s.size(); i++) {

            char ch = s[i];

            // Already present, skip
            if(seen.count(ch)) {
                continue;
            }

            // Remove bigger characters if they occur again
            while(!ans.empty() &&
                  ans.back() > ch &&
                  last[ans.back()] > i) {

                seen.erase(ans.back());
                ans.pop_back();
            }

            // Insert current character
            ans.push_back(ch);
            seen.insert(ch);
        }

        return ans;
    }
};
```

### Important C++ Functions

| Function | Meaning |
|---|---|
| `seen.count(ch)` | Checks whether character exists |
| `seen.insert(ch)` | Inserts character |
| `seen.erase(ch)` | Removes character from set |
| `ans.back()` | Returns last character |
| `ans.pop_back()` | Removes last character |
| `ans.push_back(ch)` | Inserts character at end |
| `last[s[i]] = i` | Updates last occurrence |

---

## 6. Java Solution

```java
import java.util.*;

class Solution {
    public String removeDuplicateLetters(String s) {

        // Step 1: Store last occurrence
        HashMap<Character, Integer> last = new HashMap<>();

        for(int i = 0; i < s.length(); i++) {
            last.put(s.charAt(i), i);
        }

        // Step 2: Maintain answer and visited characters
        StringBuilder ans = new StringBuilder();
        HashSet<Character> seen = new HashSet<>();

        // Step 3: Traverse string
        for(int i = 0; i < s.length(); i++) {

            char ch = s.charAt(i);

            // Already present, skip
            if(seen.contains(ch)) {
                continue;
            }

            // Remove bigger characters if they occur again
            while(ans.length() > 0 &&
                  ans.charAt(ans.length() - 1) > ch &&
                  last.get(ans.charAt(ans.length() - 1)) > i) {

                char removed = ans.charAt(ans.length() - 1);

                seen.remove(removed);
                ans.deleteCharAt(ans.length() - 1);
            }

            // Insert current character
            ans.append(ch);
            seen.add(ch);
        }

        return ans.toString();
    }
}
```

### Important Java Functions

| Function | Meaning |
|---|---|
| `seen.contains(ch)` | Checks if character exists |
| `seen.add(ch)` | Inserts character |
| `seen.remove(ch)` | Removes character |
| `ans.charAt(ans.length()-1)` | Returns last character |
| `ans.deleteCharAt()` | Removes character |
| `ans.append(ch)` | Adds character |
| `last.put(ch, i)` | Stores last occurrence |

---

## 7. Why Do We Need seen.erase()?

This is a VERY IMPORTANT concept.

Suppose:

```cpp
ans = "bc"
seen = {'b', 'c'}
```

Now current character is `'a'`.

We remove `'c'` and `'b'` from the answer.

```cpp
ans.pop_back();
```

But what if we don't remove these characters from `seen`?

Then:

```cpp
seen = {'b', 'c'}
```

Even though b and c are no longer in our answer, the set still thinks they are present.

When b appears again, this condition:

```cpp
if(seen.count(ch))
```

will incorrectly skip b.

Therefore:

```cpp
seen.erase(ans.back());
ans.pop_back();
```

Both operations are necessary.

---

## 8. Why Is This Called Monotonic Stack?

A monotonic stack maintains elements in a particular order.

Here, whenever we encounter a smaller character, we try to remove larger characters from the end if they can safely appear again.

Example:

```cpp
ans = "bc"
current = 'a'
```

We remove c and b.

This helps us maintain the lexicographically smallest possible subsequence.

Remember:

- Stack is used to maintain the answer.
- Greedy decides when to remove an element.
- Last occurrence ensures we never lose a unique character.
- Set prevents duplicates.

---

## 9. Common Mistakes

### Mistake 1: Removing bigger characters without checking future availability

Wrong:

```cpp
while(!ans.empty() && ans.back() > ch) {
    ans.pop_back();
}
```

Problem:

A character might never appear again.

Correct:

```cpp
last[ans.back()] > i
```

### Mistake 2: Not updating seen

Wrong:

```cpp
ans.pop_back();
```

Correct:

```cpp
seen.erase(ans.back());
ans.pop_back();
```

### Mistake 3: Not checking duplicates

Always check:

```cpp
if(seen.count(ch)) {
    continue;
}
```

### Mistake 4: Using a normal set to sort all characters

We cannot simply sort unique characters alphabetically.

Why?

Because we must maintain the original relative order of the selected characters.

This is a subsequence problem, not a simple sorting problem.

---

## 10. Complexity Analysis

Let n = length of string.

| Operation | Complexity |
|---|---|
| Store last occurrence | O(n) |
| Traverse string | O(n) |
| Insert each character | O(1) average |
| Remove each character | O(1) average |

**Time Complexity: O(n)**

Each character is pushed at most once and popped at most once.

**Auxiliary Space: O(1)**

Because the problem contains only lowercase English letters (maximum 26 distinct characters).

---

# 11. Similar LeetCode Questions to Practise

These questions are important because they use similar greedy, monotonic stack, or subsequence concepts.

## Level 1 – Must Do

### 1. LeetCode 1081 – Smallest Subsequence of Distinct Characters

- Almost exactly the same problem as LC 316.
- Same greedy + monotonic stack approach.
- Practise implementing without looking at notes.

### 2. LeetCode 1047 – Remove All Adjacent Duplicates in String

- Stack-based duplicate removal.
- Understand how to push and pop characters.

### 3. LeetCode 402 – Remove K Digits

- Monotonic stack.
- Remove larger digits whenever a smaller digit can improve the answer.
- Very similar greedy observation.

## Level 2 – Important

### 4. LeetCode 1673 – Find the Most Competitive Subsequence

- Monotonic stack.
- Select the smallest possible subsequence of fixed length.
- Understand when to pop elements.

### 5. LeetCode 2030 – Smallest K-Length Subsequence With Occurrences of a Letter

- Very closely related to LC 316.
- Greedy + monotonic stack.
- Maintain a required number of a particular character.

### 6. LeetCode  monotonic stack practice –  monotonic stack pattern

Focus on understanding:
- When to pop.
- When to push.
- When an element can safely be removed.
- How to preserve required elements.

## Level 3 – Advanced

### 7. LeetCode 321 – Create Maximum Number

- Greedy subsequence selection.
- Monotonic stack.
- Combining two subsequences.

### 8. LeetCode 84 – Largest Rectangle in Histogram

- Classic monotonic stack.
- Different application, but excellent for understanding stack behaviour.

### 9. LeetCode 85 – Maximal Rectangle

- Uses the histogram + monotonic stack concept.
- Good advanced practice.

---

# 12. Final Revision Notes (Before Interview)

Remember these 5 points:

1. **Last occurrence:** Store the last index of every character.
2. **Visited set:** Avoid inserting duplicate characters.
3. **Lexicographical comparison:** If `ans.back() > ch`, consider removing the bigger character.
4. **Future availability:** Remove only if `last[ans.back()] > i`.
5. **Update both:** Whenever you pop from answer, remove that character from `seen` too.

### One-line intuition

"Current character agar answer ke last character se chhota hai aur bada character future mein dobara aa sakta hai, toh usko remove kar do taaki lexicographically smallest answer ban sake."

### Pattern to remember

**Greedy + Monotonic Stack + Last Occurrence + Visited Set**

---

## My Practice Checklist

- [ ] Can I explain why last occurrence is enough?
- [ ] Can I explain all three while-loop conditions?
- [ ] Can I explain why seen.erase() is necessary?
- [ ] Can I dry run `bcabc` without help?
- [ ] Can I write the C++ code without looking?
- [ ] Can I write the Java code without looking?
- [ ] Can I solve LC 1081 independently?
- [ ] Can I solve LC 402 independently?
- [ ] Can I solve LC 1673 independently?

**Goal:** Don't just memorise the code. Understand WHEN and WHY we pop an element from the stack.