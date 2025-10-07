# Heap / Priority Queue Pattern Cheatsheet
## Essential for Real-Time Systems & Scheduling

---

## 🎯 When to Use Heap/Priority Queue

### Classic Indicators:
- ✅ Finding **top K largest/smallest** elements
- ✅ **Kth largest/smallest** element
- ✅ **Merge K sorted** lists/arrays
- ✅ **Median** of stream
- ✅ **Scheduling** with priorities
- ✅ **Task scheduling** in real-time systems
- ✅ Keywords: "top K", "largest", "smallest", "median", "priority"

### Robotics Applications:
- **Sensor fusion** - prioritize most reliable sensors
- **Task scheduling** - execute highest priority commands first
- **Path planning** - A* algorithm uses priority queue
- **Resource allocation** - assign limited resources optimally

## ⏱️ Time Complexity
- **Insert**: O(log n)
- **Extract min/max**: O(log n)
- **Peek min/max**: O(1)
- **Build heap**: O(n)

---

## 📚 C++ Priority Queue Basics

### Default: Max Heap
```cpp
#include <queue>

// Max heap (largest element on top)
priority_queue<int> maxHeap;

maxHeap.push(3);
maxHeap.push(1);
maxHeap.push(5);

maxHeap.top();    // Returns 5 (largest)
maxHeap.pop();    // Removes 5
maxHeap.size();   // Returns 2
maxHeap.empty();  // Returns false
```

### Min Heap
```cpp
// Min heap (smallest element on top)
priority_queue<int, vector<int>, greater<int>> minHeap;

minHeap.push(3);
minHeap.push(1);
minHeap.push(5);

minHeap.top();    // Returns 1 (smallest)
```

### Custom Comparator
```cpp
// Custom struct
struct Task {
    int priority;
    string name;
};

// Custom comparator (min heap by priority)
struct CompareTask {
    bool operator()(const Task& a, const Task& b) {
        return a.priority > b.priority;  // Min heap: reverse comparison
    }
};

priority_queue<Task, vector<Task>, CompareTask> taskQueue;
```

### Using Lambda (C++11)
```cpp
auto cmp = [](const pair<int,int>& a, const pair<int,int>& b) {
    return a.first > b.first;  // Min heap by first element
};

priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> pq(cmp);
```

---

## 🎨 Common Heap Patterns

### Pattern 1: Top K Elements

```cpp
// Find K largest elements
vector<int> topKLargest(vector<int>& nums, int k) {
    // Use MIN heap of size k
    // Keep k largest by removing smallest when size > k
    priority_queue<int, vector<int>, greater<int>> minHeap;
    
    for (int num : nums) {
        minHeap.push(num);
        if (minHeap.size() > k) {
            minHeap.pop();  // Remove smallest
        }
    }
    
    // Extract results
    vector<int> result;
    while (!minHeap.empty()) {
        result.push_back(minHeap.top());
        minHeap.pop();
    }
    return result;
}

// Time: O(n log k)
// Space: O(k)
```

**Key Insight:**
- For **K largest**: Use **min heap** (remove smallest to keep largest)
- For **K smallest**: Use **max heap** (remove largest to keep smallest)

---

### Pattern 2: Merge K Sorted Lists/Arrays

```cpp
struct Element {
    int value;
    int listIdx;   // Which list this came from
    int elemIdx;   // Index in that list
};

vector<int> mergeKSorted(vector<vector<int>>& lists) {
    auto cmp = [](const Element& a, const Element& b) {
        return a.value > b.value;  // Min heap
    };
    
    priority_queue<Element, vector<Element>, decltype(cmp)> minHeap(cmp);
    
    // Add first element from each list
    for (int i = 0; i < lists.size(); i++) {
        if (!lists[i].empty()) {
            minHeap.push({lists[i][0], i, 0});
        }
    }
    
    vector<int> result;
    
    while (!minHeap.empty()) {
        Element curr = minHeap.top();
        minHeap.pop();
        
        result.push_back(curr.value);
        
        // Add next element from same list
        int nextIdx = curr.elemIdx + 1;
        if (nextIdx < lists[curr.listIdx].size()) {
            minHeap.push({
                lists[curr.listIdx][nextIdx],
                curr.listIdx,
                nextIdx
            });
        }
    }
    
    return result;
}

// Time: O(n log k) where n = total elements, k = number of lists
// Space: O(k) for heap
```

