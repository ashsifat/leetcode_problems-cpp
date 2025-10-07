# Binary Search Pattern Cheatsheet
## Essential Pattern for Intuitive Surgical Interview

---

## 🎯 When to Use Binary Search

### Classic Indicators:
- Array is **sorted** (or rotated sorted)
- Finding element in **O(log n)** time
- **Search space is monotonic** (can eliminate half each step)
- **Optimization problems** where you can check "is X feasible?"
- Keywords: "sorted", "minimum/maximum that satisfies", "find in log time"

### Hidden Binary Search Problems:
- **Search space isn't an array** but a range of answers
- Can ask: "Is value X too small/large?" → Monotonic property
- Examples: "Minimum days to deliver packages", "Koko eating bananas"

## ⏱️ Time Complexity: O(log n)

---

## 🎨 Binary Search Templates

### Template 1: Standard Binary Search (Find Exact Value)

```cpp
// Use when: Finding exact target in sorted array
// Returns: Index of target, or -1 if not found

int binarySearch(vector<int>& arr, int target) {
    int left = 0;
    int right = arr.size() - 1;
    
    while (left <= right) {  // <= allows checking single element
        int mid = left + (right - left) / 2;  // Avoid overflow
        
        if (arr[mid] == target) {
            return mid;  // Found!
        } else if (arr[mid] < target) {
            left = mid + 1;   // Search right half
        } else {
            right = mid - 1;  // Search left half
        }
    }
    
    return -1;  // Not found
}

// Why left + (right - left) / 2 instead of (left + right) / 2?
// Prevents integer overflow when left + right > INT_MAX
```

---

### Template 2: Find Left Boundary (First Occurrence)

```cpp
// Use when: Finding first position where condition is true
// Returns: Leftmost index where arr[i] >= target

int leftBoundary(vector<int>& arr, int target) {
    int left = 0;
    int right = arr.size();  // Note: size() not size()-1
    
    while (left < right) {  // Note: < not <=
        int mid = left + (right - left) / 2;
        
        if (arr[mid] < target) {
            left = mid + 1;   // Target is to the right
        } else {
            right = mid;      // Could be the answer, keep searching left
        }
    }
    
    return left;  // Returns insert position if not found
}

// arr = [1,2,2,2,3], target = 2 → returns 1
// arr = [1,2,2,2,3], target = 4 → returns 5 (insert position)
```

---

### Template 3: Find Right Boundary (Last Occurrence)

```cpp
// Use when: Finding last position where condition is true
// Returns: Rightmost index where arr[i] <= target

int rightBoundary(vector<int>& arr, int target) {
    int left = 0;
    int right = arr.size();
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (arr[mid] <= target) {
            left = mid + 1;   // Could be answer, keep searching right
        } else {
            right = mid;      // Target is to the left
        }
    }
    
    return left - 1;  // Adjust by -1
}

// arr = [1,2,2,2,3], target = 2 → returns 3
```

---

### Template 4: Minimize/Maximize (Search on Answer Space)

```cpp
// Use when: Finding minimum value that satisfies condition
// Classic: "Minimum capacity needed to ship in D days"

int minimizeValue(int min, int max, function<bool(int)> isFeasible) {
    int left = min;
    int right = max;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (isFeasible(mid)) {
            right = mid;      // mid works, try smaller
        } else {
            left = mid + 1;   // mid doesn't work, need larger
        }
    }
    
    return left;  // Minimum value that works
}

// For maximize: flip the logic (search for maximum that works)
```

---

## 📚 Essential Problems with Detailed Solutions

### Problem 1: Binary Search (The Foundation)
**LeetCode 704 - Easy**

**Description:** Given a **sorted** array of integers `nums` and an integer `target`, return the index of `target` if it exists, otherwise return `-1`. Must write O(log n) algorithm.

**Example:**
```
Input: nums = [-1,0,3,5,9,12], target = 9
Output: 4
Explanation: 9 exists at index 4

Input: nums = [-1,0,3,5,9,12], target = 2
Output: -1
Explanation: 2 does not exist
```

