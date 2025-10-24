# BFS & DFS Pattern Cheatsheet
## Graph & Tree Traversal

---

## 🎯 BFS vs DFS: When to Use What?

### Use BFS When:
- ✅ Finding **shortest path** (unweighted graph)
- ✅ **Level-by-level** traversal needed
- ✅ Nearest/closest problems
- ✅ Keywords: "shortest", "minimum steps", "level order"
- ✅ **Robotic path planning** (shortest route to target)

### Use DFS When:
- ✅ Exploring **all paths**
- ✅ **Cycle detection**
- ✅ **Topological sorting**
- ✅ **Connected components**
- ✅ Backtracking problems
- ✅ Tree traversals (inorder, preorder, postorder)

---

# Part 1: BFS (Breadth-First Search)

## 🎯 Core Concept

**BFS explores level by level, like ripples in water.**

```
Starting from node A:
     A           Level 0
   / | \
  B  C  D        Level 1
 / \   / \
E   F G   H      Level 2

BFS order: A → B,C,D → E,F,G,H
```

## ⏱️ Time Complexity: O(V + E)
- V = vertices, E = edges
- Visit each vertex once, explore each edge once

---

## Template 1: Basic BFS (Graph/Tree)

```cpp
void bfs(Node* start) {
    queue<Node*> q;
    unordered_set<Node*> visited;
    
    // Initialize
    q.push(start);
    visited.insert(start);
    
    while (!q.empty()) {
        Node* node = q.front();
        q.pop();
        
        // Process current node
        process(node);
        
        // Add unvisited neighbors
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

## Template 2: Level-Order BFS (Track Levels)

```cpp
vector<vector<int>> levelOrder(Node* root) {
    if (!root) return {};
    
    vector<vector<int>> result;
    queue<Node*> q;
    q.push(root);
    
    while (!q.empty()) {
        int levelSize = q.size();  // KEY: Capture current level size
        vector<int> currentLevel;
        
        // Process all nodes in current level
        for (int i = 0; i < levelSize; i++) {
            Node* node = q.front();
            q.pop();
            
            currentLevel.push_back(node->val);
            
            // Add next level nodes
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        
        result.push_back(currentLevel);
    }
    
    return result;
}
```

---

## Template 3: BFS with Distance Tracking

```cpp
int bfsShortestPath(Node* start, Node* target) {
    queue<Node*> q;
    unordered_map<Node*, int> distance;
    
    q.push(start);
    distance[start] = 0;
    
    while (!q.empty()) {
        Node* node = q.front();
        q.pop();
        
        if (node == target) {
            return distance[node];
        }
        
        for (Node* neighbor : node->neighbors) {
            if (!distance.count(neighbor)) {
                distance[neighbor] = distance[node] + 1;
                q.push(neighbor);
            }
        }
    }
    
    return -1;  // Target not reachable
}
```

---

## Template 4: Grid BFS (4-directional)

```cpp
int bfsGrid(vector<vector<int>>& grid) {
    int m = grid.size(), n = grid[0].size();
    vector<vector<bool>> visited(m, vector<bool>(n, false));
    
    // 4 directions: right, down, left, up
    int dirs[4][2] = {{0,1}, {1,0}, {0,-1}, {-1,0}};
    
    queue<pair<int,int>> q;
    q.push({0, 0});  // Start at top-left
    visited[0][0] = true;
    
    int steps = 0;
    
    while (!q.empty()) {
        int size = q.size();
        
        for (int i = 0; i < size; i++) {
            auto [x, y] = q.front();
            q.pop();
            
            // Check if reached target
            if (x == m-1 && y == n-1) {
                return steps;
            }
            
            // Explore 4 directions
            for (auto& dir : dirs) {
                int nx = x + dir[0];
                int ny = y + dir[1];
                
                // Check bounds and visited
                if (nx >= 0 && nx < m && ny >= 0 && ny < n 
                    && !visited[nx][ny] && grid[nx][ny] != 0) {
                    visited[nx][ny] = true;
                    q.push({nx, ny});
                }
            }
        }
        
        steps++;
    }
    
    return -1;  // No path found
}
```

---

## 📚 BFS Problems with Detailed Solutions

### Problem 1: Binary Tree Level Order Traversal ⭐
**LeetCode 102 - Medium**

**Description:** Given the root of a binary tree, return the level order traversal of its nodes' values (i.e., from left to right, level by level).

**Example:**
```
Input: root = [3,9,20,null,null,15,7]

    3
   / \
  9   20
     /  \
    15   7

Output: [[3],[9,20],[15,7]]
```

**Solution:**
```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> result;
    if (!root) return result;
    
    queue<TreeNode*> q;
    q.push(root);
    
    while (!q.empty()) {
        // CRITICAL: Capture size BEFORE processing
        // This is how we know how many nodes are in current level
        int levelSize = q.size();
        vector<int> currentLevel;
        
        // Process exactly levelSize nodes (all nodes in current level)
        for (int i = 0; i < levelSize; i++) {
            TreeNode* node = q.front();
            q.pop();
            
            // Add to current level
            currentLevel.push_back(node->val);
            
            // Queue children for next level
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        
        result.push_back(currentLevel);
    }
    
    return result;
}

// Time: O(n) - visit each node once
// Space: O(w) - w is max width of tree (queue size)
```

**Visualization:**
```
    3
   / \
  9   20
     /  \
    15   7

Step 1: Queue = [3]
  levelSize = 1
  Process: 3 → currentLevel = [3]
  Queue children: Queue = [9, 20]
  Result: [[3]]

Step 2: Queue = [9, 20]
  levelSize = 2
  Process: 9 → currentLevel = [9]
  Process: 20 → currentLevel = [9, 20]
  Queue children: Queue = [15, 7]
  Result: [[3], [9,20]]

Step 3: Queue = [15, 7]
  levelSize = 2
  Process: 15 → currentLevel = [15]
  Process: 7 → currentLevel = [15, 7]
  No children
  Result: [[3], [9,20], [15,7]]
```

**Key Insight:**
```cpp
// WHY capture size first?
int size = q.size();  // Size BEFORE we start adding next level

// If we did this:
while (!q.empty()) {
    for (int i = 0; i < q.size(); i++) {  // WRONG!
        // q.size() changes as we add children!
    }
}
```

---

### Problem 2: Rotting Oranges ⭐ (Multi-Source BFS)
**LeetCode 994 - Medium**

**Description:** You are given an m x n grid where each cell can have one of three values:
- 0: empty cell
- 1: fresh orange
- 2: rotten orange

Every minute, any fresh orange that is 4-directionally adjacent to a rotten orange becomes rotten. Return the minimum number of minutes until no cell has a fresh orange. If impossible, return -1.

**Example:**
```
Input: grid = [[2,1,1],
               [1,1,0],
               [0,1,1]]
Output: 4

Minute 0:        Minute 1:        Minute 2:
2 1 1            2 2 1            2 2 2
1 1 0            2 1 0            2 2 0
0 1 1            0 1 1            0 1 1

Minute 3:        Minute 4:
2 2 2            2 2 2
2 2 0            2 2 0
0 2 1            0 2 2
```

**Solution:**
```cpp
int orangesRotting(vector<vector<int>>& grid) {
    int m = grid.size(), n = grid[0].size();
    queue<pair<int, int>> q;
    int freshCount = 0;
    
    // STEP 1: Count fresh oranges and queue all rotten oranges
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == 2) {
                q.push({i, j});  // Multi-source BFS: start from ALL rotten
            } else if (grid[i][j] == 1) {
                freshCount++;
            }
        }
    }
    
    // Edge case: no fresh oranges
    if (freshCount == 0) return 0;
    
    // 4 directions
    int dirs[4][2] = {{0,1}, {1,0}, {0,-1}, {-1,0}};
    int minutes = 0;
    
    // STEP 2: BFS to spread rot
    while (!q.empty()) {
        int size = q.size();
        bool rottedThisMinute = false;
        
        for (int i = 0; i < size; i++) {
            auto [x, y] = q.front();
            q.pop();
            
            // Check all 4 neighbors
            for (auto& dir : dirs) {
                int nx = x + dir[0];
                int ny = y + dir[1];
                
                // If neighbor is fresh orange
                if (nx >= 0 && nx < m && ny >= 0 && ny < n 
                    && grid[nx][ny] == 1) {
                    // Make it rotten
                    grid[nx][ny] = 2;
                    q.push({nx, ny});
                    freshCount--;
                    rottedThisMinute = true;
                }
            }
        }
        
        // Only increment time if we actually rotted something
        if (rottedThisMinute) {
            minutes++;
        }
    }
    
    // If there are still fresh oranges, impossible
    return freshCount == 0 ? minutes : -1;
}