---

### Pattern 3: Two Heaps (Median Finding)

```cpp
class MedianFinder {
private:
    priority_queue<int> maxHeap;  // Smaller half (max on top)
    priority_queue<int, vector<int>, greater<int>> minHeap;  // Larger half
    
public:
    void addNum(int num) {
        // Always add to maxHeap first
        maxHeap.push(num);
        
        // Balance: ensure maxHeap.top() <= minHeap.top()
        minHeap.push(maxHeap.top());
        maxHeap.pop();
        
        // Maintain size: maxHeap size >= minHeap size
        if (maxHeap.size() < minHeap.size()) {
            maxHeap.push(minHeap.top());
            minHeap.pop();
        }
    }
    
    double findMedian() {
        if (maxHeap.size() > minHeap.size()) {
            return maxHeap.top();
        } else {
            return (maxHeap.top() + minHeap.top()) / 2.0;
        }
    }
};

// addNum: O(log n)
// findMedian: O(1)
```

---

## 📚 Essential Problems with Detailed Solutions

### Problem 1: Kth Largest Element in Array ⭐
**LeetCode 215 - Medium**

**Description:** Given an integer array `nums` and an integer `k`, return the kth largest element in the array. Note that it is the kth largest element in sorted order, not the kth distinct element.

**Example:**
```
Input: nums = [3,2,1,5,6,4], k = 2
Output: 5
Explanation: Sorted: [6,5,4,3,2,1], 2nd largest is 5

Input: nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
```

**Solution 1: Min Heap (Best for Small K)**
```cpp
int findKthLargest(vector<int>& nums, int k) {
    // MIN heap of size k
    // Maintains k largest elements, smallest on top
    priority_queue<int, vector<int>, greater<int>> minHeap;
    
    for (int num : nums) {
        minHeap.push(num);
        
        // Keep only k elements
        // When size > k, remove smallest
        if (minHeap.size() > k) {
            minHeap.pop();
        }
    }
    
    // Top of min heap is kth largest
    return minHeap.top();
}

// Time: O(n log k) - n pushes, each O(log k)
// Space: O(k) - heap size
```

**Visualization:**
```
nums = [3,2,1,5,6,4], k = 2
Need 2nd largest

Process each number:

num=3: heap=[3]
num=2: heap=[2,3]
num=1: heap=[1,2,3] → size>2, pop 1 → heap=[2,3]
num=5: heap=[2,3,5] → size>2, pop 2 → heap=[3,5]
num=6: heap=[3,5,6] → size>2, pop 3 → heap=[5,6]
num=4: heap=[4,5,6] → size>2, pop 4 → heap=[5,6]

Final heap (min heap of size 2): [5,6]
Top = 5 = 2nd largest ✓

Why min heap for k LARGEST?
- We want to keep k largest elements
- When we have k+1 elements, remove the SMALLEST
- Min heap puts smallest on top for easy removal
```

**Solution 2: Quickselect (Best Average Case)**
```cpp
int findKthLargest(vector<int>& nums, int k) {
    // Quickselect: find (n-k)th smallest element
    // = kth largest
    int n = nums.size();
    return quickSelect(nums, 0, n - 1, n - k);
}

int quickSelect(vector<int>& nums, int left, int right, int k) {
    if (left == right) return nums[left];
    
    // Random pivot for better average performance
    int pivotIdx = left + rand() % (right - left + 1);
    
    // Partition around pivot
    pivotIdx = partition(nums, left, right, pivotIdx);
    
    if (k == pivotIdx) {
        return nums[k];
    } else if (k < pivotIdx) {
        return quickSelect(nums, left, pivotIdx - 1, k);
    } else {
        return quickSelect(nums, pivotIdx + 1, right, k);
    }
}

int partition(vector<int>& nums, int left, int right, int pivotIdx) {
    int pivot = nums[pivotIdx];
    
    // Move pivot to end
    swap(nums[pivotIdx], nums[right]);
    
    int storeIdx = left;
    for (int i = left; i < right; i++) {
        if (nums[i] < pivot) {
            swap(nums[i], nums[storeIdx]);
            storeIdx++;
        }
    }
    
    // Move pivot to final position
    swap(nums[storeIdx], nums[right]);
    return storeIdx;
}

// Time: O(n) average, O(n²) worst case
// Space: O(1)
```