**Solution:**
```cpp
int search(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
    
    while (left <= right) {
        // Calculate middle index
        // Use left + (right - left)/2 to avoid integer overflow
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;  // Found target!
        } else if (nums[mid] < target) {
            // Target is in right half
            // Since mid < target, mid can't be answer
            left = mid + 1;
        } else {
            // Target is in left half
            // Since mid > target, mid can't be answer
            right = mid - 1;
        }
    }
    
    // Loop ended without finding target
    return -1;
}

// Time: O(log n) - halve search space each iteration
// Space: O(1) - only using pointers
```

**Visualization:**
```
nums = [-1, 0, 3, 5, 9, 12], target = 9

Iteration 1:
[-1, 0, 3, 5, 9, 12]
 L        M        R
nums[mid] = 3 < 9, search right half

Iteration 2:
[-1, 0, 3, 5, 9, 12]
           L  M   R
nums[mid] = 9 = 9, FOUND! Return 4
```

**Why This Works:**
- **Invariant**: If target exists, it's in range [left, right]
- Each iteration eliminates half the search space
- Terminates when left > right (exhausted search space)

---

### Problem 2: Search in Rotated Sorted Array ⭐
**LeetCode 33 - Medium**

**Description:** Given the array `nums` after a possible rotation and an integer `target`, return the index of `target` if it is in `nums`, or `-1` if it is not. Array was originally sorted in ascending order, then rotated at some pivot.

**Example:**
```
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4

Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1

Visualization:
Original: [0,1,2,4,5,6,7]
Rotated:  [4,5,6,7,0,1,2]
           -------  -----
           sorted   sorted
           (higher) (lower)
```

**Solution:**
```cpp
int search(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size() - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;
        }
        
        // KEY INSIGHT: At least one half is always sorted
        // Determine which half is sorted
        
        if (nums[left] <= nums[mid]) {
            // Left half is sorted [left...mid]
            
            // Check if target is in sorted left half
            if (nums[left] <= target && target < nums[mid]) {
                // Target in left half
                right = mid - 1;
            } else {
                // Target in right half
                left = mid + 1;
            }
        } else {
            // Right half is sorted [mid...right]
            
            // Check if target is in sorted right half
            if (nums[mid] < target && target <= nums[right]) {
                // Target in right half
                left = mid + 1;
            } else {
                // Target in left half
                right = mid - 1;
            }
        }
    }
    
    return -1;
}

// Time: O(log n) - still binary search
// Space: O(1)
```

**Detailed Walkthrough:**
```
nums = [4,5,6,7,0,1,2], target = 0

Iteration 1:
[4, 5, 6, 7, 0, 1, 2]
 L        M        R
 
nums[L]=4 <= nums[M]=7? YES → Left half sorted
Is target in [4,7)? 0 in [4,7)? NO
Search right half → left = mid + 1

Iteration 2:
[4, 5, 6, 7, 0, 1, 2]
              L  M  R
              
nums[L]=0 <= nums[M]=1? YES → Left half sorted
Is target in [0,1)? 0 in [0,1)? YES
Search left half → right = mid - 1

Iteration 3:
[4, 5, 6, 7, 0, 1, 2]
              L
              M
              R
              
nums[mid] = 0 = target, FOUND! Return 4
```

**Key Insight:**
```
Rotated array has TWO sorted portions:
[4,5,6,7, | 0,1,2]
 sorted     sorted
 (pivot here)

At any mid point, at least ONE half is sorted:
- If nums[left] <= nums[mid]: left half sorted
- Otherwise: right half sorted

Once we know which half is sorted:
- Check if target is in that sorted range
- If yes, search that half
- If no, search the other half
```

---

### Problem 3: Find Minimum in Rotated Sorted Array
**LeetCode 153 - Medium**

**Description:** Suppose an array of length `n` sorted in ascending order is rotated. Given the sorted rotated array `nums`, return the minimum element.

**Example:**
```
Input: nums = [3,4,5,1,2]
Output: 1
Explanation: Original array was [1,2,3,4,5], rotated to [3,4,5,1,2]

Input: nums = [4,5,6,7,0,1,2]
Output: 0

Input: nums = [11,13,15,17]
Output: 11
Explanation: Not rotated, minimum is first element
```

