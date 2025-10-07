# Dynamic Programming & Backtracking Cheatsheet
## Optimization & Exploration Patterns

---

# Part 1: Dynamic Programming (DP)

## 🎯 When to Use Dynamic Programming

### Key Indicators:
- ✅ **Optimal substructure**: Optimal solution contains optimal solutions to subproblems
- ✅ **Overlapping subproblems**: Same subproblems solved multiple times
- ✅ **Counting** ways to do something
- ✅ **Min/Max** optimization problems
- ✅ Keywords: "maximum", "minimum", "longest", "shortest", "count ways"

### Classic DP Problems:
- Path counting in grids
- Longest subsequences
- Coin change problems
- Knapsack variants
- String matching (edit distance)

### Robotics Applications:
- **Trajectory optimization** - find optimal path
- **Motion planning** - minimize energy/time
- **Resource allocation** - optimize battery usage
- **Task scheduling** - minimize completion time

## ⏱️ Time Complexity: Usually O(n²) or O(n×m)

---

## 🎨 DP Approaches

### Approach 1: Top-Down (Memoization)
```cpp
// Recursive with caching
unordered_map<string, int> memo;

int solve(int n) {
    // Base case
    if (n <= 1) return n;
    
    // Check cache
    string key = to_string(n);
    if (memo.count(key)) {
        return memo[key];
    }
    
    // Compute and cache
    memo[key] = solve(n-1) + solve(n-2);
    return memo[key];
}

// Pros: Intuitive, only computes needed subproblems
// Cons: Recursion overhead, stack overflow risk
```

### Approach 2: Bottom-Up (Tabulation)
```cpp
// Iterative with table
int solve(int n) {
    if (n <= 1) return n;
    
    vector<int> dp(n + 1);
    dp[0] = 0;
    dp[1] = 1;
    
    for (int i = 2; i <= n; i++) {
        dp[i] = dp[i-1] + dp[i-2];
    }
    
    return dp[n];
}

// Pros: No recursion, more efficient
// Cons: Computes all subproblems
```

### Approach 3: Space Optimized
```cpp
// Only keep what's needed
int solve(int n) {
    if (n <= 1) return n;
    
    int prev2 = 0, prev1 = 1;
    
    for (int i = 2; i <= n; i++) {
        int curr = prev1 + prev2;
        prev2 = prev1;
        prev1 = curr;
    }
    
    return prev1;
}

// Reduced space from O(n) to O(1)
```

---

## 📚 Essential DP Problems

### Problem 1: Climbing Stairs (Foundation)
**LeetCode 70 - Easy**

**Description:** You are climbing a staircase. It takes n steps to reach the top. Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

**Example:**
```
Input: n = 3
Output: 3
Explanation: Three ways:
  1. 1 step + 1 step + 1 step
  2. 1 step + 2 steps
  3. 2 steps + 1 step
```

**Solution:**
```cpp
int climbStairs(int n) {
    // Base cases
    if (n <= 2) return n;
    
    // DP relation: ways[i] = ways[i-1] + ways[i-2]
    // To reach step i, can come from step i-1 or i-2
    
    int prev2 = 1;  // ways to reach step 1
    int prev1 = 2;  // ways to reach step 2
    
    for (int i = 3; i <= n; i++) {
        int curr = prev1 + prev2;
        prev2 = prev1;
        prev1 = curr;
    }
    
    return prev1;
}

// Time: O(n)
// Space: O(1)
```

**Pattern Recognition:**
```
This is Fibonacci!

n=1: 1 way  (1)
n=2: 2 ways (1+1, 2)
n=3: 3 ways (1+1+1, 1+2, 2+1)
n=4: 5 ways (1+1+1+1, 1+1+2, 1+2+1, 2+1+1, 2+2)

Pattern: f(n) = f(n-1) + f(n-2)
```

---

### Problem 2: Coin Change ⭐
**LeetCode 322 - Medium**

