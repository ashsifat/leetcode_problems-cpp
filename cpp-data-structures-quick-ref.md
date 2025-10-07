# C++ Data Structures Quick Reference
## Essential STL & Usage Guide

---

## 📦 Vector (Dynamic Array)

```cpp
#include <vector>

// Declaration
vector<int> v;                    // Empty vector
vector<int> v(10);                // Size 10, initialized to 0
vector<int> v(10, 5);             // Size 10, all elements = 5
vector<int> v = {1, 2, 3};        // Initialize with values

// Access
v[i]                              // Access element (no bounds check)
v.at(i)                           // Access with bounds check
v.front()                         // First element
v.back()                          // Last element

// Modify
v.push_back(x)                    // Add to end - O(1) amortized
v.pop_back()                      // Remove from end - O(1)
v.insert(v.begin() + i, x)        // Insert at position i - O(n)
v.erase(v.begin() + i)            // Remove at position i - O(n)
v.clear()                         // Remove all elements

// Info
v.size()                          // Number of elements
v.empty()                         // True if empty
v.capacity()                      // Current capacity

// Iterate
for (int x : v) { }               // Range-based for loop
for (int i = 0; i < v.size(); i++) { }
```

---

## 🔗 Deque (Double-Ended Queue)

```cpp
#include <deque>

deque<int> dq;

// Operations (all O(1))
dq.push_back(x)                   // Add to back
dq.push_front(x)                  // Add to front
dq.pop_back()                     // Remove from back
dq.pop_front()                    // Remove from front
dq.front()                        // First element
dq.back()                         // Last element
dq[i]                             // Random access O(1)
```

---

## 📚 Stack (LIFO)

```cpp
#include <stack>

stack<int> st;

st.push(x)                        // Add to top - O(1)
st.pop()                          // Remove from top - O(1)
st.top()                          // Access top element - O(1)
st.size()                         // Number of elements
st.empty()                        // True if empty
```

---

## 📬 Queue (FIFO)

```cpp
#include <queue>

queue<int> q;

q.push(x)                         // Add to back - O(1)
q.pop()                           // Remove from front - O(1)
q.front()                         // First element - O(1)
q.back()                          // Last element - O(1)
q.size()                          // Number of elements
q.empty()                         // True if empty
```

---

## 🔺 Priority Queue (Heap)

```cpp
#include <queue>

// MAX HEAP (default - largest on top)
priority_queue<int> maxHeap;

maxHeap.push(x)                   // Insert - O(log n)
maxHeap.pop()                     // Remove top - O(log n)
maxHeap.top()                     // Access top - O(1)
maxHeap.size()                    // Number of elements
maxHeap.empty()                   // True if empty

// MIN HEAP (smallest on top)
priority_queue<int, vector<int>, greater<int>> minHeap;

// CUSTOM COMPARATOR (struct)
struct Compare {
    bool operator()(int a, int b) {
        return a > b;  // Min heap: reverse comparison
    }
};
priority_queue<int, vector<int>, Compare> pq;

// CUSTOM COMPARATOR (lambda)
auto cmp = [](int a, int b) { return a > b; };
priority_queue<int, vector<int>, decltype(cmp)> pq(cmp);

// PAIRS (compare by first, then second)
priority_queue<pair<int,int>> maxPQ;  // Max by first
priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> minPQ;
```

---

## 🗂️ Set (Ordered Unique Elements)

```cpp
#include <set>

set<int> s;

// Modify
s.insert(x)                       // Add element - O(log n)
s.erase(x)                        // Remove element - O(log n)
s.clear()                         // Remove all

// Search
s.find(x)                         // Returns iterator or s.end() - O(log n)
s.count(x)                        // Returns 0 or 1 - O(log n)

// Info
s.size()                          // Number of elements
s.empty()                         // True if empty

// Iterate (sorted order)
for (int x : s) { }
for (auto it = s.begin(); it != s.end(); it++) { }

// Advanced
s.lower_bound(x)                  // First element >= x
s.upper_bound(x)                  // First element > x
```

---

## 🔀 Unordered Set (Hash Set)