// Time: O(m × n) - visit each cell at most once
// Space: O(m × n) - queue can hold all cells
```

**Visualization:**
```
Initial: 2 1 1      Queue = [(0,0)]
         1 1 0      Fresh = 5
         0 1 1

Minute 1:
  Process (0,0):
    Check right (0,1): fresh! → rot it, add to queue
    Check down (1,0): fresh! → rot it, add to queue
    
  Grid: 2 2 1       Queue = [(0,1), (1,0)]
        2 1 0       Fresh = 3
        0 1 1

Minute 2:
  Process (0,1):
    Check right (0,2): fresh! → rot it
  Process (1,0):
    Check right (1,1): fresh! → rot it
    
  Grid: 2 2 2       Queue = [(0,2), (1,1)]
        2 2 0       Fresh = 1
        0 1 1

Continue until all fresh are gone...
```

**Why Multi-Source BFS?**
```
Single source BFS: Start from one point, expand outward
Multi-source BFS: Start from MULTIPLE points simultaneously

Perfect for:
- Rotting oranges (multiple rotten start points)
- Forest fire spreading from multiple fires
- Flood fill from multiple sources
- Robot coverage from multiple robots

Technique: Add ALL starting points to queue initially
```

---

### Problem 3: Word Ladder ⭐
**LeetCode 127 - Hard**

**Description:** Given two words, `beginWord` and `endWord`, and a dictionary `wordList`, return the length of shortest transformation sequence from `beginWord` to `endWord`, such that:
- Only one letter can be changed at a time
- Each transformed word must exist in `wordList`

**Example:**
```
Input: beginWord = "hit", endWord = "cog"
       wordList = ["hot","dot","dog","lot","log","cog"]