**Description:** You are given an integer array `coins` representing different denominations and an integer `amount`. Return the fewest number of coins needed to make up that amount. If impossible, return -1.

**Example:**
```
Input: coins = [1,2,5], amount = 11
Output: 3
Explanation: 11 = 5 + 5 + 1

Input: coins = [2], amount = 3
Output: -1
Explanation: Cannot make 3 with only coin 2
```

**Solution (Bottom-Up):**
```cpp
int coinChange(vector<int>& coins, int amount) {
    // dp[i] = minimum coins needed to make amount i
    vector<int> dp(amount + 1, amount + 1);  // Initialize with impossible value
    
    dp[0] = 0;  // Base case: 0 coins needed for amount 0
    
    // For each amount from 1 to target
    for (int i = 1; i <= amount; i++) {
        // Try each coin
        for (int coin : coins) {
            if (coin <= i) {
                // Can use this coin
                // dp[i] = min(current, 1 + dp[i - coin])
                dp[i] = min(dp[i], 1 + dp[i - coin]);
            }
        }
    }
    
    return dp[amount] > amount ? -1 : dp[amount];
}

// Time: O(amount × coins.size())
// Space: O(amount)
```

**Visualization:**
```
coins = [1,2,5], amount = 11

Build dp table:

dp[0] = 0  (base case)

dp[1]:
  Try coin 1: dp[1] = min(INF, 1 + dp[0]) = 1
  Result: dp[1] = 1  (one coin of 1)

dp[2]:
  Try coin 1: dp[2] = min(INF, 1 + dp[1]) = 2
  Try coin 2: dp[2] = min(2, 1 + dp[0]) = 1
  Result: dp[2] = 1  (one coin of 2)

dp[5]:
  Try coin 1: dp[5] = min(INF, 1 + dp[4]) = 1 + 2 = 3
  Try coin 2: dp[5] = min(3, 1 + dp[3]) = min(3, 1 + 2) = 3
  Try coin 5: dp[5] = min(3, 1 + dp[0]) = 1
  Result: dp[5] = 1  (one coin of 5)

dp[11]:
  Try all coins, find minimum
  Result: dp[11] = 3  (5 + 5 + 1)

DP Table:
Amount:  0  1  2  3  4  5  6  7  8  9  10 11
Coins:   0  1  1  2  2  1  2  2  3  3  2  3
```

**Top-Down Alternative:**
```cpp
class Solution {
    unordered_map<int, int> memo;
    
public:
    int coinChange(vector<int>& coins, int amount) {
        return dp(coins, amount);
    }
    
private:
    int dp(vector<int>& coins, int amount) {
        // Base cases
        if (amount == 0) return 0;
        if (amount < 0) return -1;
        
        // Check memo
        if (memo.count(amount)) return memo[amount];
        
        int minCoins = INT_MAX;
        
        // Try each coin
        for (int coin : coins) {
            int sub = dp(coins, amount - coin);
            if (sub != -1) {
                minCoins = min(minCoins, sub + 1);
            }
        }
        
        memo[amount] = (minCoins == INT_MAX) ? -1 : minCoins;
        return memo[amount];
    }
};
```

---

### Problem 3: Longest Increasing Subsequence ⭐
**LeetCode 300 - Medium**

**Description:** Given an integer array `nums`, return the length of the longest strictly increasing subsequence.

**Example:**
```
Input: nums = [10,9,2,5,3,7,101,18]
Output: 4
Explanation: LIS is [2,3,7,101] or [2,3,7,18]

Input: nums = [0,1,0,3,2,3]
Output: 4
Explanation: LIS is [0,1,2,3]
```

**Solution (DP O(n²)):**
```cpp
int lengthOfLIS(vector<int>& nums) {
    int n = nums.size();
    
    // dp[i] = length of LIS ending at index i
    vector<int> dp(n, 1);  // Each element is LIS of length 1
    
    int maxLen = 1;
    
    for (int i = 1; i < n; i++) {
        // Check all previous elements
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                // Can extend LIS ending at j
                dp[i] = max(dp[i], dp[j] + 1);
            }
        }
        maxLen = max(maxLen, dp[i]);
    }
    
    return maxLen;
}

// Time: O(n²)
// Space: O(n)
```