**When to Use Which:**
- **Small k (k << n)**: Use min heap - O(n log k)
- **Large k or k ≈ n/2**: Use quickselect - O(n) average
- **Need stability/online algorithm**: Use heap

---

### Problem 2: Top K Frequent Elements ⭐
**LeetCode 347 - Medium**

**Description:** Given an integer array `nums` and an integer `k`, return the k most frequent elements. You may return the answer in any order.

**Example:**
```
Input: nums = [1,1,1,2,2,3], k = 2
Output: [1,2]
Explanation: 1 appears 3 times, 2 appears 2 times

Input: nums = [1], k = 1
Output: [1]
```

**Solution:**
```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {
    // STEP 1: Count frequencies
    unordered_map<int, int> freq;
    for (int num : nums) {
        freq[num]++;
    }
    
    // STEP 2: Use min heap to keep k most frequent
    // Pair: (frequency, number)
    // Min heap by frequency
    auto cmp = [](const pair<int,int>& a, const pair<int,int>& b) {
        return a.first > b.first;  // Min heap
    };
    
    priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> minHeap(cmp);
    
    // Add to heap
    for (auto& [num, count] : freq) {
        minHeap.push({count, num});
        
        // Keep only k elements
        if (minHeap.size() > k) {
            minHeap.pop();  // Remove least frequent
        }
    }
    
    // STEP 3: Extract results
    vector<int> result;
    while (!minHeap.empty()) {
        result.push_back(minHeap.top().second);
        minHeap.pop();
    }
    
    return result;
}

// Time: O(n log k) where n = unique numbers
// Space: O(n) for frequency map
```

**Visualization:**
```
nums = [1,1,1,2,2,3], k = 2

Step 1: Count frequencies
freq = {1:3, 2:2, 3:1}

Step 2: Build heap
Process (1, 3): heap = [(3,1)]
Process (2, 2): heap = [(2,2), (3,1)]  // min heap by frequency
Process (3, 1): heap = [(1,3), (2,2), (3,1)]
                size > 2, pop (1,3)
                heap = [(2,2), (3,1)]

Step 3: Extract
result = [2, 1]  // or [1, 2], order doesn't matter
```

**Alternative: Bucket Sort (O(n))**
```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {
    // Count frequencies
    unordered_map<int, int> freq;
    for (int num : nums) {
        freq[num]++;
    }
    
    // Bucket sort: bucket[i] = numbers with frequency i
    int n = nums.size();
    vector<vector<int>> buckets(n + 1);
    
    for (auto& [num, count] : freq) {
        buckets[count].push_back(num);
    }
    
    // Collect top k from highest frequency
    vector<int> result;
    for (int i = n; i >= 0 && result.size() < k; i--) {
        for (int num : buckets[i]) {
            result.push_back(num);
            if (result.size() == k) break;
        }
    }
    
    return result;
}

// Time: O(n)
// Space: O(n)
```

---

### Problem 3: Merge K Sorted Lists ⭐
**LeetCode 23 - Hard**

**Description:** You are given an array of k linked-lists, each linked-list is sorted in ascending order. Merge all the linked-lists into one sorted linked-list and return it.

**Example:**
```
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]

Explanation:
List 1: 1 → 4 → 5
List 2: 1 → 3 → 4
List 3: 2 → 6
Merged: 1 → 1 → 2 → 3 → 4 → 4 → 5 → 6
```