Output: 5
Explanation: "hit" → "hot" → "dot" → "dog" → "cog"

Input: beginWord = "hit", endWord = "cog"
       wordList = ["hot","dot","dog","lot","log"]
Output: 0
Explanation: endWord "cog" not in wordList
```

**Solution:**
```cpp
int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
    // Convert to set for O(1) lookup
    unordered_set<string> dict(wordList.begin(), wordList.end());
    
    // Check if endWord exists in dictionary
    if (!dict.count(endWord)) return 0;
    
    queue<string> q;
    q.push(beginWord);
    
    int steps = 1;  // Count includes beginWord
    
    while (!q.empty()) {
        int size = q.size();
        
        for (int i = 0; i < size; i++) {
            string word = q.front();
            q.pop();
            
            // Check if we reached endWord
            if (word == endWord) {
                return steps;
            }
            
            // Try changing each character
            for (int j = 0; j < word.length(); j++) {
                char original = word[j];
                
                // Try all 26 letters
                for (char c = 'a'; c <= 'z'; c++) {
                    if (c == original) continue;
                    
                    word[j] = c;
                    
                    // If this word is in dictionary
                    if (dict.count(word)) {
                        q.push(word);
                        dict.erase(word);  // Mark as visited by removing
                    }
                }
                
                word[j] = original;  // Restore original character
            }
        }
        
        steps++;
    }
    
    return 0;  // No path found
}

// Time: O(n × L × 26) where n = wordList size, L = word length
// Space: O(n) for queue and set
```

**Visualization:**
```
beginWord = "hit", endWord = "cog"
wordList = ["hot","dot","dog","lot","log","cog"]

Step 1: Queue = ["hit"]
  Try changing each char of "hit":
    "ait", "bit", ... "hot" ✓ (in dict)
  Queue = ["hot"], steps = 1

Step 2: Queue = ["hot"]
  Try changing "hot":
    "dot" ✓, "lot" ✓
  Queue = ["dot", "lot"], steps = 2

Step 3: Queue = ["dot", "lot"]
  From "dot": → "dog" ✓
  From "lot": → "log" ✓
  Queue = ["dog", "log"], steps = 3

Step 4: Queue = ["dog", "log"]
  From "dog": → "cog" ✓ FOUND!
  Return steps = 4... wait, include begin word
  
Actually: hit(1) → hot(2) → dot(3) → dog(4) → cog(5)
Return 5
```

**Optimization - Bidirectional BFS:**
```cpp
// Start BFS from both ends simultaneously
// When they meet, path found
// Much faster for long paths