```cpp
#include <unordered_set>

unordered_set<int> us;

// All operations O(1) average, O(n) worst
us.insert(x)                      // Add element
us.erase(x)                       // Remove element
us.find(x)                        // Returns iterator or us.end()
us.count(x)                       // Returns 0 or 1
us.size()                         // Number of elements
us.empty()                        // True if empty

// Iterate (no order guarantee)
for (int x : us) { }
```

---

## 🗺️ Map (Ordered Key-Value Pairs)

```cpp
#include <map>

map<string, int> m;

// Modify
m["key"] = value                  // Insert/update - O(log n)
m.insert({"key", value})          // Insert pair
m.erase("key")                    // Remove by key - O(log n)

// Access
m["key"]                          // Access/create - O(log n)
m.at("key")                       // Access with exception if missing

// Search
m.find("key")                     // Returns iterator or m.end() - O(log n)
m.count("key")                    // Returns 0 or 1 - O(log n)

// Info
m.size()
m.empty()

// Iterate (sorted by key)
for (auto& [key, val] : m) { }
for (auto& p : m) { 
    p.first;   // key
    p.second;  // value
}
```

---

## 🔑 Unordered Map (Hash Map)

```cpp
#include <unordered_map>

unordered_map<string, int> um;

// All operations O(1) average, O(n) worst
um["key"] = value                 // Insert/update
um.insert({"key", value})
um.erase("key")                   // Remove
um["key"]                         // Access (creates if missing)
um.at("key")                      // Access (throws if missing)
um.find("key")                    // Returns iterator
um.count("key")                   // Returns 0 or 1
um.size()
um.empty()

// Iterate (no order guarantee)
for (auto& [key, val] : um) { }
```

---

## 🌳 Multiset (Ordered with Duplicates)

```cpp
#include <set>

multiset<int> ms;

ms.insert(x)                      // Add element - O(log n)
ms.erase(x)                       // Removes ALL occurrences - O(k + log n)
ms.erase(ms.find(x))              // Remove single occurrence - O(log n)
ms.count(x)                       // Count occurrences - O(k + log n)
ms.size()
ms.empty()

// Iterate (sorted, duplicates together)
for (int x : ms) { }
```

---

## 🔢 Pair & Tuple

```cpp
#include <utility>  // pair
#include <tuple>    // tuple

// Pair
pair<int, string> p = {1, "hello"};
pair<int, string> p = make_pair(1, "hello");

p.first                           // Access first element
p.second                          // Access second element

// Tuple
tuple<int, string, double> t = {1, "hi", 3.14};
tuple<int, string, double> t = make_tuple(1, "hi", 3.14);

get<0>(t)                         // Access by index
auto [a, b, c] = t;               // Structured binding (C++17)
```

---

## 🔗 Linked List Nodes

```cpp
// Singly Linked List Node
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};

// Usage
ListNode* head = new ListNode(1);
head->next = new ListNode(2);

// Doubly Linked List Node
struct DListNode {
    int val;
    DListNode *prev, *next;
    DListNode(int x) : val(x), prev(nullptr), next(nullptr) {}
};
```

---

## 🌲 Tree Nodes

```cpp
// Binary Tree Node
struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

// Usage
TreeNode* root = new TreeNode(1);
root->left = new TreeNode(2);
root->right = new TreeNode(3);

// N-ary Tree Node
struct Node {
    int val;
    vector<Node*> children;
    Node(int x) : val(x) {}
};
```

---

## 🔧 Common Algorithms

```cpp
#include <algorithm>

// Sort
sort(v.begin(), v.end());                    // Ascending
sort(v.begin(), v.end(), greater<int>());    // Descending
sort(v.begin(), v.end(), [](int a, int b) {  // Custom
    return a > b;
});

// Binary Search (requires sorted)
binary_search(v.begin(), v.end(), x);        // Returns bool
lower_bound(v.begin(), v.end(), x);          // First >= x
upper_bound(v.begin(), v.end(), x);          // First > x

// Min/Max
min(a, b)
max(a, b)
*min_element(v.begin(), v.end())             // Min in range
*max_element(v.begin(), v.end())             // Max in range

// Reverse
reverse(v.begin(), v.end());

// Fill
fill(v.begin(), v.end(), value);

// Count
count(v.begin(), v.end(), value);

// Find
auto it = find(v.begin(), v.end(), value);
if (it != v.end()) { /* found */ }

// Remove duplicates (requires sorted)
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());

// Next/Prev permutation
next_permutation(v.begin(), v.end());
prev_permutation(v.begin(), v.end());
```