**Solution:**
```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};

class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        // Custom comparator for min heap
        // Compare by node values
        auto cmp = [](ListNode* a, ListNode* b) {
            return a->val > b->val;  // Min heap
        };
        
        priority_queue<ListNode*, vector<ListNode*>, decltype(cmp)> minHeap(cmp);
        
        // STEP 1: Add first node from each list
        for (ListNode* head : lists) {
            if (head) {
                minHeap.push(head);
            }
        }
        
        // STEP 2: Build result list
        ListNode dummy(0);
        ListNode* tail = &dummy;
        
        while (!minHeap.empty()) {
            // Get smallest node
            ListNode* node = minHeap.top();
            minHeap.pop();
            
            // Add to result
            tail->next = node;
            tail = tail->next;
            
            // Add next node from same list
            if (node->next) {
                minHeap.push(node->next);
            }
        }
        
        return dummy.next;
    }
};

// Time: O(n log k) where n = total nodes, k = number of lists
// Space: O(k) for heap
```

**Visualization:**
```
Lists: 
  1 → 4 → 5
  1 → 3 → 4
  2 → 6

Step 1: Initialize heap with first nodes
Heap = [1(list0), 1(list1), 2(list2)]  // min heap

Step 2: Extract and build result

Extract 1 (from list0):
  Result: 1
  Add next from list0 (4)
  Heap = [1(list1), 2(list2), 4(list0)]

Extract 1 (from list1):
  Result: 1 → 1
  Add next from list1 (3)
  Heap = [2(list2), 3(list1), 4(list0)]

Extract 2 (from list2):
  Result: 1 → 1 → 2
  Add next from list2 (6)
  Heap = [3(list1), 4(list0), 6(list2)]

Extract 3 (from list1):
  Result: 1 → 1 → 2 → 3
  Add next from list1 (4)
  Heap = [4(list0), 4(list1), 6(list2)]

Extract 4 (from list0):
  Result: 1 → 1 → 2 → 3 → 4
  Add next from list0 (5)
  Heap = [4(list1), 5(list0), 6(list2)]

Extract 4 (from list1):
  Result: 1 → 1 → 2 → 3 → 4 → 4
  No next from list1
  Heap = [5(list0), 6(list2)]

Extract 5 (from list0):
  Result: 1 → 1 → 2 → 3 → 4 → 4 → 5
  No next from list0
  Heap = [6(list2)]

Extract 6 (from list2):
  Result: 1 → 1 → 2 → 3 → 4 → 4 → 5 → 6
  Heap = []

Done!
```

**Why Heap Approach?**
```
Alternative: Merge two at a time
- Merge lists[0] and lists[1] → temp1
- Merge temp1 and lists[2] → temp2
- ...
- Time: O(kn) - each merge costs O(n), do k-1 merges

Heap approach:
- Always pick smallest across all lists
- Each pick costs O(log k)
- Total: O(n log k) - much better!
```

---

### Problem 4: Find Median from Data Stream ⭐⭐
**LeetCode 295 - Hard**

**Description:** The median is the middle value in an ordered integer list. If the size of the list is even, there is no middle value and the median is the mean of the two middle values.

Implement the MedianFinder class:
- `MedianFinder()` initializes the object
- `void addNum(int num)` adds integer num from the data stream
- `double findMedian()` returns the median of all elements so far

**Example:**
```
MedianFinder medianFinder = new MedianFinder();
medianFinder.addNum(1);    // arr = [1]
medianFinder.addNum(2);    // arr = [1, 2]
medianFinder.findMedian(); // return 1.5 (1 + 2) / 2
medianFinder.addNum(3);    // arr = [1, 2, 3]
medianFinder.findMedian(); // return 2.0
```