int ladderLengthBidirectional(string beginWord, string endWord, 
                               vector<string>& wordList) {
    unordered_set<string> dict(wordList.begin(), wordList.end());
    if (!dict.count(endWord)) return 0;
    
    unordered_set<string> beginSet{beginWord};
    unordered_set<string> endSet{endWord};
    
    int steps = 1;
    
    while (!beginSet.empty() && !endSet.empty()) {
        // Always expand smaller set (optimization)
        if (beginSet.size() > endSet.size()) {
            swap(beginSet, endSet);
        }
        
        unordered_set<string> nextLevel;
        
        for (const string& word : beginSet) {
            string temp = word;
            
            for (int i = 0; i < word.length(); i++) {
                char original = temp[i];
                
                for (char c = 'a'; c <= 'z'; c++) {
                    temp[i] = c;
                    
                    // If other set has this word, paths met!
                    if (endSet.count(temp)) {
                        return steps + 1;
                    }
                    
                    if (dict.count(temp)) {
                        nextLevel.insert(temp);
                        dict.erase(temp);
                    }
                }
                
                temp[i] = original;
            }
        }
        
        beginSet = nextLevel;
        steps++;
    }
    
    return 0;
}
```

---

### Problem 4: Shortest Path in Binary Matrix
**LeetCode 1091 - Medium**

**Description:** Given an n x n binary matrix `grid`, return the length of the shortest clear path in the matrix. A clear path starts from top-left cell (0,0) to bottom-right cell (n-1,n-1) with all cells in path being 0. Can move in **8 directions**.

**Example:**
```
Input: grid = [[0,1],
               [1,0]]
Output: 2
Path: (0,0) → (1,1)

Input: grid = [[0,0,0],
               [1,1,0],
               [1,1,0]]
Output: 4
Path: (0,0) → (0,1) → (0,2) → (1,2) → (2,2)
```

**Solution:**
```cpp
int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
    int n = grid.size();
    
    // Check if start or end is blocked
    if (grid[0][0] == 1 || grid[n-1][n-1] == 1) {
        return -1;
    }
    
    // Edge case: 1x1 grid
    if (n == 1) return 1;
    
    // 8 directions: right, down, left, up, and 4 diagonals
    int dirs[8][2] = {
        {0,1}, {1,0}, {0,-1}, {-1,0},      // 4-directional
        {1,1}, {1,-1}, {-1,1}, {-1,-1}     // diagonals
    };
    
    queue<pair<int, int>> q;
    q.push({0, 0});
    grid[0][0] = 1;  // Mark as visited (reuse grid)
    
    int pathLength = 1;
    
    while (!q.empty()) {
        int size = q.size();
        
        for (int i = 0; i < size; i++) {
            auto [x, y] = q.front();
            q.pop();
            
            // Check if reached destination
            if (x == n-1 && y == n-1) {
                return pathLength;
            }
            
            // Explore 8 directions
            for (auto& dir : dirs) {
                int nx = x + dir[0];
                int ny = y + dir[1];
                
                // Check bounds and if cell is clear (0)
                if (nx >= 0 && nx < n && ny >= 0 && ny < n 
                    && grid[nx][ny] == 0) {
                    grid[nx][ny] = 1;  // Mark visited
                    q.push({nx, ny});
                }
            }
        }
        
        pathLength++;
    }
    
    return -1;  // No path found
}

// Time: O(n²) - visit each cell at most once
// Space: O(n²) - queue can hold all cells
```

**Visualization:**
```
Grid: 0 0 0    Start: (0,0), End: (2,2)
      1 1 0
      1 1 0

Step 1: pathLength = 1
  Queue = [(0,0)]
  Mark (0,0) as visited
  
  Process (0,0):
    8 directions → can reach (0,1) and (1,1) [diagonal blocked by 1]
    Queue = [(0,1)]  // Only (0,1) is 0
    
  Grid: 1 0 0
        1 1 0
        1 1 0

Step 2: pathLength = 2
  Process (0,1):
    Can reach (0,2)
    Queue = [(0,2)]
    
  Grid: 1 1 0
        1 1 0
        1 1 0

Step 3: pathLength = 3
  Process (0,2):
    Can reach (1,2)
    Queue = [(1,2)]
    
  Grid: 1 1 1
        1 1 0
        1 1 0

Step 4: pathLength = 4
  Process (1,2):
    Can reach (2,2) - DESTINATION!
    Return 4