**Visualization:**
```
nums = [10, 9, 2, 5, 3, 7, 101, 18]

DP computation:

i=0: nums[0]=10
  dp[0] = 1  (just [10])

i=1: nums[1]=9
  Check j=0: 10 not < 9, skip
  dp[1] = 1  (just [9])

i=2: nums[2]=2
  Check j=0: 10 not < 2
  Check j=1: 9 not < 2
  dp[2] = 1  (just [2])

i=3: nums[3]=5
  Check j=0: 10 not < 5
  Check j=1: 9 not < 5
  Check j=2: 2 < 5, dp[3] = max(1, dp[2]+1) = 2
  dp[3] = 2  ([2,5])

i=4: nums[4]=3
  Check j=0,1: no
  Check j=2: 2 < 3, dp[4] = max(1, dp[2]+1) = 2
  dp[4] = 2  ([2,3])

i=5: nums[5]=7
  Check j=2: 2 < 7, dp[5] = max(1, 1+1) = 2
  Check j=3: 5 < 7, dp[5] = max(2, 2+1) = 3
  Check j=4: 3 < 7, dp[5] = max(3, 2+1) = 3
  dp[5] = 3  ([2,5,7] or [2,3,7])

i=6: nums[6]=101
  Check all previous, max is from j=5
  dp[6] = 4  ([2,5,7,101])

i=7: nums[7]=18
  Best is from j=5: dp[7] = 4  ([2,5,7,18])

Answer: max(dp) = 4
```

**Optimized Solution (Binary Search O(n log n)):**
```cpp
int lengthOfLIS(vector<int>& nums) {
    vector<int> tails;  // tails[i] = smallest tail of LIS of length i+1
    
    for (int num : nums) {
        // Binary search for position to replace/append
        auto it = lower_bound(tails.begin(), tails.end(), num);
        
        if (it == tails.end()) {
            // num is larger than all tails, extend LIS
            tails.push_back(num);
        } else {
            // Replace to keep smallest possible tail
            *it = num;
        }
    }
    
    return tails.size();
}

// Time: O(n log n)
// Space: O(n)
```

---

### Problem 4: Unique Paths (2D DP) ⭐
**LeetCode 62 - Medium**

**Description:** Robot is located at top-left corner of a m x n grid. Robot can only move down or right. How many unique paths are there to bottom-right corner?

**Example:**
```
Input: m = 3, n = 7
Output: 28

Grid visualization:
S . . . . . .
. . . . . . .
. . . . . . E

S = Start, E = End
```

**Solution:**
```cpp
int uniquePaths(int m, int n) {
    // dp[i][j] = number of paths to reach cell (i,j)
    vector<vector<int>> dp(m, vector<int>(n, 1));
    
    // First row and column all have 1 path (straight line)
    // Already initialized to 1
    
    // Fill rest of table
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            // Can reach (i,j) from top or left
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
        }
    }
    
    return dp[m-1][n-1];
}

// Time: O(m × n)
// Space: O(m × n)
```

**Visualization:**
```
m=3, n=4

Build DP table:

Initial (first row and column = 1):
1  1  1  1
1  .  .  .
1  .  .  .

Fill (i=1, j=1): dp[1][1] = dp[0][1] + dp[1][0] = 1 + 1 = 2
1  1  1  1
1  2  .  .
1  .  .  .

Fill (i=1, j=2): dp[1][2] = dp[0][2] + dp[1][1] = 1 + 2 = 3
1  1  1  1
1  2  3  .
1  .  .  .

Continue filling...

Final table:
1  1  1  1
1  2  3  4
1  3  6  10

Answer: dp[2][3] = 10 paths
```

