
# LeetCode 3331 - Find Subtree Sizes After Changes

## Problem Statement

You are given a rooted tree with `n` nodes numbered from `0` to `n-1`.

- `parent[i]` represents the parent of node `i`.
- `s[i]` represents the character assigned to node `i`.

For every node `x` (except root):

1. Find the nearest ancestor `y` of `x` having the same character.
2. If such an ancestor exists, change the parent of `x` to `y`.
3. If no such ancestor exists, keep the original parent.

Finally, return the subtree size of every node after these changes.

---

## 1. Main Observation

Humein har node ke liye uska **nearest ancestor having the same character** find karna hai.

Example:

```text
          0(a)
         /    \
      1(b)    2(a)
      /
    3(a)
   /
 4(b)
```

Node 3 ka character `a` hai.

Iske ancestors:
- Node 1 -> b
- Node 0 -> a

Nearest ancestor having character `a` is node 0.

So, parent of node 3 changes from 1 to 0.

### Important Observation

Humein har node ke liye baar-baar ancestors traverse nahi karne.

Instead, DFS ke through track kar sakte hain ki current path mein kisi character ka nearest ancestor kaun hai.

Iske liye use karenge:

```cpp
vector<int> last(26, -1);
```

---

## 2. Understanding `last[26]`

Since string contains only lowercase English characters, we need an array of size 26.

`last[c]` stores:

**The nearest ancestor (including the current node after updating) having character c on the current DFS path.**

Initially:

```cpp
last.assign(26, -1);
```

- `-1` means no ancestor with that character exists.
- `0` to `25` represent the node indices.

For example:

```text
s = "aba"
```

Suppose DFS is currently at node 2.

Current path:

```text
0(a) -> 1(b) -> 2(a)
```

Then:

```cpp
last['a' - 'a'] = 2;
last['b' - 'a'] = 1;
```

But before updating `last`, we save its previous value.

Why?

Because after completing the current node's subtree, we need to restore the previous ancestor.

---

## 3. DFS Approach

### Step 1: Build the adjacency list

Given parent array:

```cpp
parent = [-1, 0, 0, 1, 1, 1]
```

We construct:

```text
       0
      / \
     1   2
   / | \
  3  4  5
```

Code:

```cpp
for(int i = 1; i < n; i++) {
    int u = parent[i];
    int v = i;

    adj[u].push_back(v);
}
```

Here:

- `u` = parent
- `v` = child

So we create an edge `u -> v`.

### Step 2: Initialize subtree sizes

```cpp
resultSize.assign(n, 1);
```

Initially, every node contributes at least 1 to its own subtree.

For example:

```text
0
|
1
|
2
```

Initially:

```text
resultSize = [1, 1, 1]
```

Later, children will contribute their subtree sizes to their new parents.

### Step 3: DFS and find the nearest ancestor

```cpp
int c = s[currNode] - 'a';

int nearestAncestor = last[c];
```

First, convert the character into an index.

For example:

```cpp
'a' - 'a' = 0
'b' - 'a' = 1
'c' - 'a' = 2
```

`nearestAncestor` stores the closest previous node on the current DFS path having the same character.

### Step 4: Update the last array

```cpp
last[c] = currNode;
```

Now the current node becomes the nearest node having character `c`.

This is important because its descendants might have the same character.

### Step 5: Visit all children

```cpp
for(int child : adj[currNode]) {
    dfs(child, s, parent);
}
```

We explore the entire subtree.

### Step 6: Backtracking (VERY IMPORTANT)

```cpp
last[c] = nearestAncestor;
```

Why are we doing this?

Suppose:

```text
       0(a)
       /
     1(b)
    /   \
  2(a)  3(c)
```

While visiting node 2:

```cpp
last['a'] = 2;
```

After completing node 2, we must restore:

```cpp
last['a'] = 0;
```

Otherwise, node 3 or another branch might incorrectly consider node 2 as its ancestor.

**Remember:**

DFS explores one branch at a time.

So whatever changes we make to the path information must be restored when returning from that branch.

This is called BACKTRACKING.

---

## 4. Changing the Parent and Calculating Subtree Size

This is the most important part.

```cpp
if(currNode != 0) {
    int newParent = parent[currNode];

    if(nearestAncestor != -1) {
        newParent = nearestAncestor;
    }

    parent[currNode] = newParent;

    resultSize[newParent] += resultSize[currNode];
}
```

Let's understand it.

### First:

```cpp
int newParent = parent[currNode];
```

Initially, assume the parent does not change.

### Then:

```cpp
if(nearestAncestor != -1) {
    newParent = nearestAncestor;
}
```

If we found an ancestor having the same character, make it the new parent.

Otherwise, retain the original parent.

### Finally:

```cpp
parent[currNode] = newParent;
```

Update the parent.

And:

```cpp
resultSize[newParent] += resultSize[currNode];
```

Add the current node's complete subtree size to its new parent.

**Why are we adding here?**

Because DFS processes children before their parent finishes.

By the time we reach this statement for a node, all its descendants have already contributed to its subtree size.

So its complete subtree size is ready to be added to its new parent.

This is POSTORDER DFS.

---

## 5. Complete C++ Code