```

---

# Part 2: DFS (Depth-First Search)

## 🎯 Core Concept

**DFS explores as far as possible along each branch before backtracking.**

```
Starting from node A:
     A          
   / | \
  B  C  D       
 / \   / \
E   F G   H     

DFS order (preorder): A → B → E → F → C → D → G → H
Goes deep before going wide
```

## ⏱️ Time Complexity: O(V + E)

---

## Template 1: Recursive DFS (Most Common)

```cpp
void dfs(Node* node, unordered_set<Node*>& visited) {
    // Base case
    if (!node || visited.count(node)) {
        return;
    }
    
    // Mark as visited
    visited.insert(node);
    
    // Process current node
    process(node);
    
    // Recurse on neighbors
    for (Node* neighbor : node->neighbors) {
        dfs(neighbor, visited);
    }
}
```

---

## Template 2: Iterative DFS (Using Stack)

```cpp
void dfsIterative(Node* start) {
    stack<Node*> s;
    unordered_set<Node*> visited;
    
    s.push(start);
    
    while (!s.empty()) {
        Node* node = s.top();
        s.pop();
        
        if (visited.count(node)) continue;
        
        visited.insert(node);
        process(node);
        
        // Add neighbors to stack
        for (Node* neighbor : node->neighbors) {
            if (!visited.count(neighbor)) {
                s.push(neighbor);
            }
        }
    }
}
```

---

## Template 3: DFS for Trees (Inorder/Preorder/Postorder)

```cpp
// Preorder: Root → Left → Right
void preorder(TreeNode* root) {
    if (!root) return;
    process(root);           // Process root first
    preorder(root->left);
    preorder(root->right);
}

// Inorder: Left → Root → Right (for BST: sorted order)
void inorder(TreeNode* root) {
    if (!root) return;
    inorder(root->left);
    process(root);           // Process root in middle
    inorder(root->right);
}

// Postorder: Left → Right → Root
void postorder(TreeNode* root) {
    if (!root) return;
    postorder(root->left);
    postorder(root->right);
    process(root);           // Process root last
}
```

---

## Template 4: DFS with Backtracking

```cpp
void dfsBacktrack(vector<int>& path, int start) {
    // Base case: found valid solution
    if (isComplete(path)) {
        result.push_back(path);
        return;
    }
    
    // Try all possibilities
    for (int choice : getChoices(start)) {
        // Make choice
        path.push_back(choice);
        
        // Recurse with choice
        dfsBacktrack(path, start + 1);
        
        // Undo choice (backtrack)
        path.pop_back();
    }
}
```

---

## 📚 DFS Problems with Detailed Solutions

### Problem 1: Number of Islands ⭐
**LeetCode 200 - Medium**

**Description:** Given an m x n 2D binary grid which represents a map of '1's (land) and '0's (water), return the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically.

**Example:**
```
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1

Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3
```

**Solution (DFS):**
```cpp
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        if (grid.empty()) return 0;
        
        int m = grid.size();
        int n = grid[0].size();
        int islands = 0;
        
        // Scan entire grid
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    // Found new island! Start DFS to mark entire island
                    islands++;
                    dfs(grid, i, j);
                }
            }
        }
        
        return islands;
    }
    
private:
    void dfs(vector<vector<char>>& grid, int i, int j) {
        int m = grid.size();
        int n = grid[0].size();
        
        // Base cases: out of bounds or water or already visited
        if (i < 0 || i >= m || j < 0 || j >= n || grid[i][j] != '1') {
            return;
        }
        
        // Mark as visited by changing to '0'
        grid[i][j] = '0';
        
        // Explore 4 directions
        dfs(grid, i + 1, j);  // Down
        dfs(grid, i - 1, j);  // Up
        dfs(grid, i, j + 1);  // Right
        dfs(grid, i, j - 1);  // Left
    }
};

// Time: O(m × n) - visit each cell once
// Space: O(m × n) - recursion stack in worst case (entire grid is one island)
```

**Visualization:**
```
Grid: 1 1 0
      1 0 0
      0 0 1

Scan from top-left:

