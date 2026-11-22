# LeetCode 420 – Strong Password Checker (Hard)
## Problem
Make the given password strong using minimum insertions, deletions and replacements.
A strong password must:
- Have length between 6 and 20.
- Contain at least one lowercase, uppercase and digit.
- Not have 3 consecutive identical characters.
## Core Idea
There are 3 things to calculate:
1. `missing` = How many character types are missing (lowercase, uppercase, digit).
2. `replace` = How many replacements are needed to fix consecutive repeating characters.
3. `over` = How many characters must be deleted because length exceeds 20.
The approach depends on password length.
## 1. If length < 6
```cpp
return max(missing, 6 - n);
```
We need insertions to make length at least 6 and to add missing character types. One insertion can solve both problems, so take `max`, not sum.
## 2. If length is between 6 and 20
```cpp
return max(missing, replace);
```
No insertion or deletion is needed. We only fix missing character types and repeating groups. One replacement can fix both, so take maximum.
## 3. If length > 20
This is the tricky greedy part.
First, mandatory deletions:
```cpp
int over = n - 20;
int deletionsLeft = over;
```
We must delete `n - 20` characters. But delete strategically from repeating groups to reduce replacements.
### How to calculate replacements?
For every consecutive repeating group of length `L`:
```cpp
replace += L / 3;
```
Example: `aaaaaa` has length 6, so it needs 2 replacements.
### Greedy deletion priority
We should use deletions where they save the most replacements first.
| Repeating group | Deletions needed to save 1 replacement |
|---|---|
| `L % 3 == 0` | 1 deletion |
| `L % 3 == 1` | 2 deletions |
| `L % 3 == 2` | 3 deletions |
**Priority 1: L % 3 == 0**
One deletion saves one replacement.
Example: `aaaaaa` -> `aaaaa`. Replacements: 2 -> 1.
**Priority 2: L % 3 == 1**
Two deletions save one replacement.
Example: `aaaaaaa` -> `aaaaa`. Replacements: 2 -> 1.
**Priority 3: Remaining groups**
Three deletions save one replacement.
Example: `aaaaaaaa` -> `aaaaa`. Replacements: 2 -> 1.
After using deletions strategically:
```cpp
return over + max(missing, replace);
```
`over` counts all mandatory deletions. `max(missing, replace)` counts the remaining operations. One replacement can fix a missing character type and a repeating group simultaneously.
## What Each Important Variable Does
- `n`: Password length.
- `missing`: Number of missing character types.
- `runs`: Stores lengths of consecutive repeating groups (only length >= 3).
- `count`: Tracks the current repeating group's length.
- `replace`: Total replacements initially needed.
- `over`: Mandatory deletions (`n - 20`).
- `deletionsLeft`: Deletions still available to reduce replacements.
- `L`: Length of one repeating group.
- `use`: Number of replacements we can save using remaining deletions.
## Important Code Blocks
1. **Character type checking:** Find missing lowercase, uppercase and digit.
2. **Run detection:** Traverse the string and store lengths of consecutive identical character groups in `runs`.
3. **Replacement calculation:** Add `L / 3` for every repeating group.
4. **Short password:** Return `max(missing, 6 - n)`.
5. **Valid-length password:** Return `max(missing, replace)`.
6. **Long password:** Delete extra characters using the greedy priority: remainder 0, then remainder 1, then remaining groups.
7. **Final answer:** Mandatory deletions + maximum of remaining missing types and replacements.
## Key Observations / Things to Remember
1. Integer division `L / 3` gives replacements required for a repeating group.
2. We use `max()` because one insertion/replacement can satisfy multiple requirements.
3. When length > 20, deletion is not random. We prioritize groups where fewer deletions save one replacement.
4. `int& L` means we modify the actual value inside the `runs` vector, not a copy.
5. We don't actually modify the password string. We only update run lengths to calculate the minimum operations.
6. `over` remains unchanged because all mandatory deletions count in the final answer, even if they also reduce replacements.
## Similar Questions to Practice
1. [LeetCode 443 – String Compression](https://leetcode.com/problems/string-compression/) – String traversal and consecutive groups.
2. [LeetCode 316 – Remove Duplicate Letters](https://leetcode.com/problems/remove-duplicate-letters/) – Greedy + stack.
3. [LeetCode 402 – Remove K Digits](https://leetcode.com/problems/remove-k-digits/) – Greedy deletion.
4. [LeetCode 767 – Reorganize String](https://leetcode.com/problems/reorganize-string/) – Avoid consecutive identical characters.
5. [LeetCode 621 – Task Scheduler](https://leetcode.com/problems/task-scheduler/) – Greedy + frequency.
6. [LeetCode 1647 – Minimum Deletions to Make Character Frequencies Unique](https://leetcode.com/problems/minimum-deletions-to-make-character-frequencies-unique/) – Greedy deletion.
**Final takeaway:** First check password length. If short, insert. If length is valid, replace. If too long, delete extra characters strategically to reduce replacements, then fix whatever requirements remain.