**Solution:**
```cpp
int findMin(vector<int>& nums) {
    int left = 0;
    int right = nums.size() - 1;
    
    // If array is not rotated at all
    if (nums[left] < nums[right]) {
        return nums[left];
    }
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        // KEY INSIGHT: Minimum is in the unsorted half
        // The sorted half has all elements > minimum
        
        if (nums[mid] > nums[right]) {
            // Right half is unsorted (has the drop)
            // Minimum is in right half
            // mid itself is too large (part of sorted left)
            left = mid + 1;
        } else {
            // Left half is unsorted (or all sorted)
            // Minimum is in left half (or at mid)
            // mid could be the minimum
            right = mid;
        }
    }
    
    // When left == right, we found the minimum
    return nums[left];
}

// Time: O(log n)
// Space: O(1)
```

**Visualization:**
```
nums = [4,5,6,7,0,1,2]

Iteration 1:
[4, 5, 6, 7, 0, 1, 2]
 L        M        R

nums[mid]=7 > nums[right]=2? YES
Minimum in right half → left = mid + 1

Iteration 2:
[4, 5, 6, 7, 0, 1, 2]
              L  M  R
              
nums[mid]=1 > nums[right]=2? NO
Minimum in left half (or at mid) → right = mid

Iteration 3:
[4, 5, 6, 7, 0, 1, 2]
              L
              M,R
              
left == right, FOUND! Return 0
```

**Why Compare with nums[right]?**
```
In rotated array:
[larger values...] [smaller values...]
                 ↑ (pivot = minimum)

If nums[mid] > nums[right]:
  mid is in "larger values" section
  minimum must be to the right

If nums[mid] <= nums[right]:
  mid is in "smaller values" section
  minimum is at mid or to the left
```

---

### Problem 4: Find First and Last Position ⭐
**LeetCode 34 - Medium**

**Description:** Given an array of integers `nums` sorted in non-decreasing order, find the starting and ending position of a given `target` value. If target is not found, return `[-1, -1]`. Must write O(log n) algorithm.

**Example:**
```
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]

Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]

Input: nums = [], target = 0
Output: [-1,-1]
```

**Solution:**
```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> result = {-1, -1};
        
        // Find left boundary (first occurrence)
        result[0] = findLeft(nums, target);
        
        // If target not found, no need to find right boundary
        if (result[0] == -1) {
            return result;
        }
        
        // Find right boundary (last occurrence)
        result[1] = findRight(nums, target);
        
        return result;
    }
    
private:
    // Find leftmost (first) occurrence of target
    int findLeft(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1;
        int result = -1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            
            if (nums[mid] == target) {
                result = mid;      // Found one, but keep searching left
                right = mid - 1;   // Try to find earlier occurrence
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        return result;
    }
    
    // Find rightmost (last) occurrence of target
    int findRight(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1;
        int result = -1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            
            if (nums[mid] == target) {
                result = mid;      // Found one, but keep searching right
                left = mid + 1;    // Try to find later occurrence
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        return result;
    }
};

// Time: O(log n) - two binary searches
// Space: O(1)
```

**Visualization:**
```
nums = [5,7,7,8,8,10], target = 8

Finding LEFT boundary:

Iteration 1:
[5, 7, 7, 8, 8, 10]
 L        M       R
nums[mid]=7 < 8, search right → left = mid + 1

Iteration 2:
[5, 7, 7, 8, 8, 10]
           L  M  R
nums[mid]=8 = 8, found! But keep searching left
result = 4, right = mid - 1

Iteration 3:
[5, 7, 7, 8, 8, 10]
           L
           M,R
nums[mid]=8 = 8, found earlier occurrence!
result = 3, right = mid - 1

Iteration 4:
[5, 7, 7, 8, 8, 10]
        R  L
left > right, stop. Return result = 3

Finding RIGHT boundary (similar process):
Final result = 4
```

**Alternative: Using Lower/Upper Bound Pattern:**
```cpp
vector<int> searchRange(vector<int>& nums, int target) {
    int left = lower_bound(nums, target);    // First >= target
    int right = upper_bound(nums, target);   // First > target
    
    // Check if target exists
    if (left == nums.size() || nums[left] != target) {
        return {-1, -1};
    }
    
    return {left, right - 1};  // right-1 because upper_bound returns first > target
}

int lower_bound(vector<int>& nums, int target) {
    int left = 0, right = nums.size();
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) left = mid + 1;
        else right = mid;
    }
    return left;
}

int upper_bound(vector<int>& nums, int target) {
    int left = 0, right = nums.size();
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] <= target) left = mid + 1;  // Note: <= instead of <
        else right = mid;
    }
    return left;
}
```