(0,0) = '1' → Found island #1!
  DFS from (0,0):
    Mark (0,0) as '0'
    Try down (1,0): '1' → recurse
      Mark (1,0) as '0'
      Try down (2,0): '0' → return
      Try up (0,0): '0' → return
      Try right (1,1): '0' → return
      Try left: out of bounds → return
    Try up: out of bounds
    Try right (0,1): '1' → recurse
      Mark (0,1) as '0'
      ... continue DFS
    Try left: out of bounds
    
  Grid after DFS:
  0 0 0
  0 0 0
  0 0 1

Continue scanning...
(2,2) = '1' → Found island #2!
  DFS marks it as '0'

Result: 2 islands
```

**BFS Alternative:**
```cpp
void bfs(vector<vector<char>>& grid, int i, int j) {
    queue<pair<int, int>> q;
    q.push({i, j});
    grid[i][j] = '0';
    
    int dirs[4][2] = {{0,1}, {1,0}, {0,-1}, {-1,0}};
    
    while (!q.empty()) {
        auto [x, y] = q.front();
        q.pop();
        
        for (auto& dir : dirs) {
            int nx = x + dir[0];
            int ny = y + dir[1];
            
            if (nx >= 0 && nx < grid.size() && 
                ny >= 0 && ny < grid[0].size() && 
                grid[nx][ny] == '1') {
                grid[nx][ny] = '0';
                q.push({nx, ny});
            }
        }
    }
}

// BFS and DFS both work - DFS is simpler for this problem
```

---

### Problem 2: Course Schedule (Cycle Detection) ⭐
**LeetCode 207 - Medium**

**Description:** There are `numCourses` courses labeled from 0 to n-1. You are given an array `prerequisites` where `prerequisites[i] = [ai, bi]` indicates you must take course `bi` before course `ai`. Return true if you can finish all courses.

**Example:**
```
Input: numCourses = 2, prerequisites = [[1,0]]
Output: true
Explanation: Take course 0, then course 1

Input: numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: false
Explanation: Circular dependency! 0 requires 1, 1 requires 0
```

**Solution (DFS Cycle Detection):**
```cpp
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        // Build adjacency list
        vector<vector<int>> graph(numCourses);
        for (auto& prereq : prerequisites) {
            int course = prereq[0];
            int prerequisite = prereq[1];
            graph[prerequisite].push_back(course);
        }
        
        // 0 = unvisited, 1 = visiting (in current path), 2 = visited
        vector<int> state(numCourses, 0);
        
        // Check each course
        for (int i = 0; i < numCourses; i++) {
            if (hasCycle(graph, state, i)) {
                return false;  // Cycle detected!
            }
        }
        
        return true;  // No cycles, can finish all courses
    }
    
private:
    bool hasCycle(vector<vector<int>>& graph, vector<int>& state, int course) {
        // If in current DFS path, we found a cycle!
        if (state[course] == 1) {
            return true;
        }
        
        // If already visited and no cycle, skip
        if (state[course] == 2) {
            return false;
        }
        
        // Mark as visiting (in current path)
        state[course] = 1;
        
        // Check all courses that depend on this one
        for (int next : graph[course]) {
            if (hasCycle(graph, state, next)) {
                return true;
            }
        }
        
        // Mark as visited (processed)
        state[course] = 2;
        
        return false;
    }
};

// Time: O(V + E) - visit each vertex and edge once
// Space: O(V + E) - graph storage and recursion stack
```

**Visualization:**
```
Example: prerequisites = [[1,0], [2,1], [0,2]]

Build graph (adjacency list):
0 → [2]  (if take 0, can take 2)
1 → [0]  (if take 1, can take 0)
2 → [1]  (if take 2, can take 1)

DFS from course 0:
  state[0] = 1 (visiting)
  Check neighbors: [2]
    DFS from course 2:
      state[2] = 1 (visiting)
      Check neighbors: [1]
        DFS from course 1:
          state[1] = 1 (visiting)
          Check neighbors: [0]
            DFS from course 0:
              state[0] == 1 → CYCLE DETECTED!

Path: 0 → 2 → 1 → 0 (cycle!)

State meanings:
  0 (white): Unvisited
  1 (gray): Visiting - in current DFS path
  2 (black): Visited - fully processed
  
If we reach a gray node, we're revisiting a node in current path → cycle!
```

**3-State Coloring Explanation:**
```
Why 3 states instead of 2 (visited/unvisited)?

Example: Graph with no cycle but multiple paths

    0 → 1
    ↓   ↓
    2 → 3

DFS from 0:
  Visit 0 → 1 → 3 (mark all as visited)
  Back to 0, try path 0 → 2 → 3
  