---

## 📊 Time Complexity Summary

| Data Structure | Access | Search | Insert | Delete |
|----------------|--------|--------|--------|--------|
| **Vector** | O(1) | O(n) | O(n) | O(n) |
| **Deque** | O(1) | O(n) | O(1)* | O(1)* |
| **Stack** | O(1) | - | O(1) | O(1) |
| **Queue** | O(1) | - | O(1) | O(1) |
| **Priority Queue** | O(1)† | - | O(log n) | O(log n) |
| **Set** | O(log n) | O(log n) | O(log n) | O(log n) |
| **Unordered Set** | - | O(1)‡ | O(1)‡ | O(1)‡ |
| **Map** | O(log n) | O(log n) | O(log n) | O(log n) |
| **Unordered Map** | O(1)‡ | O(1)‡ | O(1)‡ | O(1)‡ |

*At ends only  
†Top only  
‡Average case, O(n) worst

---

## 🎯 Quick Decision Guide

**Need ordered elements?**
- Yes → `set`, `map`, `multiset`
- No → `unordered_set`, `unordered_map`

**Need duplicates?**
- With order → `multiset`, `multimap`
- Without order → `unordered_multiset`

**Need fast access by index?**
- Yes → `vector`, `deque`
- No → `list`

**Need LIFO (Last In First Out)?**
- Use `stack`

**Need FIFO (First In First Out)?**
- Use `queue`

**Need priority-based removal?**
- Use `priority_queue` (heap)

**Need key-value pairs?**
- Ordered keys → `map`
- Fast lookup → `unordered_map`

---

## 💡 Common Patterns

```cpp
// Frequency counting
unordered_map<int, int> freq;
for (int x : nums) freq[x]++;

// Two sum pattern
unordered_map<int, int> seen;
for (int i = 0; i < n; i++) {
    if (seen.count(target - nums[i])) {
        return {seen[target - nums[i]], i};
    }
    seen[nums[i]] = i;
}

// Sliding window with set
unordered_set<char> window;
int left = 0;
for (int right = 0; right < s.length(); right++) {
    while (window.count(s[right])) {
        window.erase(s[left++]);
    }
    window.insert(s[right]);
}

// BFS with queue
queue<Node*> q;
q.push(start);
while (!q.empty()) {
    Node* node = q.front();
    q.pop();
    // process node
    for (auto neighbor : node->neighbors) {
        q.push(neighbor);
    }
}

// DFS with stack
stack<Node*> s;
s.push(start);
while (!s.empty()) {
    Node* node = s.top();
    s.pop();
    // process node
}

// Top K with heap
priority_queue<int, vector<int>, greater<int>> minHeap;
for (int x : nums) {
    minHeap.push(x);
    if (minHeap.size() > k) minHeap.pop();
}
```

---

## ⚠️ Common Pitfalls

```cpp
// ❌ Wrong: Modifying container while iterating
for (int x : v) {
    v.push_back(x);  // Undefined behavior!
}

// ✅ Correct: Use index or copy
for (int i = 0; i < v.size(); i++) { }

// ❌ Wrong: Accessing non-existent key creates it
if (map[key] == value) { }  // Creates key if missing

// ✅ Correct: Use count or find
if (map.count(key) && map[key] == value) { }
if (map.find(key) != map.end() && map[key] == value) { }

// ❌ Wrong: Comparing sizes of different types
if (i < v.size() - 1) { }  // Issue if v.size() == 0 (underflow)

// ✅ Correct: Handle edge cases
if (!v.empty() && i < v.size() - 1) { }
if (i + 1 < v.size()) { }

// ❌ Wrong: Not checking iterator validity
auto it = s.find(x);
cout << *it;  // Crash if not found!

// ✅ Correct: Check before dereferencing
auto it = s.find(x);
if (it != s.end()) {
    cout << *it;
}
```

**Master these and you're ready for any interview! 🚀**