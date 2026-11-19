# How to Add a New Problem

Paste a message in **exactly** this format and the agent will handle everything else:

```
QUESTION: <paste the full problem statement here>
SOLUTION: <paste your solution code here>
LEARNED: <write what you learned, any key insight, trick, or mistake — rough notes are fine>
```

## What the agent does automatically

1. **Detects** topic, difficulty, and pattern (e.g. two pointers, sliding window, prefix sum, DFS, DP).  
   It will ask you only if something is genuinely ambiguous.
2. **Creates** the next numbered folder inside the right topic directory  
   e.g. `arrays/001-two-sum/`
3. **Writes** `README.md` using the standard template, polishing your "learned" notes into clear sentences without changing their meaning.
4. **Saves** your solution code as-is in `solution.<ext>` (adds comments, but does **not** rewrite your logic).
5. **Adds** complexity analysis (Time & Space) and edge cases.
6. **Updates** the main `README.md` — problem table row + Patterns Index entry.
7. **Commits** with a message like `Add: 001 Two Sum (arrays, hash map)`.

## Optional fields you can add

If you want to override the agent's auto-detection, you can include:

```
QUESTION: ...
SOLUTION: ...
LEARNED: ...
TOPIC: arrays
DIFFICULTY: Easy
PATTERN: Hash Map
LANGUAGE: python
LINK: https://leetcode.com/problems/two-sum/
```

## Topic → folder mapping

| Topic keywords          | Folder                    |
|-------------------------|---------------------------|
| array, list             | arrays/                   |
| string                  | strings/                  |
| linked list             | linked-list/              |
| stack, queue, deque     | stack-queue/              |
| tree, BST, trie         | trees/                    |
| graph, BFS, DFS         | graphs/                   |
| dynamic programming, dp | dp/                       |
| recursion, backtracking | recursion-backtracking/   |
| binary search           | binary-search/            |
| sliding window          | sliding-window/           |
| two pointers            | two-pointers/             |
| greedy                  | greedy/                   |
| heap, priority queue    | heap/                     |
| math, bit manipulation  | math/                     |