If we only had visited/unvisited:
  When we reach 3 from 2, it's already visited
  But this doesn't mean cycle!
  
With 3 states:
  state[3] = 2 (black/fully processed)
  We only detect cycle if state == 1 (gray/in current path)
```

---

### Problem 3: Validate Binary Search Tree ⭐
**LeetCode 98 - Medium**

**Description:** Given the root of a binary tree, determine if it is a valid binary search tree (BST). A valid BST is defined as follows:
- Left subtree contains only nodes with keys less than node's key
- Right subtree contains only nodes with keys greater than node's key
- Both left and right subtrees must also be BSTs

**Example:**
```
Input: root = [2,1,3]
    2
   / \
  1   3
Output: true

Input: root = [5,1,4,null,null,3,6]
    5
   / \
  1   4
     / \
    3   6
Output: false
Explanation: 4 should be less than 5, but its right child 6 is greater than 5
```

**Solution 1: DFS with Range Checking**
```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        return validate(root, LONG_MIN, LONG_MAX);
    }
    
private:
    bool validate(TreeNode* node, long minVal, long maxVal) {
        // Empty tree is valid BST
        if (!node) return true;
        
        // Current node must be in valid range
        if (node->val <= minVal || node->val >= maxVal) {
            return false;
        }
        
        // Recursively check subtrees with updated ranges
        // Left subtree: all values must be < node->val
        // Right subtree: all values must be > node->val
        return validate(node->left, minVal, node->val) &&
               validate(node->right, node->val, maxVal);
    }
};

// Time: O(n) - visit each node once
// Space: O(h) - recursion stack height
```

**Visualization:**
```
Tree:     5
         / \
        1   4
           / \
          3   6

Check root 5: range (-∞, +∞) ✓
  Check left 1: range (-∞, 5)
    1 in (-∞, 5)? YES ✓
    Check left of 1: null ✓
    Check right of 1: null ✓
    
  Check right 4: range (5, +∞)
    4 in (5, +∞)? NO! 4 < 5 ✗
    
Return FALSE

Correct interpretation:
  Node 4 should be greater than 5 (its ancestor)
  but 4 < 5, so invalid BST
```

**Solution 2: Inorder Traversal (Elegant)**
```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        TreeNode* prev = nullptr;
        return inorder(root, prev);
    }
    
private:
    bool inorder(TreeNode* node, TreeNode*& prev) {
        if (!node) return true;
        
        // Check left subtree
        if (!inorder(node->left, prev)) {
            return false;
        }
        
        // Check current node
        // In valid BST, inorder traversal gives sorted sequence
        // So current node must be > previous node
        if (prev && node->val <= prev->val) {
            return false;
        }
        
        // Update previous node
        prev = node;
        
        // Check right subtree
        return inorder(node->right, prev);
    }
};

// Time: O(n)
// Space: O(h)
```

**Why Inorder Works:**
```
Property of BST: Inorder traversal gives sorted sequence

Valid BST:    2
             / \
            1   3
            
Inorder: 1 → 2 → 3 (sorted) ✓

Invalid BST:  5
             / \
            1   4
               / \
              3   6
              
Inorder: 1 → 5 → 3 → 4 → 6 (NOT sorted!) ✗
         5 > 3 violates sorted property
```

---

### Problem 4: Clone Graph
**LeetCode 133 - Medium**

**Description:** Given a reference of a node in a connected undirected graph, return a deep copy (clone) of the graph. Each node contains a value and a list of its neighbors.

**Example:**
```
Input: adjList = [[2,4],[1,3],[2,4],[1,3]]

    1 --- 2
    |     |
    4 --- 3

Output: Deep copy of the same graph
```

**Solution (DFS with HashMap):**
```cpp
class Solution {
public:
    Node* cloneGraph(Node* node) {
        if (!node) return nullptr;
        
        unordered_map<Node*, Node*> visited;
        return dfs(node, visited);
    }
    
private:
    Node* dfs(Node* node, unordered_map<Node*, Node*>& visited) {
        // If already cloned, return the clone
        if (visited.count(node)) {
            return visited[node];
        }
        
        // Create clone of current node
        Node* clone = new Node(node->val);
        
        // IMPORTANT: Add to visited BEFORE processing neighbors
        // This prevents infinite recursion in cycles
        visited[node] = clone;
        
        // Clone all neighbors recursively
        for (Node* neighbor : node->neighbors) {
            clone->neighbors.push_back(dfs(neighbor, visited));
        }
        
        return clone;
    }
};