---

### Problem 5: Koko Eating Bananas (Binary Search on Answer) ⭐
**LeetCode 875 - Medium**

**Description:** Koko loves to eat bananas. There are `n` piles of bananas, the `i`th pile has `piles[i]` bananas. Koko can decide her bananas-per-hour eating speed `k`. Each hour, she chooses a pile and eats `k` bananas from it. If the pile has less than `k` bananas, she eats all and won't eat more during that hour.

Return the minimum integer `k` such that she can eat all bananas within `h` hours.

**Example:**
```
Input: piles = [3,6,7,11], h = 8
Output: 4
Explanation:
  Speed 4: 3/4=1hr, 6/4=2hr, 7/4=2hr, 11/4=3hr = 8hrs total ✓
  Speed 3: Would take 1+2+3+4 = 10hrs (too slow) ✗

Input: piles = [30,11,23,4,20], h = 5
Output: 30
Explanation: Must eat each pile in 1 hour max
```

**Solution:**
```cpp
class Solution {
public:
    int minEatingSpeed(vector<int>& piles, int h) {
        // INSIGHT: This is NOT searching in piles array
        // We're searching in the ANSWER SPACE [1, max_pile]
        // Finding minimum speed that works
        
        // Minimum possible speed: 1 banana/hour
        int left = 1;
        
        // Maximum possible speed: largest pile (eat any pile in 1 hour)
        int right = *max_element(piles.begin(), piles.end());
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            
            // Check if eating at speed 'mid' finishes in time
            if (canFinish(piles, mid, h)) {
                // mid works, but try smaller speed
                right = mid;
            } else {
                // mid is too slow, need faster speed
                left = mid + 1;
            }
        }
        
        return left;  // Minimum speed that works
    }
    
private:
    // Check if Koko can finish all bananas at given speed within h hours
    bool canFinish(vector<int>& piles, int speed, int h) {
        long long hours = 0;  // Use long long to avoid overflow
        
        for (int pile : piles) {
            // Hours needed for this pile
            // Ceiling division: (pile + speed - 1) / speed
            // Or: (pile - 1) / speed + 1
            hours += (pile + speed - 1) / speed;
            
            // Early termination if already exceeded h
            if (hours > h) return false;
        }
        
        return hours <= h;
    }
};

// Time: O(n × log(max_pile))
//   - Binary search: O(log(max_pile))
//   - Each check: O(n) to iterate through piles
// Space: O(1)
```

**Visualization:**
```
piles = [3,6,7,11], h = 8

Search space: speed from 1 to 11

Iteration 1:
Speed range: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
              L              M                 R
              
Try speed=6:
  Pile 3: ⌈3/6⌉ = 1 hour
  Pile 6: ⌈6/6⌉ = 1 hour
  Pile 7: ⌈7/6⌉ = 2 hours
  Pile 11: ⌈11/6⌉ = 2 hours
  Total: 1+1+2+2 = 6 hours <= 8 ✓
  
Speed 6 works, try slower → right = 6

Iteration 2:
Speed range: [1, 2, 3, 4, 5, 6]
              L        M     R
              
Try speed=3:
  Total: 1+2+3+4 = 10 hours > 8 ✗
  
Speed 3 too slow → left = 4

Iteration 3:
Speed range: [4, 5, 6]
              L  M  R
              
Try speed=5:
  Total: 1+2+2+3 = 8 hours <= 8 ✓
  
Speed 5 works → right = 5

Iteration 4:
Speed range: [4, 5]
              L
              M,R
              
Try speed=4:
  Total: 1+2+2+3 = 8 hours <= 8 ✓
  
Speed 4 works → right = 4

Iteration 5:
left == right = 4, FOUND!
```

**Key Insight - Binary Search on Answer:**
```
Instead of searching in the INPUT (piles array),
we search in the OUTPUT (possible speeds).

Monotonic property:
- If speed X works, all speeds > X also work
- If speed X fails, all speeds < X also fail

This allows binary search on answer space!

Pattern applies to many problems:
- "Minimum capacity to ship in D days"
- "Split array largest sum"
- "Magnetic force between balls"
```

---

### Problem 6: Median of Two Sorted Arrays ⭐⭐
**LeetCode 4 - Hard**