**Solution (Two Heaps):**
```cpp
class MedianFinder {
private:
    // Max heap for smaller half (largest on top)
    priority_queue<int> maxHeap;
    
    // Min heap for larger half (smallest on top)
    priority_queue<int, vector<int>, greater<int>> minHeap;
    
public:
    MedianFinder() {}
    
    void addNum(int num) {
        // STRATEGY: Keep heaps balanced
        // maxHeap contains smaller half
        // minHeap contains larger half
        // maxHeap.size() == minHeap.size() OR maxHeap.size() = minHeap.size() + 1
        
        if (maxHeap.empty() || num <= maxHeap.top()) {
            // Add to smaller half
            maxHeap.push(num);
        } else {
            // Add to larger half
            minHeap.push(num);
        }
        
        // BALANCE HEAPS
        // Ensure maxHeap has at most 1 more element than minHeap
        
        if (maxHeap.size() > minHeap.size() + 1) {
            // Too many in maxHeap, move one to minHeap
            minHeap.push(maxHeap.top());
            maxHeap.pop();
        } else if (minHeap.size() > maxHeap.size()) {
            // Too many in minHeap, move one to maxHeap
            maxHeap.push(minHeap.top());
            minHeap.pop();
        }
    }
    
    double findMedian() {
        if (maxHeap.size() > minHeap.size()) {
            // Odd total count, median is top of maxHeap
            return maxHeap.top();
        } else {
            // Even total count, median is average of two tops
            return (maxHeap.top() + minHeap.top()) / 2.0;
        }
    }
};

// addNum: O(log n)
// findMedian: O(1)
// Space: O(n)
```

**Visualization:**
```
Data stream: 1, 2, 3, 4, 5

After adding 1:
  maxHeap = [1]
  minHeap = []
  Median = 1

After adding 2:
  Try add to maxHeap? 2 > 1 (maxHeap.top()), so add to minHeap
  maxHeap = [1]
  minHeap = [2]
  Median = (1 + 2) / 2 = 1.5

After adding 3:
  3 > 1, add to minHeap
  maxHeap = [1]
  minHeap = [2, 3]
  Rebalance: minHeap.size() > maxHeap.size()
  Move 2 to maxHeap
  maxHeap = [2, 1]
  minHeap = [3]
  Median = 2

After adding 4:
  4 > 2, add to minHeap
  maxHeap = [2, 1]
  minHeap = [3, 4]
  Median = (2 + 3) / 2 = 2.5

After adding 5:
  5 > 2, add to minHeap
  maxHeap = [2, 1]
  minHeap = [3, 4, 5]
  Rebalance: move 3 to maxHeap
  maxHeap = [3, 2, 1]
  minHeap = [4, 5]
  Median = 3

Invariants maintained:
1. maxHeap.top() <= minHeap.top() (all in max <= all in min)
2. |maxHeap.size() - minHeap.size()| <= 1
3. If sizes differ, maxHeap has 1 more
```

**Why Two Heaps?**
```
Problem: Need median quickly after each insert
Naive: Sort array each time → O(n log n) per insert

Two heap solution:
- Split data into two halves
- maxHeap has smaller half, largest on top
- minHeap has larger half, smallest on top
- Median is either:
  - Top of maxHeap (odd count)
  - Average of two tops (even count)

Key insight: We don't need full sort, just middle element(s)!
```

---

### Problem 5: Task Scheduler ⭐
**LeetCode 621 - Medium**

**Description:** Given a characters array `tasks` representing tasks a CPU needs to do, where each letter represents a different task. Tasks can be done in any order, but there is a constraint: there has to be n units of time gap between two same tasks.

Return the least number of units of time the CPU will take to finish all tasks.

**Example:**
```
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation: A -> B -> idle -> A -> B -> idle -> A -> B
             Wait 2 units between same tasks

Input: tasks = ["A","A","A","B","B","B"], n = 0
Output: 6
Explanation: No waiting needed: A -> A -> A -> B -> B -> B
```

