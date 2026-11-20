# Monocarp and k-Balanced Subarrays

## 1. Problem Statement

We are given an array `a` of `n` positive integers and an integer `k`.

A subarray from index `l` to `r` is called **k-balanced** if:

1. Its sum is divisible by `k`.
2. Its length is strictly greater than `k`.

We need to count the total number of such subarrays.

---

## 2. Connection with LeetCode 974

This problem is very similar to **LeetCode 974 – Subarray Sums Divisible by K**.

In LC 974, we use:

- Prefix sums.
- Modulo `k`.
- Frequency array to count equal remainders.

We know that:

\[
sum(l...r)=prefix[r]-prefix[l-1]
\]

For the sum to be divisible by `k`:

\[
prefix[r]\%k=prefix[l-1]\%k
\]

So, we count pairs of prefix sums having equal remainders.

**But this problem has one additional condition:**

\[
r-l+1>k
\]

This means we cannot consider every previous prefix index. We can only consider indices sufficiently far behind the current ending index.

---

## 3. Convert the Length Condition

Let:

\[
j=l-1
\]

Then:

\[
length=r-j
\]

Since the length must be greater than `k`:

\[
r-j>k
\]

Rearranging:

\[
\boxed{j\leq r-k-1}
\]

This means:

For every ending index `r`, we can only consider prefix indices from `0` to `r-k-1`.

This is the main modification from LC 974.

---

## 4. Why Does the Loop Start From k+1?

We know:

\[
j\leq r-k-1
\]

The smallest possible prefix index is `j = 0`.

For `j = 0` to become valid:

\[
0\leq r-k-1
\]

Therefore:

\[
r\geq k+1
\]

Hence, we start our loop from:

```cpp
for(int r = k+1; r <= n; r++)
```

There cannot be any valid subarray ending before `k+1`, because its length cannot be greater than `k`.

---

## 5. Understanding the Main Logic

```cpp
for(int r = k+1; r <= n; r++) {

    cnt[p[r-k-1]]++;

    ans += cnt[p[r]];
}
```

### First line: `cnt[p[r-k-1]]++`

We know that the eligible prefix indices are:

\[
0\leq j\leq r-k-1
\]

As `r` increases, this eligible range grows by exactly one index.

For example, take `k = 2`.

| r | Eligible prefix indices |
|---|---|
| 3 | 0 |
| 4 | 0, 1 |
| 5 | 0, 1, 2 |
| 6 | 0, 1, 2, 3 |

Notice that every iteration introduces exactly one new eligible index.

That index is:

\[
j=r-k-1
\]

Therefore, we insert its remainder into our frequency array:

```cpp
cnt[p[r-k-1]]++;
```

Here, `cnt[x]` stores the frequency of eligible prefix indices having remainder `x`.

### Second line: `ans += cnt[p[r]]`

**This is the same counting logic as LC 974.**

Remember:

For a subarray sum to be divisible by `k`, we need:

\[
p[r]=p[j]
\]

We are currently considering ending index `r`.

So we ask:

**How many eligible previous prefix indices have the same remainder as the current prefix `p[r]`?**

That number is:

```cpp
cnt[p[r]]
```

Every matching prefix index gives us one valid subarray ending at `r`.

Therefore:

```cpp
ans += cnt[p[r]];
```

We add the entire frequency because multiple eligible previous prefix indices can have the same remainder.

**In simple words:**

For the current ending index `r`, count all eligible starting positions that produce a subarray sum divisible by `k`, and add them to our answer.

---

## 6. Complete Dry Run

Consider:

```cpp
n = 5, k = 2
a = [1, 2, 3, 4, 5]
```

First, calculate prefix sums modulo `k`.

| Index | Prefix sum | Remainder |
|---|---|---|
| 0 | 0 | 0 |
| 1 | 1 | 1 |
| 2 | 3 | 1 |
| 3 | 6 | 0 |
| 4 | 10 | 0 |
| 5 | 15 | 1 |

Therefore:

```cpp
p = [0, 1, 1, 0, 0, 1];
```

Initially:

```cpp
cnt[0] = 0;
cnt[1] = 0;
ans = 0;
```

### Iteration 1: r = 3

Newly eligible index:

\[
j=3-2-1=0
\]

Insert its remainder:

```cpp
cnt[p[0]]++;
cnt[0]++;
```

Now:

```cpp
cnt[0] = 1;
```

Current remainder:

```cpp
p[3] = 0;
```

Count matching previous prefixes:

```cpp
ans += cnt[p[3]];
ans += cnt[0];
ans += 1;
```

Now `ans = 1`.

### Iteration 2: r = 4

Newly eligible index:

\[
j=4-2-1=1
\]

Insert its remainder:

```cpp
cnt[p[1]]++;
cnt[1]++;
```

Now:

```cpp
cnt[0] = 1;
cnt[1] = 1;
```

Current remainder:

```cpp
p[4] = 0;
```

Count matching previous prefixes:

```cpp
ans += cnt[p[4]];
ans += cnt[0];
ans += 1;
```

Now `ans = 2`.

### Iteration 3: r = 5

Newly eligible index:

\[
j=5-2-1=2
\]

Insert its remainder:

```cpp
cnt[p[2]]++;
cnt[1]++;
```

Now:

```cpp
cnt[0] = 1;
cnt[1] = 2;
```

Current remainder:

```cpp
p[5] = 1;
```

Count matching previous prefixes:

```cpp
ans += cnt[p[5]];
ans += cnt[1];
ans += 2;
```

Now `ans = 4`.

### Why did we add 2 in the last iteration?

Because two eligible previous prefix indices have remainder `1`:

- `p[1] = 1`
- `p[2] = 1`

They give us two different subarrays:

1. `a[2...5]` = `[2,3,4,5]`, sum = 14.
2. `a[3...5]` = `[3,4,5]`, sum = 12.

Both sums are divisible by 2, and both lengths are greater than 2.

Therefore, we add 2.

**Final answer = 4.**

---

## 7. Complete C++ Solution

```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n, k;
    cin >> n >> k;

    vector<int> p(n + 1, 0);

    for(int i = 1; i <= n; i++) {
        int x;
        cin >> x;

        p[i] = (p[i-1] + x) % k;
    }

    vector<long long> cnt(k, 0);

    long long ans = 0;

    for(int r = k + 1; r <= n; r++) {

        cnt[p[r-k-1]]++;  // newly eligible prefix index

        ans += cnt[p[r]]; // count matching eligible remainders
    }

    cout << ans << '\n';
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int t;
    cin >> t;

    while(t--) {
        solve();
    }

    return 0;
}
```

---

## 8. Complexity Analysis

- **Time Complexity:** O(n) per test case.
- **Space Complexity:** O(n + k).

We use `long long` for `ans` because the number of valid subarrays can be as large as O(n²).

---

## 9. Final Takeaway: LC 974 vs This Problem

| LC 974 | Monocarp's Problem |
|---|---|
| Every previous prefix index is eligible. | Only prefix indices satisfying `j <= r-k-1` are eligible. |
| Insert previous prefix remainders. | Insert only the newly eligible prefix remainder. |
| Count matching remainders. | Same counting logic: `ans += cnt[p[r]]`. |

**Remember:**

The counting logic is exactly the same as LC 974.

The only change is that the length condition restricts which previous prefix indices are allowed into our frequency array.

The most important line to understand:

```cpp
ans += cnt[p[r]];
```

It means:

"How many eligible previous prefix sums have the same remainder as my current prefix sum? Each match gives me one valid subarray, so add all of them to my answer."