**Space Optimized (O(n)):**
```cpp
int uniquePaths(int m, int n) {
    vector<int> dp(n, 1);
    
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            dp[j] += dp[j-1];
        }
    }
    
    return dp[n-1];
}

// Time: O(m × n)
// Space: O(n) - only keep one row
```

---

### Problem 5: Word Break ⭐
**LeetCode 139 - Medium**

**Description:** Given a string `s` and a dictionary `wordDict`, return true if `s` can be segmented into a space-separated sequence of one or more dictionary words.

**Example:**
```
Input: s = "leetcode", wordDict = ["leet","code"]
Output: true
Explanation: "leetcode" can be segmented as "leet code"

Input: s = "applepenapple", wordDict = ["apple","pen"]
Output: true
Explanation: "applepenapple" = "apple pen apple"

Input: s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]
Output: false
```

**Solution:**
```cpp
bool wordBreak(string s, vector<string>& wordDict) {
    int n = s.length();
    unordered_set<string> dict(wordDict.begin(), wordDict.end());
    
    // dp[i] = true if s[0...i-1] can be segmented
    vector<bool> dp(n + 1, false);
    dp[0] = true;  // Empty string can be segmented
    
    for (int i = 1; i <= n; i++) {
        // Try all possible last words ending at position i
        for (int j = 0; j < i; j++) {
            // If s[0...j-1] can be segmented
            // AND s[j...i-1] is in dictionary
            if (dp[j] && dict.count(s.substr(j, i - j))) {
                dp[i] = true;
                break;  // Found one way, done
            }
        }
    }
    
    return dp[n];
}

// Time: O(n² × L) where L = average word length (for substr)
// Space: O(n + dict size)
```

**Visualization:**
```
s = "leetcode"
dict = ["leet", "code"]

Build DP table:

dp[0] = true  (empty string)

dp[1]: Check substrings ending at index 1
  s[0:1] = "l" → not in dict
  dp[1] = false

dp[2]: "le" → not in dict
  dp[2] = false

dp[3]: "lee" → not in dict
  dp[3] = false

dp[4]: Check substrings
  s[0:4] = "leet" → in dict! AND dp[0] = true
  dp[4] = true ✓

dp[5]: "leetc" parts?
  "c" not in dict, "etc" not in dict...
  dp[5] = false

dp[6]: "leetco" parts?
  dp[6] = false

dp[7]: "leetcod" parts?
  dp[7] = false

dp[8]: Check substrings ending at 8
  s[0:8] = "leetcode" → not in dict
  s[4:8] = "code" → in dict! AND dp[4] = true
  dp[8] = true ✓

Answer: dp[8] = true
Segmentation: "leet" + "code"
```

---

### Problem 6: Longest Common Subsequence
**LeetCode 1143 - Medium**

**Description:** Given two strings `text1` and `text2`, return the length of their longest common subsequence. A subsequence is a sequence that can be derived from another sequence by deleting some or no elements without changing the order.

**Example:**
```
Input: text1 = "abcde", text2 = "ace" 
Output: 3
Explanation: LCS is "ace"

Input: text1 = "abc", text2 = "abc"
Output: 3

Input: text1 = "abc", text2 = "def"
Output: 0
```

**Solution:**
```cpp
int longestCommonSubsequence(string text1, string text2) {
    int m = text1.length();
    int n = text2.length();
    
    // dp[i][j] = LCS length of text1[0..i-1] and text2[0..j-1]
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
    
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (text1[i-1] == text2[j-1]) {
                // Characters match, extend LCS
                dp[i][j] = dp[i-1][j-1] + 1;
            } else {
                // Characters don't match, take max of:
                // - Skip char from text1
                // - Skip char from text2
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }
    }
    
    return dp[m][n];
}

// Time: O(m × n)
// Space: O(m × n)
```