**Description:** Given two sorted arrays `nums1` and `nums2`, return the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

**Example:**
```
Input: nums1 = [1,3], nums2 = [2]
Output: 2.0
Explanation: merged array = [1,2,3], median = 2

Input: nums1 = [1,2], nums2 = [3,4]
Output: 2.5
Explanation: merged array = [1,2,3,4], median = (2+3)/2 = 2.5
```

**Solution:**
```cpp
double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
    // STRATEGY: Binary search on the SMALLER array
    // Ensure nums1 is the smaller array
    if (nums1.size() > nums2.size()) {
        return findMedianSortedArrays(nums2, nums1);
    }
    
    int m = nums1.size();
    int n = nums2.size();
    int totalLeft = (m + n + 1) / 2;  // Elements in left partition
    
    // Binary search on nums1
    int left = 0;
    int right = m;
    
    while (left <= right) {
        // Partition nums1 at i (i elements on left)
        int i = left + (right - left) / 2;
        
        // Partition nums2 at j (to balance left/right partitions)
        int j = totalLeft - i;
        
        // Get the 4 boundary values
        int nums1LeftMax = (i == 0) ? INT_MIN : nums1[i - 1];
        int nums1RightMin = (i == m) ? INT_MAX : nums1[i];
        int nums2LeftMax = (j == 0) ? INT_MIN : nums2[j - 1];
        int nums2RightMin = (j == n) ? INT_MAX : nums2[j];
        
        // Check if partition is correct
        if (nums1LeftMax <= nums2RightMin && 
            nums2LeftMax <= nums1RightMin) {
            // FOUND correct partition!
            
            // Calculate median
            if ((m + n) % 2 == 1) {
                // Odd total length: median is max of left partition
                return max(nums1LeftMax, nums2LeftMax);
            } else {
                // Even total length: median is average of middle two
                int maxLeft = max(nums1LeftMax, nums2LeftMax);
                int minRight = min(nums1RightMin, nums2RightMin);
                return (maxLeft + minRight) / 2.0;
            }
        } else if (nums1LeftMax > nums2RightMin) {
            // nums1 left partition is too large
            // Move partition in nums1 to the left
            right = i - 1;
        } else {
            // nums1 left partition is too small
            // Move partition in nums1 to the right
            left = i + 1;
        }
    }
    
    return 0.0;  // Should never reach here
}

// Time: O(log(min(m,n))) - binary search on smaller array
// Space: O(1)
```

**Detailed Explanation:**

**Concept - Partition Arrays:**
```
Goal: Partition both arrays so that:
1. Left partition has (m+n+1)/2 elements
2. All elements in left <= all elements in right

Example: nums1=[1,3,5], nums2=[2,4,6,7,8]
Total = 8 elements, need 4 in left partition

nums1: [1, 3 | 5]        i=2 (2 elements in left)
nums2: [2, 4 | 6, 7, 8]  j=2 (2 elements in left)

Left partition:  [1,3,2,4]    (4 elements)
Right partition: [5,6,7,8]    (4 elements)

Check condition:
  max(left1) = 3, min(right1) = 5
  max(left2) = 4, min(right2) = 6
  
  Is 3 <= 6? YES
  Is 4 <= 5? YES
  ✓ Valid partition!

Median = (max(3,4) + min(5,6)) / 2 = (4+5)/2 = 4.5
```

**Visualization:**
```
nums1 = [1,3], nums2 = [2,4,5,6]
Total = 6, need 3 in left partition

Binary search on nums1 (smaller array):

Iteration 1:
Try i=1 (1 element from nums1 in left)
Then j=3-1=2 (2 elements from nums2 in left)

nums1: [1 | 3]
nums2: [2, 4 | 5, 6]

Left:  [1, 2, 4]  max = 4
Right: [3, 5, 6]  min = 3

Check: Is 1 <= 5? YES, Is 4 <= 3? NO ✗
nums1 left is too large → right = i-1 = 0

Iteration 2:
Try i=0 (0 elements from nums1 in left)
Then j=3-0=3 (3 elements from nums2 in left)

nums1: [ | 1, 3]
nums2: [2, 4, 5 | 6]

Left:  [2, 4, 5]  max = 5
Right: [1, 3, 6]  min = 1

Check: Is -∞ <= 6? YES, Is 5 <= 1? NO ✗
nums2 left is too large → left = i+1 = 1

Iteration 3:
Try i=1, j=2

nums1: [1 | 3]
nums2: [2, 4 | 5, 6]

Left:  [1, 2, 4]  max = 4
Right: [3, 5, 6]  min = 3

Already checked in iteration 1...

After binary search, we find:
Best partition: i=1, j=2
Median = (max(1,4) + min(3,5)) / 2 = (4+3)/2 = 3.5
```