**Solution:**
```cpp
int leastInterval(vector<char>& tasks, int n) {
    // STEP 1: Count frequency of each task
    unordered_map<char, int> freq;
    for (char task : tasks) {
        freq[task]++;
    }
    
    // STEP 2: Use max heap to always pick most frequent available task
    priority_queue<int> maxHeap;
    for (auto& [task, count] : freq) {
        maxHeap.push(count);
    }
    
    int time = 0;
    
    // STEP 3: Process tasks
    while (!maxHeap.empty()) {
        vector<int> temp;  // Store tasks executed in this cycle
        
        // Process up to n+1 tasks in one cycle
        for (int i = 0; i <= n; i++) {
            if (!maxHeap.empty()) {
                int count = maxHeap.top();
                maxHeap.pop();
                
                count--;  // Execute this task once
                if (count > 0) {
                    temp.push_back(count);  // Still has remaining
                }
            }
            
            time++;  // One time unit consumed
            
            // If heap empty and no tasks to add back, we're done
            if (maxHeap.empty() && temp.empty()) {
                break;
            }
        }
        
        // Add back tasks that still need execution
        for (int count : temp) {
            maxHeap.push(count);
        }
    }
    
    return time;
}

// Time: O(n) where n = number of tasks
// Space: O(26) = O(1) for at most 26 different tasks
```

**Visualization:**
```
tasks = ["A","A","A","B","B","B"], n = 2

Step 1: Count frequencies
freq = {A: 3, B: 3}

Step 2: Build max heap
heap = [3, 3]  (two tasks with count 3)

Step 3: Process cycles (each cycle is n+1 = 3 slots)

Cycle 1: (3 slots available)
  Execute most frequent (A): count 3 → 2, time=1
  Execute next frequent (B): count 3 → 2, time=2
  Heap empty, need idle: time=3
  Add back: heap = [2, 2]

Cycle 2: (3 slots available)
  Execute A: count 2 → 1, time=4
  Execute B: count 2 → 1, time=5
  Heap empty, need idle: time=6
  Add back: heap = [1, 1]

Cycle 3: (3 slots available)
  Execute A: count 1 → 0, time=7
  Execute B: count 1 → 0, time=8
  Both done, heap empty
  Break early (no need for idle)

Total time: 8
Sequence: A B idle A B idle A B
```

**Mathematical Formula (Optimization):**
```cpp
int leastInterval(vector<char>& tasks, int n) {
    // Count frequencies
    vector<int> freq(26, 0);
    int maxFreq = 0;
    
    for (char task : tasks) {
        freq[task - 'A']++;
        maxFreq = max(maxFreq, freq[task - 'A']);
    }
    
    // Count how many tasks have max frequency
    int maxCount = 0;
    for (int f : freq) {
        if (f == maxFreq) maxCount++;
    }
    
    // Formula:
    // (maxFreq - 1) * (n + 1) + maxCount
    // OR tasks.size() (if no idle needed)
    
    int minTime = (maxFreq - 1) * (n + 1) + maxCount;
    return max((int)tasks.size(), minTime);
}

// Time: O(n)
// Space: O(1)

/*
Explanation:
If A appears most (3 times), n=2:
  A _ _ A _ _ A
  Need (3-1)*(2+1) = 6 slots for gaps
  Plus 1 for last A = 7 total
  
If multiple tasks have max freq:
  A _ _ A _ _ A
  B _ _ B _ _ B
  Need same structure, add 1 per max task
*/
```

---

### Problem 6: K Closest Points to Origin ⭐⭐
**LeetCode 973 - Medium**

**Description:** Given an array of points where `points[i] = [xi, yi]` represents a point on the X-Y plane and an integer k, return the k closest points to the origin (0, 0).

The distance between two points is the Euclidean distance: √(x² + y²).

**Example:**
```
Input: points = [[1,3],[-2,2]], k = 1
Output: [[-2,2]]
Explanation: 
  Distance (1,3): √(1² + 3²) = √10
  Distance (-2,2): √(4 + 4) = √8
  √8 < √10, so [-2,2] is closer

Input: points = [[3,3],[5,-1],[-2,4]], k = 2
Output: [[3,3],[-2,4]]
```

