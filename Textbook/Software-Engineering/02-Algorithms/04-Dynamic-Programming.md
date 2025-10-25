# Chapter 4: Dynamic Programming

## Table of Contents
- [Introduction](#introduction)
- [Core Concepts](#core-concepts)
- [1D Dynamic Programming](#1d-dynamic-programming)
- [2D Dynamic Programming](#2d-dynamic-programming)
- [Common Patterns](#common-patterns)
- [Optimization Techniques](#optimization-techniques)
- [Practice Problems](#practice-problems)

---

## Introduction

### What is Dynamic Programming?

Dynamic Programming (DP) is an optimization technique that solves complex problems by breaking them down into simpler subproblems and storing their solutions to avoid redundant calculations.

**Key Characteristics:**
1. **Optimal Substructure**: Optimal solution contains optimal solutions to subproblems
2. **Overlapping Subproblems**: Same subproblems are solved multiple times

**Approaches:**
- **Top-Down (Memoization)**: Recursion + caching
- **Bottom-Up (Tabulation)**: Iterative + table filling

---

## Core Concepts

### When to Use DP

Look for these signs:
1. Problem asks for optimal value (max/min)
2. Problem asks to count total ways
3. Multiple choices at each step
4. Subproblems overlap

### DP Framework

```
1. Define state (what does dp[i] represent?)
2. Define recurrence relation (how to compute dp[i]?)
3. Define base cases
4. Determine iteration order
5. Optimize space if possible
```

---

## 1D Dynamic Programming

### 1. Fibonacci Number

Classic DP example demonstrating memoization vs tabulation.

**Recursive (Exponential Time):**

**JavaScript:**
```javascript
// Naive recursion - O(2^n)
function fibRecursive(n) {
    if (n <= 1) return n;
    return fibRecursive(n - 1) + fibRecursive(n - 2);
}

// Memoization - O(n)
function fibMemo(n, memo = {}) {
    if (n in memo) return memo[n];
    if (n <= 1) return n;

    memo[n] = fibMemo(n - 1, memo) + fibMemo(n - 2, memo);
    return memo[n];
}

// Tabulation - O(n)
function fibTab(n) {
    if (n <= 1) return n;

    const dp = [0, 1];
    for (let i = 2; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }

    return dp[n];
}

// Space Optimized - O(1) space
function fibOptimized(n) {
    if (n <= 1) return n;

    let prev2 = 0, prev1 = 1;

    for (let i = 2; i <= n; i++) {
        const curr = prev1 + prev2;
        prev2 = prev1;
        prev1 = curr;
    }

    return prev1;
}

console.log(fibOptimized(10));  // 55
```

**Python:**
```python
# Naive recursion - O(2^n)
def fib_recursive(n):
    if n <= 1:
        return n
    return fib_recursive(n - 1) + fib_recursive(n - 2)

# Memoization - O(n)
def fib_memo(n, memo=None):
    if memo is None:
        memo = {}

    if n in memo:
        return memo[n]
    if n <= 1:
        return n

    memo[n] = fib_memo(n - 1, memo) + fib_memo(n - 2, memo)
    return memo[n]

# Using functools.lru_cache
from functools import lru_cache

@lru_cache(maxsize=None)
def fib_cached(n):
    if n <= 1:
        return n
    return fib_cached(n - 1) + fib_cached(n - 2)

# Tabulation - O(n)
def fib_tab(n):
    if n <= 1:
        return n

    dp = [0, 1]
    for i in range(2, n + 1):
        dp.append(dp[i - 1] + dp[i - 2])

    return dp[n]

# Space Optimized - O(1) space
def fib_optimized(n):
    if n <= 1:
        return n

    prev2, prev1 = 0, 1

    for _ in range(2, n + 1):
        curr = prev1 + prev2
        prev2, prev1 = prev1, curr

    return prev1

print(fib_optimized(10))  # 55
```

### 2. Climbing Stairs

How many ways to climb n stairs taking 1 or 2 steps at a time?

**JavaScript:**
```javascript
function climbStairs(n) {
    if (n <= 2) return n;

    let prev2 = 1, prev1 = 2;

    for (let i = 3; i <= n; i++) {
        const curr = prev1 + prev2;
        prev2 = prev1;
        prev1 = curr;
    }

    return prev1;
}

console.log(climbStairs(5));  // 8
// Ways: 11111, 1112, 1121, 1211, 2111, 122, 212, 221

// State: dp[i] = ways to reach stair i
// Recurrence: dp[i] = dp[i-1] + dp[i-2]
// Base: dp[1] = 1, dp[2] = 2
// Time: O(n), Space: O(1)
```

**Python:**
```python
def climb_stairs(n):
    if n <= 2:
        return n

    prev2, prev1 = 1, 2

    for _ in range(3, n + 1):
        curr = prev1 + prev2
        prev2, prev1 = prev1, curr

    return prev1

print(climb_stairs(5))  # 8

# Time: O(n), Space: O(1)
```

### 3. House Robber

Rob houses to maximize money without robbing adjacent houses.

**JavaScript:**
```javascript
function rob(nums) {
    if (nums.length === 0) return 0;
    if (nums.length === 1) return nums[0];

    let prev2 = 0, prev1 = 0;

    for (const num of nums) {
        const curr = Math.max(prev1, prev2 + num);
        prev2 = prev1;
        prev1 = curr;
    }

    return prev1;
}

console.log(rob([2, 7, 9, 3, 1]));  // 12 (2 + 9 + 1)

// State: dp[i] = max money robbing houses 0..i
// Recurrence: dp[i] = max(dp[i-1], dp[i-2] + nums[i])
//   - Don't rob house i: dp[i-1]
//   - Rob house i: dp[i-2] + nums[i]
// Base: dp[0] = nums[0], dp[1] = max(nums[0], nums[1])
// Time: O(n), Space: O(1)
```

**Python:**
```python
def rob(nums):
    if not nums:
        return 0
    if len(nums) == 1:
        return nums[0]

    prev2, prev1 = 0, 0

    for num in nums:
        curr = max(prev1, prev2 + num)
        prev2, prev1 = prev1, curr

    return prev1

print(rob([2, 7, 9, 3, 1]))  # 12

# Time: O(n), Space: O(1)
```

### 4. Coin Change

Minimum coins needed to make amount.

**JavaScript:**
```javascript
function coinChange(coins, amount) {
    const dp = new Array(amount + 1).fill(Infinity);
    dp[0] = 0;

    for (let i = 1; i <= amount; i++) {
        for (const coin of coins) {
            if (coin <= i) {
                dp[i] = Math.min(dp[i], dp[i - coin] + 1);
            }
        }
    }

    return dp[amount] === Infinity ? -1 : dp[amount];
}

console.log(coinChange([1, 2, 5], 11));  // 3 (5 + 5 + 1)

// State: dp[i] = min coins to make amount i
// Recurrence: dp[i] = min(dp[i], dp[i-coin] + 1) for all coins
// Base: dp[0] = 0
// Time: O(amount * coins), Space: O(amount)
```

**Python:**
```python
def coin_change(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0

    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i:
                dp[i] = min(dp[i], dp[i - coin] + 1)

    return dp[amount] if dp[amount] != float('inf') else -1

print(coin_change([1, 2, 5], 11))  # 3

# Time: O(amount * coins), Space: O(amount)
```

### 5. Longest Increasing Subsequence

**JavaScript:**
```javascript
function lengthOfLIS(nums) {
    if (nums.length === 0) return 0;

    const dp = new Array(nums.length).fill(1);

    for (let i = 1; i < nums.length; i++) {
        for (let j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
    }

    return Math.max(...dp);
}

console.log(lengthOfLIS([10, 9, 2, 5, 3, 7, 101, 18]));  // 4 ([2,3,7,101])

// O(n²) solution
// State: dp[i] = length of LIS ending at index i
// Recurrence: dp[i] = max(dp[j] + 1) where j < i and nums[j] < nums[i]
// Time: O(n²), Space: O(n)

// O(n log n) solution using binary search
function lengthOfLISOptimized(nums) {
    const tails = [];

    for (const num of nums) {
        let left = 0, right = tails.length;

        while (left < right) {
            const mid = Math.floor((left + right) / 2);
            if (tails[mid] < num) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }

        if (left === tails.length) {
            tails.push(num);
        } else {
            tails[left] = num;
        }
    }

    return tails.length;
}

// Time: O(n log n), Space: O(n)
```

**Python:**
```python
def length_of_lis(nums):
    if not nums:
        return 0

    dp = [1] * len(nums)

    for i in range(1, len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)

    return max(dp)

print(length_of_lis([10, 9, 2, 5, 3, 7, 101, 18]))  # 4

# O(n²) solution
# Time: O(n²), Space: O(n)

# O(n log n) solution using binary search
import bisect

def length_of_lis_optimized(nums):
    tails = []

    for num in nums:
        pos = bisect.bisect_left(tails, num)

        if pos == len(tails):
            tails.append(num)
        else:
            tails[pos] = num

    return len(tails)

# Time: O(n log n), Space: O(n)
```

---

## 2D Dynamic Programming

### 1. Unique Paths

Number of ways to reach bottom-right from top-left.

**JavaScript:**
```javascript
function uniquePaths(m, n) {
    // Create 2D DP table
    const dp = Array(m).fill(null).map(() => Array(n).fill(0));

    // Initialize first row and column
    for (let i = 0; i < m; i++) dp[i][0] = 1;
    for (let j = 0; j < n; j++) dp[0][j] = 1;

    // Fill the table
    for (let i = 1; i < m; i++) {
        for (let j = 1; j < n; j++) {
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
        }
    }

    return dp[m-1][n-1];
}

// Space optimized O(n)
function uniquePathsOptimized(m, n) {
    let dp = new Array(n).fill(1);

    for (let i = 1; i < m; i++) {
        for (let j = 1; j < n; j++) {
            dp[j] += dp[j-1];
        }
    }

    return dp[n-1];
}

console.log(uniquePaths(3, 7));  // 28

// Time: O(m*n), Space: O(m*n) or O(n) optimized
```

**Python:**
```python
def unique_paths(m, n):
    # Create 2D DP table
    dp = [[0] * n for _ in range(m)]

    # Initialize first row and column
    for i in range(m):
        dp[i][0] = 1
    for j in range(n):
        dp[0][j] = 1

    # Fill the table
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]

    return dp[m-1][n-1]

# Space optimized
def unique_paths_optimized(m, n):
    dp = [1] * n

    for i in range(1, m):
        for j in range(1, n):
            dp[j] += dp[j-1]

    return dp[n-1]

print(unique_paths(3, 7))  # 28

# Time: O(m*n), Space: O(n)
```

### 2. Longest Common Subsequence

**JavaScript:**
```javascript
function longestCommonSubsequence(text1, text2) {
    const m = text1.length, n = text2.length;
    const dp = Array(m + 1).fill(null).map(() => Array(n + 1).fill(0));

    for (let i = 1; i <= m; i++) {
        for (let j = 1; j <= n; j++) {
            if (text1[i-1] === text2[j-1]) {
                dp[i][j] = dp[i-1][j-1] + 1;
            } else {
                dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);
            }
        }
    }

    return dp[m][n];
}

console.log(longestCommonSubsequence("abcde", "ace"));  // 3 ("ace")

// State: dp[i][j] = LCS length of text1[0..i-1] and text2[0..j-1]
// Recurrence:
//   if text1[i-1] == text2[j-1]: dp[i][j] = dp[i-1][j-1] + 1
//   else: dp[i][j] = max(dp[i-1][j], dp[i][j-1])
// Time: O(m*n), Space: O(m*n)
```

**Python:**
```python
def longest_common_subsequence(text1, text2):
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])

    return dp[m][n]

print(longest_common_subsequence("abcde", "ace"))  # 3

# Time: O(m*n), Space: O(m*n)
```

### 3. Edit Distance

Minimum operations to convert word1 to word2.

**JavaScript:**
```javascript
function minDistance(word1, word2) {
    const m = word1.length, n = word2.length;
    const dp = Array(m + 1).fill(null).map(() => Array(n + 1).fill(0));

    // Initialize base cases
    for (let i = 0; i <= m; i++) dp[i][0] = i;
    for (let j = 0; j <= n; j++) dp[0][j] = j;

    for (let i = 1; i <= m; i++) {
        for (let j = 1; j <= n; j++) {
            if (word1[i-1] === word2[j-1]) {
                dp[i][j] = dp[i-1][j-1];
            } else {
                dp[i][j] = 1 + Math.min(
                    dp[i-1][j],      // delete
                    dp[i][j-1],      // insert
                    dp[i-1][j-1]     // replace
                );
            }
        }
    }

    return dp[m][n];
}

console.log(minDistance("horse", "ros"));  // 3

// Time: O(m*n), Space: O(m*n)
```

**Python:**
```python
def min_distance(word1, word2):
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    # Initialize base cases
    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = 1 + min(
                    dp[i-1][j],      # delete
                    dp[i][j-1],      # insert
                    dp[i-1][j-1]     # replace
                )

    return dp[m][n]

print(min_distance("horse", "ros"))  # 3

# Time: O(m*n), Space: O(m*n)
```

### 4. 0/1 Knapsack

**JavaScript:**
```javascript
function knapsack(weights, values, capacity) {
    const n = weights.length;
    const dp = Array(n + 1).fill(null)
        .map(() => Array(capacity + 1).fill(0));

    for (let i = 1; i <= n; i++) {
        for (let w = 1; w <= capacity; w++) {
            if (weights[i-1] <= w) {
                dp[i][w] = Math.max(
                    dp[i-1][w],  // Don't take item
                    dp[i-1][w - weights[i-1]] + values[i-1]  // Take item
                );
            } else {
                dp[i][w] = dp[i-1][w];
            }
        }
    }

    return dp[n][capacity];
}

// Space optimized - O(capacity)
function knapsackOptimized(weights, values, capacity) {
    const dp = new Array(capacity + 1).fill(0);

    for (let i = 0; i < weights.length; i++) {
        for (let w = capacity; w >= weights[i]; w--) {
            dp[w] = Math.max(dp[w], dp[w - weights[i]] + values[i]);
        }
    }

    return dp[capacity];
}

console.log(knapsack([1, 3, 4, 5], [1, 4, 5, 7], 7));  // 9

// Time: O(n * capacity), Space: O(capacity)
```

**Python:**
```python
def knapsack(weights, values, capacity):
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        for w in range(1, capacity + 1):
            if weights[i-1] <= w:
                dp[i][w] = max(
                    dp[i-1][w],  # Don't take item
                    dp[i-1][w - weights[i-1]] + values[i-1]  # Take item
                )
            else:
                dp[i][w] = dp[i-1][w]

    return dp[n][capacity]

# Space optimized
def knapsack_optimized(weights, values, capacity):
    dp = [0] * (capacity + 1)

    for i in range(len(weights)):
        for w in range(capacity, weights[i] - 1, -1):
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i])

    return dp[capacity]

print(knapsack([1, 3, 4, 5], [1, 4, 5, 7], 7))  # 9

# Time: O(n * capacity), Space: O(capacity)
```

---

## Common Patterns

### Pattern 1: Fibonacci-Style
- Each state depends on 1-2 previous states
- Examples: Climbing Stairs, House Robber

### Pattern 2: Unbounded Choices
- Can use same element multiple times
- Examples: Coin Change (unlimited coins)

### Pattern 3: 0/1 Choice
- Each element used once or not at all
- Examples: 0/1 Knapsack, Partition Equal Subset Sum

### Pattern 4: Sequence Matching
- Compare two sequences
- Examples: LCS, Edit Distance

### Pattern 5: Game Theory
- Minimax strategy
- Examples: Stone Game, Predict the Winner

---

## Optimization Techniques

### 1. Space Optimization
- Reduce 2D to 1D if only previous row/column needed
- Use variables for constant space

### 2. State Compression
- Use bitmask for subset problems
- Encode multiple dimensions

### 3. Memorization Optimization
- Only store necessary states
- Use hash map for sparse data

---

## Practice Problems

### Easy
1. **Climbing Stairs** - Basic fibonacci pattern
2. **Min Cost Climbing Stairs** - Cost variant
3. **Divisor Game** - Simple game theory

### Medium
1. **House Robber II** - Circular array
2. **Decode Ways** - Count ways to decode
3. **Unique Paths II** - With obstacles
4. **Maximum Product Subarray** - Track min and max
5. **Partition Equal Subset Sum** - 0/1 knapsack variant
6. **Target Sum** - Count ways to reach target

### Hard
1. **Regular Expression Matching** - Pattern matching DP
2. **Longest Valid Parentheses** - Track valid lengths
3. **Maximal Rectangle** - 2D histogram
4. **Distinct Subsequences** - Count subsequences
5. **Interleaving String** - String manipulation

---

## Key Takeaways

1. **Identify overlapping subproblems** - key to using DP
2. **Define state clearly** - what does dp[i] represent?
3. **Write recurrence relation** - how to compute from subproblems
4. **Start with recursion + memoization** - easier to think through
5. **Optimize to tabulation** - better performance
6. **Optimize space** - often can reduce dimensions

---

## Next Chapter

Continue to [Greedy Algorithms](./05-Greedy-Algorithms.md) to learn optimization strategies.

[← Previous: Recursion](./03-Recursion.md) | [Next: Greedy Algorithms →](./05-Greedy-Algorithms.md)
