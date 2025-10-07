# LeetCode Problems Organized by Patterns
## Pattern-Based Learning for Intuitive Surgical Interview Prep

---

## 🎯 Pattern 1: Two Pointers

**When to Use:** Array/string problems requiring linear scan with multiple positions, finding pairs/triplets, removing duplicates, reversing.

**Time Complexity:** Usually O(n) or O(n²)

### Problems:
1. **Two Sum II** - Medium | Two pointers on sorted array
2. **3Sum** - Medium ⭐ | Three pointers, skip duplicates
3. **Container With Most Water** - Medium | Max area between lines
4. **Trapping Rain Water** - Hard ⭐ | Calculate trapped water
5. **Remove Nth Node From End of List** - Medium | Fast/slow pointers
6. **Linked List Cycle II** - Medium | Fast/slow pointers (Floyd's)
7. **Reverse Linked List** - Easy | Two pointers
8. **Reorder List** - Medium | Find middle + reverse + merge
9. **Merge Two Sorted Lists** - Easy | Two pointers merge
10. **Intersection of Two Linked Lists** - Easy | Two pointers alignment

**Pattern Template:**
```cpp
// Opposite direction
int left = 0, right = n - 1;
while (left < right) {
    if (condition) left++;
    else right--;
}

// Same direction (fast/slow)
int slow = 0, fast = 0;
while (fast < n) {
    fast++;
    if (condition) slow++;
}
```

---

## 🎯 Pattern 2: Sliding Window

**When to Use:** Finding subarray/substring with specific property, min/max window, fixed/variable size windows.

**Time Complexity:** O(n)

### Problems:
1. **Longest Substring Without Repeating Characters** - Medium ⭐
2. **Minimum Window Substring** - Hard ⭐⭐
3. **Permutation in String** - Medium
4. **Find All Anagrams in a String** - Medium
5. **Longest Repeating Character Replacement** - Medium
6. **Maximum Erasure Value** - Medium
7. **Subarray Sum Equals K** - Medium | Hash map variant
8. **Sliding Window Maximum** - Hard ⭐ | Using deque

**Pattern Template:**
```cpp
// Variable size window
int left = 0;
for (int right = 0; right < n; right++) {
    // Expand window
    addToWindow(arr[right]);
    
    // Shrink window if needed
    while (windowInvalid()) {
        removeFromWindow(arr[left]);
        left++;
    }
    
    // Update result
    result = max(result, right - left + 1);
}

// Fixed size window
for (int i = 0; i < n; i++) {
    addToWindow(arr[i]);
    if (i >= k - 1) {
        processWindow();
        removeFromWindow(arr[i - k + 1]);
    }
}
```

---

## 🎯 Pattern 3: Fast & Slow Pointers (Floyd's Cycle Detection)

**When to Use:** Linked list cycle detection, finding middle element, cycle start.

**Time Complexity:** O(n), Space: O(1)

### Problems:
1. **Linked List Cycle II** - Medium | Find cycle start
2. **Reorder List** - Medium | Find middle
3. **Remove Nth Node From End of List** - Medium | N nodes apart

**Pattern Template:**
```cpp
ListNode* slow = head;
ListNode* fast = head;

while (fast && fast->next) {
    slow = slow->next;
    fast = fast->next->next;
    
    if (slow == fast) {
        // Cycle detected
        return true;
    }
}
return false;
```

---

## 🎯 Pattern 4: In-place Reversal of Linked List

**When to Use:** Reversing linked list or parts of it.

**Time Complexity:** O(n), Space: O(1)

### Problems:
1. **Reverse Linked List** - Easy
2. **Reverse Nodes in k-Group** - Hard

**Pattern Template:**
```cpp
ListNode* prev = nullptr;
ListNode* curr = head;

while (curr) {
    ListNode* next = curr->next;
    curr->next = prev;
    prev = curr;
    curr = next;
}
return prev;
```

---

## 🎯 Pattern 5: Merge Intervals

**When to Use:** Overlapping intervals, merging ranges, scheduling problems.

**Time Complexity:** O(n log n) for sorting + O(n) for merging

### Problems:
1. **Merge Two Sorted Lists** - Easy
2. **Merge K Sorted Lists** - Hard ⭐ | Using heap

**Pattern Template:**
```cpp
sort(intervals.begin(), intervals.end());
vector<Interval> result;

for (auto& interval : intervals) {
    if (result.empty() || result.back().end < interval.start) {
        result.push_back(interval);
    } else {
        result.back().end = max(result.back().end, interval.end);
    }
}
```

---

## 🎯 Pattern 6: Binary Search

**When to Use:** Sorted arrays, finding boundaries, optimization problems with monotonic search space.

**Time Complexity:** O(log n)

### Problems:
1. **Binary Search** - Easy
2. **Search in Rotated Sorted Array** - Medium ⭐
3. **Find Minimum in Rotated Sorted Array** - Medium
4. **Search a 2D Matrix** - Medium
5. **Find Peak Element** - Medium
6. **Median of Two Sorted Arrays** - Hard ⭐⭐
7. **Koko Eating Bananas** - Medium | Binary search on answer
8. **Time Based Key-Value Store** - Medium
9. **Find First and Last Position of Element in Sorted Array** - Medium

**Pattern Template:**
```cpp
// Standard binary search
int left = 0, right = n - 1;
while (left <= right) {
    int mid = left + (right - left) / 2;
    if (arr[mid] == target) return mid;
    if (arr[mid] < target) left = mid + 1;
    else right = mid - 1;
}

// Finding left boundary
while (left < right) {
    int mid = left + (right - left) / 2;
    if (condition(mid)) right = mid;
    else left = mid + 1;
}
```

---

## 🎯 Pattern 7: BFS (Breadth-First Search)

**When to Use:** Shortest path in unweighted graph, level-order traversal, exploring neighbors layer by layer.

**Time Complexity:** O(V + E)

### Problems:
1. **Binary Tree Level Order Traversal** - Medium ⭐
2. **Binary Tree Zigzag Level Order Traversal** - Medium
3. **Binary Tree Right Side View** - Medium
4. **Number of Islands** - Medium ⭐
5. **Rotting Oranges** - Medium ⭐
6. **Word Ladder** - Hard ⭐
7. **Word Ladder II** - Hard
8. **Shortest Path in Binary Matrix** - Medium
9. **Shortest Path in a Grid with Obstacles Elimination** - Hard ⭐⭐
10. **Clone Graph** - Medium
11. **Surrounded Regions** - Medium
12. **Robot Room Cleaner** - Hard ⭐⭐⭐ | Robotics specific

**Pattern Template:**
```cpp
queue<Node*> q;
q.push(start);
unordered_set<Node*> visited;
visited.insert(start);

while (!q.empty()) {
    int size = q.size();
    for (int i = 0; i < size; i++) {
        Node* node = q.front();
        q.pop();
        
        // Process node
        
        for (Node* neighbor : node->neighbors) {
            if (!visited.count(neighbor)) {
                visited.insert(neighbor);
                q.push(neighbor);
            }
        }
    }
}
```

---

## 🎯 Pattern 8: DFS (Depth-First Search)

**When to Use:** Exploring all paths, backtracking, cycle detection, topological sort.

**Time Complexity:** O(V + E)

### Problems:
1. **Binary Tree Inorder Traversal** - Easy
2. **Validate Binary Search Tree** - Medium ⭐
3. **Number of Islands** - Medium ⭐ | Can use BFS or DFS
4. **Course Schedule** - Medium ⭐ | Cycle detection
5. **Course Schedule II** - Medium | Topological sort
6. **Pacific Atlantic Water Flow** - Medium
7. **Word Search** - Medium
8. **All Paths From Source to Target** - Medium
9. **Clone Graph** - Medium | Can use BFS or DFS

**Pattern Template:**
```cpp
// Recursive DFS
void dfs(Node* node, unordered_set<Node*>& visited) {
    if (!node || visited.count(node)) return;
    
    visited.insert(node);
    // Process node
    
    for (Node* neighbor : node->neighbors) {
        dfs(neighbor, visited);
    }
}

// Iterative DFS
stack<Node*> s;
s.push(start);
unordered_set<Node*> visited;

while (!s.empty()) {
    Node* node = s.top();
    s.pop();
    
    if (visited.count(node)) continue;
    visited.insert(node);
    
    // Process node
    
    for (Node* neighbor : node->neighbors) {
        s.push(neighbor);
    }
}
```

---

## 🎯 Pattern 9: Backtracking

**When to Use:** Generate all solutions, permutations, combinations, constraint satisfaction, N-Queens, Sudoku.

**Time Complexity:** Often exponential O(2^n) or O(n!)

### Problems:
1. **Permutations** - Medium ⭐
2. **Subsets** - Medium ⭐
3. **Combination Sum** - Medium
4. **Generate Parentheses** - Medium
5. **Letter Combinations of a Phone Number** - Medium
6. **Word Search** - Medium
7. **Palindrome Partitioning** - Medium
8. **N-Queens** - Hard

**Pattern Template:**
```cpp
void backtrack(vector<int>& curr, vector<vector<int>>& result, 
               /* other params */) {
    // Base case
    if (isComplete(curr)) {
        result.push_back(curr);
        return;
    }
    
    // Try all candidates
    for (int candidate : candidates) {
        // Make choice
        curr.push_back(candidate);
        
        // Recurse
        backtrack(curr, result);
        
        // Undo choice (backtrack)
        curr.pop_back();
    }
}
```

---

## 🎯 Pattern 10: Dynamic Programming (1D)

**When to Use:** Optimal substructure, overlapping subproblems, counting ways, min/max optimization.

**Time Complexity:** Usually O(n) or O(n²)

### Problems:
1. **Climbing Stairs** - Easy | Classic DP
2. **House Robber** - Medium
3. **Coin Change** - Medium ⭐
4. **Jump Game** - Medium ⭐
5. **Word Break** - Medium ⭐
6. **Decode Ways** - Medium
7. **Maximum Product Subarray** - Medium
8. **Longest Increasing Subsequence** - Medium ⭐
9. **Best Time to Buy and Sell Stock with Cooldown** - Medium

**Pattern Template:**
```cpp
// Bottom-up
vector<int> dp(n + 1);
dp[0] = base_case;

for (int i = 1; i <= n; i++) {
    for (int j = 0; j < i; j++) {
        dp[i] = combine(dp[i], dp[j]);
    }
}

// Top-down with memoization
unordered_map<int, int> memo;
int solve(int n) {
    if (n <= base_case) return base_value;
    if (memo.count(n)) return memo[n];
    
    memo[n] = combine(solve(n-1), solve(n-2));
    return memo[n];
}
```

---

## 🎯 Pattern 11: Dynamic Programming (2D)

**When to Use:** Grid problems, two sequences (LCS, edit distance), matrix paths.

**Time Complexity:** O(m × n)

### Problems:
1. **Unique Paths** - Medium ⭐
2. **Unique Paths II** - Medium
3. **Minimum Path Sum** - Medium
4. **Longest Common Subsequence** - Medium
5. **Edit Distance** - Hard

**Pattern Template:**
```cpp
vector<vector<int>> dp(m, vector<int>(n));

// Initialize base cases
for (int i = 0; i < m; i++) dp[i][0] = init;
for (int j = 0; j < n; j++) dp[0][j] = init;

// Fill table
for (int i = 1; i < m; i++) {
    for (int j = 1; j < n; j++) {
        dp[i][j] = combine(dp[i-1][j], dp[i][j-1]);
    }
}
```

---

## 🎯 Pattern 12: Top K Elements (Heap)

**When to Use:** Finding K largest/smallest elements, priority-based problems, median in stream.

**Time Complexity:** O(n log k)

### Problems:
1. **Kth Largest Element in an Array** - Medium ⭐
2. **Top K Frequent Elements** - Medium ⭐
3. **Find Median from Data Stream** - Hard ⭐⭐
4. **Merge K Sorted Lists** - Hard ⭐
5. **Sliding Window Median** - Hard
6. **K Closest Points to Origin** - Medium ⭐⭐
7. **Task Scheduler** - Medium ⭐
8. **Reorganize String** - Medium

**Pattern Template:**
```cpp
// Min heap (top k largest)
priority_queue<int, vector<int>, greater<int>> minHeap;

for (int num : nums) {
    minHeap.push(num);
    if (minHeap.size() > k) {
        minHeap.pop();
    }
}

// Max heap (top k smallest)
priority_queue<int> maxHeap;

for (int num : nums) {
    maxHeap.push(num);
    if (maxHeap.size() > k) {
        maxHeap.pop();
    }
}
```

---

## 🎯 Pattern 13: Tree Traversal

**When to Use:** Processing all nodes, searching in trees, serialization.

**Time Complexity:** O(n)

### Problems:
1. **Binary Tree Inorder Traversal** - Easy
2. **Binary Tree Level Order Traversal** - Medium ⭐
3. **Binary Tree Zigzag Level Order Traversal** - Medium
4. **Construct Binary Tree from Preorder and Inorder Traversal** - Medium
5. **Validate Binary Search Tree** - Medium ⭐
6. **Lowest Common Ancestor of a Binary Tree** - Medium ⭐
7. **Binary Tree Right Side View** - Medium
8. **Kth Smallest Element in a BST** - Medium
9. **Count Complete Tree Nodes** - Medium
10. **Serialize and Deserialize Binary Tree** - Hard ⭐
11. **Binary Tree Maximum Path Sum** - Hard
12. **Inorder Successor in BST** - Medium

**Pattern Template:**
```cpp
// Inorder (Left, Root, Right)
void inorder(TreeNode* root) {
    if (!root) return;
    inorder(root->left);
    process(root);
    inorder(root->right);
}

// Preorder (Root, Left, Right)
void preorder(TreeNode* root) {
    if (!root) return;
    process(root);
    preorder(root->left);
    preorder(root->right);
}

// Postorder (Left, Right, Root)
void postorder(TreeNode* root) {
    if (!root) return;
    postorder(root->left);
    postorder(root->right);
    process(root);
}

// Level order (BFS)
queue<TreeNode*> q;
q.push(root);
while (!q.empty()) {
    int size = q.size();
    for (int i = 0; i < size; i++) {
        TreeNode* node = q.front();
        q.pop();
        process(node);
        if (node->left) q.push(node->left);
        if (node->right) q.push(node->right);
    }
}
```

---

## 🎯 Pattern 14: Union Find (Disjoint Set)

**When to Use:** Connected components, cycle detection in undirected graphs, dynamic connectivity.

**Time Complexity:** Nearly O(1) per operation with path compression

### Problems:
1. **Number of Islands** - Medium ⭐ | Can also use BFS/DFS
2. **Accounts Merge** - Medium

**Pattern Template:**
```cpp
class UnionFind {
    vector<int> parent, rank;
public:
    UnionFind(int n) : parent(n), rank(n, 0) {
        iota(parent.begin(), parent.end(), 0);
    }
    
    int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]);  // Path compression
        }
        return parent[x];
    }
    
    bool unite(int x, int y) {
        int px = find(x), py = find(y);
        if (px == py) return false;
        
        // Union by rank
        if (rank[px] < rank[py]) swap(px, py);
        parent[py] = px;
        if (rank[px] == rank[py]) rank[px]++;
        return true;
    }
};
```

---

## 🎯 Pattern 15: Topological Sort

**When to Use:** Ordering with dependencies, course prerequisites, build systems.

**Time Complexity:** O(V + E)

### Problems:
1. **Course Schedule** - Medium ⭐
2. **Course Schedule II** - Medium

**Pattern Template:**
```cpp
// Kahn's Algorithm (BFS)
vector<int> topologicalSort(int n, vector<vector<int>>& edges) {
    vector<int> inDegree(n, 0);
    vector<vector<int>> graph(n);
    
    // Build graph
    for (auto& edge : edges) {
        graph[edge[0]].push_back(edge[1]);
        inDegree[edge[1]]++;
    }
    
    // Start with nodes having no incoming edges
    queue<int> q;
    for (int i = 0; i < n; i++) {
        if (inDegree[i] == 0) q.push(i);
    }
    
    vector<int> result;
    while (!q.empty()) {
        int node = q.front();
        q.pop();
        result.push_back(node);
        
        for (int neighbor : graph[node]) {
            if (--inDegree[neighbor] == 0) {
                q.push(neighbor);
            }
        }
    }
    
    return result.size() == n ? result : vector<int>{};
}
```

---

## 🎯 Pattern 16: Modified Binary Search

**When to Use:** Rotated arrays, finding in matrix, search space optimization.

**Time Complexity:** O(log n)

### Problems:
1. **Search in Rotated Sorted Array** - Medium ⭐
2. **Find Minimum in Rotated Sorted Array** - Medium
3. **Search a 2D Matrix** - Medium
4. **Find Peak Element** - Medium

**Pattern Template:**
```cpp
// Rotated array search
int left = 0, right = n - 1;
while (left <= right) {
    int mid = left + (right - left) / 2;
    
    if (arr[mid] == target) return mid;
    
    // Determine which half is sorted
    if (arr[left] <= arr[mid]) {  // Left half sorted
        if (target >= arr[left] && target < arr[mid]) {
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    } else {  // Right half sorted
        if (target > arr[mid] && target <= arr[right]) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
}
```

---

## 🎯 Pattern 17: Hash Map / Set

**When to Use:** O(1) lookup, counting frequency, finding duplicates, two sum variants.

**Time Complexity:** O(n)

### Problems:
1. **Group Anagrams** - Medium
2. **Subarray Sum Equals K** - Medium
3. **Longest Substring Without Repeating Characters** - Medium ⭐
4. **Top K Frequent Elements** - Medium ⭐

**Pattern Template:**
```cpp
// Frequency counting
unordered_map<int, int> freq;
for (int num : nums) {
    freq[num]++;
}

// Two sum pattern
unordered_map<int, int> seen;
for (int i = 0; i < n; i++) {
    int complement = target - nums[i];
    if (seen.count(complement)) {
        return {seen[complement], i};
    }
    seen[nums[i]] = i;
}
```

---

## 🎯 Pattern 18: Monotonic Stack

**When to Use:** Next greater/smaller element, temperature problems, histogram problems.

**Time Complexity:** O(n)

### Problems:
1. **Daily Temperatures** - Medium
2. **Next Greater Element II** - Medium
3. **Largest Rectangle in Histogram** - Hard

**Pattern Template:**
```cpp
// Next greater element
stack<int> st;  // Indices
vector<int> result(n, -1);

for (int i = 0; i < n; i++) {
    while (!st.empty() && arr[st.top()] < arr[i]) {
        result[st.top()] = arr[i];
        st.pop();
    }
    st.push(i);
}
```

---

## 🎯 Pattern 19: Stack for String/Expression Problems

**When to Use:** Valid parentheses, calculator, decode strings, expression evaluation.

**Time Complexity:** O(n)

### Problems:
1. **Valid Parentheses** - Easy
2. **Evaluate Reverse Polish Notation** - Medium
3. **Decode String** - Medium ⭐
4. **String to Integer (atoi)** - Medium

**Pattern Template:**
```cpp
stack<char> st;
for (char c : s) {
    if (isOpen(c)) {
        st.push(c);
    } else if (isClose(c)) {
        if (st.empty() || !matches(st.top(), c)) {
            return false;
        }
        st.pop();
    }
}
return st.empty();
```

---

## 🎯 Pattern 20: Matrix Traversal (Robotics)

**When to Use:** Robot navigation, grid path problems, spiral traversal, coordinate transformations.

**Time Complexity:** O(m × n)

### Problems:
1. **Robot Return to Origin** - Easy
2. **Walking Robot Simulation** - Medium ⭐⭐⭐
3. **Spiral Matrix** - Medium
4. **Rotate Image** - Medium
5. **Set Matrix Zeroes** - Medium
6. **Robot Room Cleaner** - Hard ⭐⭐⭐

**Pattern Template:**
```cpp
// 4-directional movement
int dirs[4][2] = {{0,1}, {1,0}, {0,-1}, {-1,0}};  // Right, Down, Left, Up
int x = 0, y = 0, dir = 0;

// Move forward
x += dirs[dir][0];
y += dirs[dir][1];

// Turn right
dir = (dir + 1) % 4;

// Turn left
dir = (dir + 3) % 4;

// Check bounds
bool isValid(int x, int y, int m, int n) {
    return x >= 0 && x < m && y >= 0 && y < n;
}
```

---

## 🎯 Pattern 21: Design Problems (OOP)

**When to Use:** System design, data structure implementation, cache, API design.

**Time Complexity:** Varies by operation

### Problems:
1. **LRU Cache** - Medium ⭐⭐⭐
2. **Design HashMap** - Easy
3. **Min Stack** - Medium ⭐
4. **Implement Trie (Prefix Tree)** - Medium ⭐
5. **Design Hit Counter** - Medium
6. **Design Circular Queue** - Medium
7. **Design Tic-Tac-Toe** - Medium
8. **Design Search Autocomplete System** - Hard
9. **Implement Queue using Stacks** - Easy
10. **Time Based Key-Value Store** - Medium

**Pattern Template:**
```cpp
// LRU Cache pattern (list + hash map)
class LRUCache {
    int capacity;
    list<pair<int, int>> cache;  // key, value pairs
    unordered_map<int, list<pair<int, int>>::iterator> map;
    
public:
    LRUCache(int capacity) : capacity(capacity) {}
    
    int get(int key) {
        if (!map.count(key)) return -1;
        
        // Move to front
        cache.splice(cache.begin(), cache, map[key]);
        return map[key]->second;
    }
    
    void put(int key, int value) {
        if (map.count(key)) {
            cache.erase(map[key]);
        } else if (cache.size() == capacity) {
            map.erase(cache.back().first);
            cache.pop_back();
        }
        
        cache.push_front({key, value});
        map[key] = cache.begin();
    }
};
```

---

## 🎯 Pattern 22: Greedy Algorithms

**When to Use:** Optimal choice at each step leads to global optimum, interval scheduling, jump game.

**Time Complexity:** Usually O(n) or O(n log n)

### Problems:
1. **Jump Game** - Medium ⭐
2. **Task Scheduler** - Medium ⭐
3. **Reorganize String** - Medium

**Pattern Template:**
```cpp
// Greedy choice pattern
int result = 0;
sort(arr.begin(), arr.end());  // Often need sorting

for (int i = 0; i < n; i++) {
    if (canTake(arr[i])) {
        result += arr[i];
        update();
    }
}
```

---

## 🎯 Pattern 23: Bit Manipulation

**When to Use:** XOR tricks, single number, subsets generation, bit counting.

**Time Complexity:** O(n) or O(1)

### Problems:
1. **Subsets** - Medium ⭐ | Can use backtracking or bit manipulation

**Pattern Template:**
```cpp
// Generate all subsets
for (int mask = 0; mask < (1 << n); mask++) {
    vector<int> subset;
    for (int i = 0; i < n; i++) {
        if (mask & (1 << i)) {
            subset.push_back(nums[i]);
        }
    }
    result.push_back(subset);
}
```

---

## 🎯 Pattern 24: String Manipulation

**When to Use:** Parsing, pattern matching, anagrams, palindromes.

**Time Complexity:** O(n)

### Problems:
1. **Group Anagrams** - Medium
2. **Longest Palindromic Substring** - Medium
3. **String to Integer (atoi)** - Medium
4. **Implement strStr()** - Medium

**Pattern Template:**
```cpp
// Anagram detection
string getSignature(const string& s) {
    string sorted = s;
    sort(sorted.begin(), sorted.end());
    return sorted;
}

// Or use frequency array
vector<int> getFreq(const string& s) {
    vector<int> freq(26, 0);
    for (char c : s) {
        freq[c - 'a']++;
    }
    return freq;
}
```

---

## 🎯 Pattern 25: Array Manipulation

**When to Use:** In-place algorithms, prefix sum, product calculations.

**Time Complexity:** O(n)

### Problems:
1. **Product of Array Except Self** - Medium ⭐
2. **Next Permutation** - Medium
3. **Rotate Image** - Medium
4. **Set Matrix Zeroes** - Medium

**Pattern Template:**
```cpp
// Prefix/suffix product pattern
vector<int> prefix(n), suffix(n);

prefix[0] = 1;
for (int i = 1; i < n; i++) {
    prefix[i] = prefix[i-1] * nums[i-1];
}

suffix[n-1] = 1;
for (int i = n-2; i >= 0; i--) {
    suffix[i] = suffix[i+1] * nums[i+1];
}
```

---

## 📊 Quick Pattern Recognition Guide

| Problem Keywords | Likely Pattern |
|------------------|----------------|
| Subarray, substring | Sliding Window |
| Two sum, pair finding | Two Pointers, Hash Map |
| Sorted array | Binary Search |
| Tree | DFS, BFS, Recursion |
| Graph, islands | BFS, DFS, Union Find |
| Shortest path | BFS, Dijkstra, DP |
| Permutations, combinations | Backtracking |
| Optimization (min/max) | DP, Greedy |
| Top K elements | Heap |
| Next greater/smaller | Monotonic Stack |
| Valid parentheses | Stack |
| Linked list cycle | Fast/Slow Pointers |
| Robot navigation | Matrix Traversal, BFS |

---

## 🎯 Pattern Priority for Intuitive Interview

**Must Master (⭐⭐⭐):**
1. Two Pointers
2. Sliding Window
3. Binary Search
4. BFS/DFS
5. Dynamic Programming (1D & 2D)
6. Heap/Priority Queue
7. Hash Map
8. Matrix Traversal (Robotics!)

**Important (⭐⭐):**
9. Backtracking
10. Tree Traversal
11. Stack (Monotonic & Expression)
12. Fast/Slow Pointers
13. Design Problems

**Good to Know (⭐):**
14. Union Find
15. Topological Sort
16. Greedy
17. Bit Manipulation

---

## 📝 Pattern Practice Strategy

### Week 1-2: Foundation Patterns
- Two Pointers (5 problems)
- Sliding Window (5 problems)
- Binary Search (5 problems)
- Hash Map (5 problems)

### Week 3: Graph & Tree Patterns
- BFS (6 problems)
- DFS (6 problems)
- Tree Traversal (6 problems)

### Week 4: Advanced Patterns
- Dynamic Programming (8 problems)
- Heap/Priority Queue (6 problems)
- Backtracking (4 problems)

### Week 5: Robotics & Design
- Matrix Traversal (6 problems)
- Design Problems (6 problems)
- Stack patterns (4 problems)

### Week 6: Integration & Mock Interviews
- Mixed pattern problems
- Timed practice
- Mock interviews

**Pro Tip:** After solving each problem, identify which pattern(s) it uses. Many problems combine multiple patterns!