**Solution:**
```cpp
vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
    // Use MAX heap to keep k smallest distances
    // Pair: (distance², point)
    // We use distance² to avoid sqrt computation
    
    auto cmp = [](const pair<int, vector<int>>& a, 
                  const pair<int, vector<int>>& b) {
        return a.first < b.first;  // Max heap by distance
    };
    
    priority_queue<pair<int, vector<int>>, 
                   vector<pair<int, vector<int>>>, 
                   decltype(cmp)> maxHeap(cmp);
    
    for (auto& point : points) {
        int x = point[0], y = point[1];
        int distSquared = x * x + y * y;
        
        maxHeap.push({distSquared, point});
        
        // Keep only k closest
        if (maxHeap.size() > k) {
            maxHeap.pop();  // Remove farthest
        }
    }
    
    // Extract results
    vector<vector<int>> result;
    while (!maxHeap.empty()) {
        result.push_back(maxHeap.top().second);
        maxHeap.pop();
    }
    
    return result;
}

// Time: O(n log k)
// Space: O(k)
```

**Robotics Application:**
```cpp
// Robot sensor fusion: Find K nearest obstacles
struct Obstacle {
    double x, y;
    int sensorId;
    double confidence;
};

vector<Obstacle> findNearestObstacles(
    const vector<Obstacle>& obstacles,
    pair<double, double> robotPos,
    int k) {
    
    auto cmp = [](const pair<double, Obstacle>& a,
                  const pair<double, Obstacle>& b) {
        return a.first < b.first;  // Max heap
    };
    
    priority_queue<pair<double, Obstacle>,
                   vector<pair<double, Obstacle>>,
                   decltype(cmp)> maxHeap(cmp);
    
    for (const auto& obs : obstacles) {
        // Calculate distance from robot
        double dx = obs.x - robotPos.first;
        double dy = obs.y - robotPos.second;
        double dist = dx*dx + dy*dy;  // No need for sqrt
        
        // Weight by sensor confidence
        double score = dist / obs.confidence;
        
        maxHeap.push({score, obs});
        if (maxHeap.size() > k) {
            maxHeap.pop();
        }
    }
    
    // Extract k nearest high-confidence obstacles
    vector<Obstacle> result;
    while (!maxHeap.empty()) {
        result.push_back(maxHeap.top().second);
        maxHeap.pop();
    }
    
    return result;
}
```

---

## 💡 Pattern Recognition Guide

### When to Use Min Heap vs Max Heap

```cpp
// Find K LARGEST elements → Use MIN heap
priority_queue<int, vector<int>, greater<int>> minHeap;
// Keep k largest by removing smallest

// Find K SMALLEST elements → Use MAX heap  
priority_queue<int> maxHeap;
// Keep k smallest by removing largest

// Mnemonic: Use OPPOSITE heap from what you're finding
```

### Common Heap Patterns Summary

| Pattern | Heap Type | Use Case |
|---------|-----------|----------|
| **Top K Largest** | Min Heap (size k) | Keep k largest, remove smallest |
| **Top K Smallest** | Max Heap (size k) | Keep k smallest, remove largest |
| **Median** | Two Heaps | Max heap (lower), Min heap (upper) |
| **Merge K** | Min Heap | Always pick smallest across K sources |
| **Scheduling** | Custom Heap | Priority-based task ordering |

---

## ⚡ Quick Reference

```cpp
// MAX HEAP (default)
priority_queue<int> maxHeap;
maxHeap.push(5);
maxHeap.top();  // largest
maxHeap.pop();

// MIN HEAP
priority_queue<int, vector<int>, greater<int>> minHeap;
minHeap.push(5);
minHeap.top();  // smallest
minHeap.pop();

// CUSTOM COMPARATOR (struct)
struct Compare {
    bool operator()(const T& a, const T& b) {
        return a.priority > b.priority;  // min heap
    }
};
priority_queue<T, vector<T>, Compare> pq;

// CUSTOM COMPARATOR (lambda)
auto cmp = [](const T& a, const T& b) { 
    return a.val > b.val;  // min heap
};
priority_queue<T, vector<T>, decltype(cmp)> pq(cmp);

// TWO HEAPS PATTERN (median)
priority_queue<int> maxHeap;  // smaller half
priority_queue<int, vector<int>, greater<int>> minHeap;  // larger half
```

**Master heap patterns for real-time robotics scheduling! 🚀**