// Time: O(V + E) - visit each vertex and edge once
// Space: O(V) - hashmap and recursion stack
```

**Visualization:**
```
Original:  1 --- 2
           |     |
           4 --- 3

DFS from node 1:
  visited = {}
  
  Clone node 1: clone1
  visited = {1: clone1}
  
  Process neighbors of 1: [2, 4]
    DFS(2):
      Clone node 2: clone2
      visited = {1: clone1, 2: clone2}
      
      Process neighbors of 2: [1, 3]
        DFS(1):
          Already in visited! Return clone1
        DFS(3):
          Clone node 3: clone3
          visited = {1: clone1, 2: clone2, 3: clone3}
          
          Process neighbors of 3: [2, 4]
            DFS(2): Return clone2 (already visited)
            DFS(4):
              Clone node 4: clone4
              visited = {1: clone1, 2: clone2, 3: clone3, 4: clone4}
              
              Process neighbors of 4: [1, 3]
                DFS(1): Return clone1
                DFS(3): Return clone3
              
              clone4.neighbors = [clone1, clone3]
              Return clone4
            
            clone3.neighbors = [clone2, clone4]
            Return clone3
        
        clone2.neighbors = [clone1, clone3]
        Return clone2
      
    DFS(4):
      Already in visited! Return clone4
    
    clone1.neighbors = [clone2, clone4]
    Return clone1

Final cloned graph structure mirrors original
```

**Key Insight - Why HashMap?**
```
Graph has cycles! Without tracking visited nodes:

DFS(1) → clone 1
  DFS(2) → clone 2
    DFS(1) → clone 1 AGAIN (infinite recursion!)
    
HashMap serves two purposes:
1. Prevent infinite recursion (cycle detection)
2. Ensure same node isn't cloned multiple times
3. Allow us to link back to previously cloned nodes
```

---

## 🎯 BFS vs DFS Decision Guide

| Scenario | Use BFS | Use DFS |
|----------|---------|---------|
| **Shortest path (unweighted)** | ✅ | ❌ |
| **Level order** | ✅ | ❌ |
| **Explore all paths** | ❌ | ✅ |
| **Cycle detection** | ❌ | ✅ |
| **Topological sort** | ✅ | ✅ |
| **Connected components** | ✅ | ✅ |
| **Tree traversal** | ✅ | ✅ |
| **Memory efficient (deep graph)** | ❌ | ✅ |
| **Memory efficient (wide graph)** | ✅ | ❌ |

---

## 💡 Common Patterns Summary

### BFS Patterns:
1. **Level-order traversal**: Track level size
2. **Multi-source BFS**: Add all sources to queue initially
3. **Bidirectional BFS**: Search from both ends
4. **Grid BFS**: Use direction arrays

### DFS Patterns:
1. **Recursive DFS**: Most intuitive for trees
2. **3-state coloring**: Cycle detection (white/gray/black)
3. **Backtracking**: Try path, undo if fails
4. **Inorder BST**: Gives sorted sequence

---

## ⚡ Quick Templates

```cpp
// BFS - Level Order
queue<Node*> q;
q.push(start);
while (!q.empty()) {
    int size = q.size();
    for (int i = 0; i < size; i++) {
        Node* node = q.front(); q.pop();
        // process
        // add neighbors
    }
}

// DFS - Recursive
void dfs(Node* node, set<Node*>& visited) {
    if (!node || visited.count(node)) return;
    visited.insert(node);
    for (auto neighbor : node->neighbors) {
        dfs(neighbor, visited);
    }
}

// DFS - Iterative
stack<Node*> s;
s.push(start);
while (!s.empty()) {
    Node* node = s.top(); s.pop();
    if (visited.count(node)) continue;
    visited.insert(node);
    for (auto neighbor : node->neighbors) {
        s.push(neighbor);
    }
}

// Grid BFS/DFS
int dirs[4][2] = {{0,1},{1,0},{0,-1},{-1,0}};
for (auto& dir : dirs) {
    int nx = x + dir[0];
    int ny = y + dir[1];
    if (isValid(nx, ny)) {
        // process
    }
}
```

**Master these patterns and you'll dominate graph/tree problems! 🚀**