```cpp
class Solution {
public:
    unordered_map<int, vector<int>> adj;
    vector<int> resultSize;
    vector<int> last;

    void dfs(int currNode, string& s, vector<int>& parent) {

        int c = s[currNode] - 'a';

        int nearestAncestor = last[c];

        last[c] = currNode;

        for(int child : adj[currNode]) {
            dfs(child, s, parent);
        }

        // Backtracking: restore previous ancestor
        last[c] = nearestAncestor;

        // Root has no parent
        if(currNode != 0) {

            int newParent = parent[currNode];

            if(nearestAncestor != -1) {
                newParent = nearestAncestor;
            }

            parent[currNode] = newParent;

            // Add current subtree size to new parent
            resultSize[newParent] += resultSize[currNode];
        }
    }

    vector<int> findSubtreeSizes(vector<int>& parent, string s) {

        int n = parent.size();

        // Build adjacency list
        for(int i = 1; i < n; i++) {
            int u = parent[i];
            int v = i;

            adj[u].push_back(v);
        }

        // Every node initially has subtree size 1
        resultSize.assign(n, 1);

        // Tracks nearest node for every character
        last.assign(26, -1);

        dfs(0, s, parent);

        return resultSize;
    }
};
```

---

## 6. Dry Run - Example 1

Input:

```cpp
parent = [-1, 0, 0, 1, 1, 1]
s = "abaabc"
```

Original tree:

```text
          0(a)
         /    \
       1(b)   2(a)
      / | \
   3(a) 4(b) 5(c)
```

### Node 0

Character = `a`

```cpp
nearestAncestor = last[0] = -1;
last[0] = 0;
```

No parent because this is root.

### Node 1

Character = `b`

```cpp
nearestAncestor = last[1] = -1;
last[1] = 1;
```

No matching ancestor.

Parent remains 0.

### Node 3

Character = `a`

```cpp
nearestAncestor = last[0] = 0;
last[0] = 3;
```

Matching ancestor found: node 0.

So:

```cpp
parent[3] = 0;
```

Node 3 contributes 1 to node 0.

### Node 4

Character = `b`

```cpp
nearestAncestor = last[1] = 1;
```

Matching ancestor found: node 1.

Parent remains 1.

Node 4 contributes 1 to node 1.

### Node 5

Character = `c`

```cpp
nearestAncestor = last[2] = -1;
```

No matching ancestor.

Parent remains 1.

Node 5 contributes 1 to node 1.

### Node 1 finishes

Its subtree size:

```text
1 (itself) + 1 (node 4) + 1 (node 5) = 3
```

Node 1 contributes 3 to node 0.

### Node 2

Character = `a`

Nearest matching ancestor = node 0.

Its parent already is 0.

Node 2 contributes 1 to node 0.

### Final Answer

```cpp
[6, 3, 1, 1, 1, 1]
```

---

## 7. Why Does This Work for Simultaneous Changes?

This is an important conceptual point.

Question says all parent changes happen simultaneously.

But our code processes nodes one by one.

Why is this correct?

Because we build the adjacency list using the ORIGINAL parent array.

We never modify the adjacency list during DFS.

Therefore, DFS always traverses the original tree and finds ancestors using the original root-to-node path.

We only calculate the new parent separately.

After processing a node, we add its subtree contribution to its new parent.

Since every node's new parent is determined using the original tree, processing order does not change the result.

**Important:** We must not rebuild or traverse the modified tree during this DFS.

---

## 8. Complexity Analysis

Let `n` be the number of nodes.

| Operation | Complexity |
|---|---|
| Building adjacency list | O(n) |
| DFS traversal | O(n) |
| Accessing `last[c]` | O(1) |
| Subtree size calculation | O(n) |

**Time Complexity: O(n)**

Every node is visited exactly once.

**Space Complexity: O(n)**

- Adjacency list: O(n)
- Result array: O(n)
- Recursion stack: O(n) in worst case
- Last array: O(26) = O(1)

---

## 9. Key Takeaways / Important Observations

1. When a question asks for the nearest ancestor having a particular property, think about maintaining information along the DFS path.

2. `last[26]` avoids repeatedly traversing ancestors.

3. Always save the previous value before updating path information.

4. Restore the previous value after DFS using backtracking.

5. Subtree sizes are calculated using POSTORDER DFS (children first, parent later).

6. When a problem says simultaneous changes, check whether you can calculate all changes using the original tree.

7. The most important line:

```cpp
int nearestAncestor = last[c];
```

This finds the nearest ancestor with the same character in O(1).

8. The most important backtracking line:

```cpp
last[c] = nearestAncestor;
```

This prevents information from one DFS branch from affecting another branch.

---

## 10. Similar Questions to Practice

### DFS + Subtree Sizes

1. LeetCode 1519 - Number of Nodes in the Sub-Tree With the Same Label
2. LeetCode 865 - Smallest Subtree with all the Deepest Nodes
3. LeetCode  subtree-related DFS problems

### DFS + Ancestor Tracking

4. LeetCode 236 - Lowest Common Ancestor of a Binary Tree
5. LeetCode 1026 - Maximum Difference Between Node and Ancestor
6. LeetCode 1372 - Longest ZigZag Path in a Binary Tree

### DFS + Backtracking

7. LeetCode 1443 - Minimum Time to Collect All Apples in a Tree
8. LeetCode 199 - Binary Tree Right Side View

---

## Final Revision

Remember this pattern:

```cpp
// 1. Save previous information
int previous = last[c];

// 2. Update current path information
last[c] = currNode;

// 3. Explore children
for(int child : adj[currNode]) {
    dfs(child, s, parent);
}

// 4. Restore previous information
last[c] = previous;

// 5. Process current node after children
```

**Core concept: DFS + Path Tracking + Backtracking + Postorder Subtree Calculation.**