**Why This is Hard:**
- Not searching for a value, searching for a **partition point**
- Must maintain balance between two arrays
- Edge cases: empty partition, odd/even total length
- Requires understanding of median as partition concept

---

## 🎯 Pattern Recognition

### Standard Binary Search:
```
✓ Sorted array
✓ Find exact value
✓ Return index or -1
→ Use Template 1
```

### Finding Boundaries:
```
✓ Find first/last occurrence
✓ Insert position
✓ Words like "first", "last", "at least"
→ Use Template 2 or 3
```

### Modified Binary Search:
```
✓ Rotated sorted array
✓ Search in 2D matrix
✓ Still O(log n) possible
→ Determine sorted half, adjust logic
```

### Binary Search on Answer:
```
✓ "Minimum X such that..."
✓ "Maximum X such that..."
✓ Can verify if X works in O(n)
✓ Monotonic: if X works, X+1 works (or vice versa)
→ Use Template 4, search answer space
```

---

## 💡 Common Pitfalls

### 1. Integer Overflow
```cpp
// ❌ Wrong
int mid = (left + right) / 2;  // Overflow if left+right > INT_MAX

// ✅ Correct
int mid = left + (right - left) / 2;
```

### 2. Infinite Loop
```cpp
// ❌ Wrong: Can cause infinite loop
while (left < right) {
    int mid = (left + right) / 2;
    if (condition) left = mid;  // If mid=left, infinite loop!
    else right = mid - 1;
}

// ✅ Correct: Ensure progress
while (left < right) {
    int mid = left + (right - left) / 2;
    if (condition) left = mid + 1;  // Always make progress
    else right = mid;
}
```

### 3. Boundary Confusion (< vs <=)
```cpp
// Template 1: Use <=
while (left <= right)  // Can check single element

// Template 2/3: Use <
while (left < right)   // Terminates when left==right
```

### 4. Return Value
```cpp
// Template 1: Check during loop
if (arr[mid] == target) return mid;
return -1;  // After loop

// Template 2: Return left
return left;  // After loop exits

// Template 3: Return left-1
return left - 1;  // Adjust for right boundary
```

---

## ⚡ Quick Reference

```cpp
// TEMPLATE 1: Find exact value
int left = 0, right = n - 1;
while (left <= right) {
    int mid = left + (right - left) / 2;
    if (arr[mid] == target) return mid;
    if (arr[mid] < target) left = mid + 1;
    else right = mid - 1;
}
return -1;

// TEMPLATE 2: Find left boundary (first occurrence)
int left = 0, right = n;
while (left < right) {
    int mid = left + (right - left) / 2;
    if (arr[mid] < target) left = mid + 1;
    else right = mid;
}
return left;

// TEMPLATE 3: Binary search on answer space
int left = minAnswer, right = maxAnswer;
while (left < right) {
    int mid = left + (right - left) / 2;
    if (isFeasible(mid)) right = mid;  // Try smaller
    else left = mid + 1;                // Need larger
}
return left;

// ROTATED ARRAY
if (nums[left] <= nums[mid]) {
    // Left half sorted
    if (nums[left] <= target && target < nums[mid])
        search left;
    else search right;
} else {
    // Right half sorted
    if (nums[mid] < target && target <= nums[right])
        search right;
    else search left;
}
```

---

## 🎓 Key Takeaways

1. **Always avoid overflow**: Use `left + (right - left) / 2`
2. **Know your template**: Standard vs boundary vs answer space
3. **Watch the loop condition**: `<=` vs `<` matters!
4. **Rotated arrays**: Identify sorted half first
5. **Binary search on answer**: If can verify in O(n), can optimize with binary search
6. **Median problem**: Think of it as finding partition, not value

**Master these 6 problems and you'll handle any binary search variation! 🚀**