**Visualization:**
```
text1 = "abcde"
text2 = "ace"

Build DP table:

    ""  a  c  e
""   0  0  0  0
a    0  1  1  1
b    0  1  1  1
c    0  1  2  2
d    0  1  2  2
e    0  1  2  3

Step-by-step for key cells:

dp[1][1]: text1[0]='a', text2[0]='a' → match!
  dp[1][1] = dp[0][0] + 1 = 1

dp[2][1]: text1[1]='b', text2[0]='a' → no match
  dp[2][1] = max(dp[1][1], dp[2][0]) = max(1, 0) = 1

dp[3][2]: text1[2]='c', text2[1]='c' → match!
  dp[3][2] = dp[2][1] + 1 = 2

dp[5][3]: text1[4]='e', text2[2]='e' → match!
  dp[5][3] = dp[4][2] + 1 = 3

Answer: 3 (LCS = "ace")
```

---

## 🎯 DP Pattern Recognition

| Problem Type | DP Relation | Example |
|--------------|-------------|---------|
| **Fibonacci-like** | `dp[i] = dp[i-1] + dp[i-2]` | Climbing Stairs |
| **Min/Max path sum** | `dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1])` | Unique Paths |
| **Subsequence** | Match: `dp[i-1][j-1]+1` <br> No match: `max(dp[i-1][j], dp[i][j-1])` | LCS |
| **Knapsack** | `dp[i][w] = max(dp[i-1][w], val[i] + dp[i-1][w-wt[i]])` | 0/1 Knapsack |
| **String matching** | Edit distance, word break | Word Break, Edit Distance |

---

# Part 2: Backtracking

## 🎯 When to Use Backtracking

### Key Indicators:
- ✅ Generate **all possible solutions**
- ✅ **Permutations**, **combinations**, **subsets**
- ✅ Constraint satisfaction (N-Queens, Sudoku)
- ✅ Exploring **decision tree** exhaustively
- ✅ Keywords: "all", "find all", "generate all"

### Template:
```cpp
void backtrack(vector<int>& current, /* other params */) {
    // BASE CASE: Found complete solution
    if (isComplete(current)) {
        result.push_back(current);
        return;
    }
    
    // TRY all choices
    for (int choice : getChoices()) {
        // MAKE choice
        current.push_back(choice);
        
        // EXPLORE with this choice
        backtrack(current, ...);
        
        // UNDO choice (backtrack)
        current.pop_back();
    }
}
```

---

## 📚 Essential Backtracking Problems

### Problem 1: Subsets ⭐
**LeetCode 78 - Medium**

**Description:** Given an integer array `nums` of unique elements, return all possible subsets (the power set). The solution set must not contain duplicate subsets.

**Example:**
```
Input: nums = [1,2,3]
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

**Solution:**
```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> result;
        vector<int> current;
        backtrack(nums, 0, current, result);
        return result;
    }
    
private:
    void backtrack(vector<int>& nums, int start, 
                   vector<int>& current, vector<vector<int>>& result) {
        // Add current subset (even if empty)
        result.push_back(current);
        
        // Try adding each remaining element
        for (int i = start; i < nums.size(); i++) {
            // CHOOSE: add nums[i]
            current.push_back(nums[i]);
            
            // EXPLORE: recurse with next start
            backtrack(nums, i + 1, current, result);
            
            // UNCHOOSE: remove nums[i] (backtrack)
            current.pop_back();
        }
    }
};

// Time: O(2^n) - each element can be included or not
// Space: O(n) - recursion depth
```

**Visualization:**
```
nums = [1,2,3]

Decision tree:

                      []
           /          |          \
        [1]          [2]         [3]
       /   \          |
    [1,2] [1,3]     [2,3]
      |
   [1,2,3]

Execution trace:

Call backtrack([], start=0):
  Add [] to result
  Try i=0: add 1 → [1]
    Call backtrack([1], start=1):
      Add [1] to result
      Try i=1: add 2 → [1,2]
        Call backtrack([1,2], start=2):
          Add [1,2] to result
          Try i=2: add 3 → [1,2,3]
            Call backtrack([1,2,3], start=3):
              Add [1,2,3] to result
              No more elements, return
            Remove 3 → [1,2]
          No more elements, return
        Remove 2 → [1]
      Try i