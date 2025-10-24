# Two Pointers & Sliding Window Cheatsheet
## Essential Patternn

---

# Pattern 1: Two Pointers

## 🎯 When to Use
- Array/string with **sorted or sequential** property
- Finding **pairs, triplets** that meet condition
- **Removing duplicates** in-place
- **Reversing** or **reordering** elements
- **Fast/slow pointer** for linked lists

## ⏱️ Time Complexity: O(n) or O(n²)

---

## Template 1: Opposite Direction Pointers

```cpp
// Use when: Need to find pair/triplet in sorted array
int left = 0, right = n - 1;

while (left < right) {
    int sum = arr[left] + arr[right];
    
    if (sum == target) {
        // Found the pair
        return {left, right};
    } else if (sum < target) {
        left++;   // Need larger sum
    } else {
        right--;  // Need smaller sum
    }
}
```

---

## Template 2: Fast/Slow Pointers (Same Direction)

```cpp
// Use when: Removing elements, partitioning, or cycle detection
int slow = 0;  // Position to place next valid element

for (int fast = 0; fast < n; fast++) {
    if (isValid(arr[fast])) {
        arr[slow] = arr[fast];  // Keep this element
        slow++;
    }
}
// slow is now the length of valid elements
```

---

## 📚 Essential Problems with Detailed Solutions

### Problem 1: Two Sum II (Sorted Array)
**LeetCode 167 - Medium**

**Description:** Given a **1-indexed** array of integers `numbers` that is already **sorted in non-decreasing order**, find two numbers such that they add up to a specific `target` number. Return the indices (1-indexed) of the two numbers.

**Example:**
```
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explanation: 2 + 7 = 9, return indices 1 and 2
```

**Solution:**
```cpp
vector<int> twoSum(vector<int>& numbers, int target) {
    int left = 0, right = numbers.size() - 1;
    
    while (left < right) {
        int sum = numbers[left] + numbers[right];
        
        if (sum == target) {
            // Found! Return 1-indexed positions
            return {left + 1, right + 1};
        } else if (sum < target) {
            // Current sum too small, need larger number
            // Move left pointer right to get bigger value
            left++;
        } else {
            // Current sum too large, need smaller number
            // Move right pointer left to get smaller value
            right--;
        }
    }
    
    return {-1, -1};  // No solution found
}

// Time: O(n) - each pointer moves at most n times
// Space: O(1) - only using two pointers
```

**Why Two Pointers?**
- Array is **sorted** ✓
- Need to find **pair** with specific sum ✓
- Can eliminate half search space each iteration ✓

---

### Problem 2: 3Sum ⭐
**LeetCode 15 - Medium**

**Description:** Given an integer array `nums`, return all unique triplets `[nums[i], nums[j], nums[k]]` such that `i != j`, `i != k`, and `j != k`, and `nums[i] + nums[j] + nums[k] == 0`.

**Example:**
```
Input: nums = [-1,0,1,2,-1,-4]
Output: [[-1,-1,2],[-1,0,1]]
Explanation: 
  -1 + 0 + 1 = 0
  -1 + -1 + 2 = 0
```

**Solution:**
```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    vector<vector<int>> result;
    int n = nums.size();
    
    // STEP 1: Sort array to enable two-pointer technique
    sort(nums.begin(), nums.end());
    
    // STEP 2: Fix first number, find other two using two pointers
    for (int i = 0; i < n - 2; i++) {
        // Skip duplicate first numbers to avoid duplicate triplets
        if (i > 0 && nums[i] == nums[i-1]) continue;
        
        // Now find two numbers that sum to -nums[i]
        int target = -nums[i];
        int left = i + 1;      // Start after current element
        int right = n - 1;     // Start from end
        
        while (left < right) {
            int sum = nums[left] + nums[right];
            
            if (sum == target) {
                // Found a triplet!
                result.push_back({nums[i], nums[left], nums[right]});
                
                // Skip duplicates for left pointer
                while (left < right && nums[left] == nums[left + 1]) {
                    left++;
                }
                // Skip duplicates for right pointer
                while (left < right && nums[right] == nums[right - 1]) {
                    right--;
                }
                
                // Move both pointers inward
                left++;
                right--;
            } else if (sum < target) {
                left++;   // Need larger sum
            } else {
                right--;  // Need smaller sum
            }
        }
    }
    
    return result;
}

// Time: O(n²) - O(n log n) for sort + O(n) outer loop × O(n) inner two pointers
// Space: O(1) - excluding output array
```

**Key Insights:**
- **Reduce 3Sum to 2Sum**: Fix one number, find other two
- **Sorting enables two pointers**: Can move left/right based on sum
- **Skip duplicates**: Critical to avoid duplicate triplets
- **Pattern**: Fix one, use two pointers on rest

---

### Problem 3: Container With Most Water
**LeetCode 11 - Medium**

**Description:** Given `n` non-negative integers `height` where each represents a point at coordinate `(i, height[i])`. Find two lines that together with the x-axis form a container that holds the most water.

**Example:**
```
Input: height = [1,8,6,2,5,4,8,3,7]
Output: 49
Explanation: Lines at index 1 (height=8) and index 8 (height=7)
             form container with area = 7 * (8-1) = 49
```

**Visual:**
```
Height: [1, 8, 6, 2, 5, 4, 8, 3, 7]
Index:   0  1  2  3  4  5  6  7  8

     8 |    █           █
     7 |    █           █     █
     6 |    █  █        █     █
     5 |    █  █     █  █     █
       |________________█_____█
       0  1  2  3  4  5  6  7  8
       
Best: height[1]=8 and height[8]=7
Area = min(8,7) × (8-1) = 7 × 7 = 49
```

**Solution:**
```cpp
int maxArea(vector<int>& height) {
    int left = 0;
    int right = height.size() - 1;
    int maxWater = 0;
    
    while (left < right) {
        // Calculate current area
        // Area = width × height
        // Width = distance between pointers
        // Height = minimum of the two lines (water level)
        int width = right - left;
        int h = min(height[left], height[right]);
        int area = width * h;
        
        maxWater = max(maxWater, area);
        
        // KEY INSIGHT: Move the pointer with smaller height
        // Why? The width is decreasing as we move inward.
        // To potentially get larger area, we need taller height.
        // Moving the taller pointer can only decrease area
        // (width decreases, height stays same or decreases).
        // Moving shorter pointer gives chance for taller height.
        
        if (height[left] < height[right]) {
            left++;   // Left is bottleneck, try to find taller
        } else {
            right--;  // Right is bottleneck, try to find taller
        }
    }
    
    return maxWater;
}

// Time: O(n) - each pointer moves inward once
// Space: O(1) - only using pointers and variables
```

**Why This Works:**
- Start with **maximum width** (furthest apart)
- As we move inward, **width decreases**
- Only way to increase area: find **taller height**
- **Always move shorter line** - it's the bottleneck
- Greedy approach works because we can't do better by moving taller line

---

### Problem 4: Remove Nth Node From End (Fast/Slow)
**LeetCode 19 - Medium**

**Description:** Given the head of a linked list, remove the `n`th node from the end of the list and return its head.

**Example:**
```
Input: head = [1,2,3,4,5], n = 2
Output: [1,2,3,5]
Explanation: Remove 4 (2nd from end)

Before: 1 -> 2 -> 3 -> 4 -> 5
After:  1 -> 2 -> 3 -> 5
```

**Solution:**
```cpp
ListNode* removeNthFromEnd(ListNode* head, int n) {
    // Use dummy node to handle edge case of removing head
    ListNode* dummy = new ListNode(0);
    dummy->next = head;
    
    // TECHNIQUE: Two pointers with n-gap
    ListNode* fast = dummy;
    ListNode* slow = dummy;
    
    // STEP 1: Move fast pointer n+1 steps ahead
    // Why n+1? So slow ends up at node BEFORE the one to delete
    for (int i = 0; i <= n; i++) {
        fast = fast->next;
    }
    
    // STEP 2: Move both pointers until fast reaches end
    // When fast reaches end, slow is at node before target
    while (fast != nullptr) {
        fast = fast->next;
        slow = slow->next;
    }
    
    // STEP 3: Delete the nth node from end
    ListNode* toDelete = slow->next;
    slow->next = slow->next->next;
    delete toDelete;
    
    ListNode* result = dummy->next;
    delete dummy;
    return result;
}

// Time: O(n) - single pass through list
// Space: O(1) - only using pointers
```

**Visualization:**
```
List: D -> 1 -> 2 -> 3 -> 4 -> 5 -> null  (n=2)

Step 1: Move fast n+1=3 steps
D -> 1 -> 2 -> 3 -> 4 -> 5 -> null
↑              ↑
slow          fast

Step 2: Move both until fast reaches null
D -> 1 -> 2 -> 3 -> 4 -> 5 -> null
               ↑              ↑
              slow          fast

Step 3: slow is before target, delete slow->next
D -> 1 -> 2 -> 3 -> 5 -> null
```

**Key Pattern: N-Gap Technique**
- Maintain **gap of n** between pointers
- When fast reaches end, slow is at **nth from end**
- Works in **one pass** without knowing list length

---

### Problem 5: Linked List Cycle II (Floyd's Algorithm)
**LeetCode 142 - Medium**

**Description:** Given head of a linked list, return the node where the cycle begins. If no cycle, return `null`.

**Example:**
```
Input: head = [3,2,0,-4], pos = 1 (cycle starts at index 1)

    3 -> 2 -> 0 -> -4
         ↑__________|
         
Output: Node with value 2
```

**Solution:**
```cpp
ListNode* detectCycle(ListNode* head) {
    if (!head || !head->next) return nullptr;
    
    // PHASE 1: Detect if cycle exists using fast/slow pointers
    ListNode* slow = head;
    ListNode* fast = head;
    
    // Fast moves 2x speed of slow
    // If cycle exists, they will meet inside the cycle
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        
        if (slow == fast) {
            // CYCLE DETECTED! Now find the start of cycle
            
            // PHASE 2: Find cycle start
            // Mathematical proof: Distance from head to cycle start
            // equals distance from meeting point to cycle start
            
            ListNode* ptr1 = head;        // Start from head
            ListNode* ptr2 = slow;        // Start from meeting point
            
            // Move both at same speed
            // They will meet at cycle start!
            while (ptr1 != ptr2) {
                ptr1 = ptr1->next;
                ptr2 = ptr2->next;
            }
            
            return ptr1;  // This is the cycle start
        }
    }
    
    // No cycle found
    return nullptr;
}

// Time: O(n) - at most 2 passes through the list
// Space: O(1) - only using pointers
```

**Why This Works - The Math:**
```
Let's say:
- Distance from head to cycle start = a
- Distance from cycle start to meeting point = b
- Remaining cycle length = c

When slow and fast meet:
- Slow traveled: a + b
- Fast traveled: a + b + c + b (went around cycle once more)

Since fast moves 2x speed:
2(a + b) = a + b + c + b
2a + 2b = a + 2b + c
a = c

Therefore: Distance from head to start = Distance from meeting to start
```

**Visualization:**
```
    a          b        c
head -> ... -> START -> ... -> MEET -> ... -> START
                ↑________________↑_______________|
                
Phase 1: slow and fast meet at MEET
Phase 2: ptr1 from head, ptr2 from MEET
         Both travel distance 'a' to reach START
```

---

# Pattern 2: Sliding Window

## 🎯 When to Use
- Finding **subarray/substring** with specific property
- **Contiguous** elements only
- Asking for **min/max** length or **count**
- Keywords: "substring", "subarray", "consecutive", "contiguous"

## ⏱️ Time Complexity: O(n)

---

## Template 1: Variable Size Window

```cpp
// Use when: Finding longest/shortest subarray with condition
int left = 0;
int maxLength = 0;

for (int right = 0; right < n; right++) {
    // Expand window: add arr[right]
    addToWindow(arr[right]);
    
    // Shrink window while invalid
    while (windowInvalid()) {
        removeFromWindow(arr[left]);
        left++;
    }
    
    // Update result with current valid window
    maxLength = max(maxLength, right - left + 1);
}
```

---

## Template 2: Fixed Size Window

```cpp
// Use when: Window size k is given
for (int right = 0; right < n; right++) {
    // Add current element
    addToWindow(arr[right]);
    
    // Once window reaches size k
    if (right >= k - 1) {
        processWindow();  // Check/update result
        
        // Remove leftmost element
        removeFromWindow(arr[right - k + 1]);
    }
}
```

---

## 📚 Essential Problems with Detailed Solutions

### Problem 1: Longest Substring Without Repeating Characters ⭐
**LeetCode 3 - Medium**

**Description:** Given a string `s`, find the length of the **longest substring** without repeating characters.

**Example:**
```
Input: s = "abcabcbb"
Output: 3
Explanation: "abc" is longest without repeats

Input: s = "bbbbb"
Output: 1
Explanation: "b" is longest

Input: s = "pwwkew"
Output: 3
Explanation: "wke" is longest
```

**Solution:**
```cpp
int lengthOfLongestSubstring(string s) {
    // Track characters in current window
    unordered_set<char> window;
    
    int left = 0;
    int maxLen = 0;
    
    // Expand window with right pointer
    for (int right = 0; right < s.length(); right++) {
        char c = s[right];
        
        // Shrink window until no duplicate
        // Keep removing from left until we can add s[right]
        while (window.count(c)) {
            // s[right] is duplicate, remove s[left]
            window.erase(s[left]);
            left++;
        }
        
        // Now window has no duplicates, add current char
        window.insert(c);
        
        // Update max length
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}

// Time: O(n) - each char added and removed at most once
// Space: O(min(n, 26)) - at most 26 unique lowercase letters
```

**Visualization:**
```
s = "abcabcbb"

Step 1: right=0, left=0
Window: [a]         Length: 1
        ^

Step 2: right=1, left=0
Window: [ab]        Length: 2
        ^^

Step 3: right=2, left=0
Window: [abc]       Length: 3
        ^^^

Step 4: right=3, left=0
Window: [abca] - DUPLICATE 'a'!
        ^^^
Shrink: Remove 'a', left=1
Window: [bca]       Length: 3
         ^^^

Step 5: right=4, left=1
Window: [bcab] - DUPLICATE 'b'!
Shrink: Remove 'b', left=2
        Remove 'c', left=3
Window: [ab]        Length: 2
           ^^
```

**Alternative Solution with HashMap (tracks last seen index):**
```cpp
int lengthOfLongestSubstring(string s) {
    // Map: char -> last seen index
    unordered_map<char, int> lastSeen;
    
    int left = 0;
    int maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s[right];
        
        // If we've seen this char and it's in current window
        if (lastSeen.count(c) && lastSeen[c] >= left) {
            // Move left to position after last occurrence
            left = lastSeen[c] + 1;
        }
        
        // Update last seen position
        lastSeen[c] = right;
        
        // Update max length
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

---

### Problem 2: Minimum Window Substring ⭐⭐
**LeetCode 76 - Hard**

**Description:** Given strings `s` and `t`, return the **minimum window substring** of `s` such that every character in `t` (including duplicates) is included. If no such substring exists, return empty string.

**Example:**
```
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
Explanation: Minimum window containing A, B, C

Input: s = "a", t = "a"
Output: "a"

Input: s = "a", t = "aa"
Output: ""
Explanation: Need two 'a's but only have one
```

**Solution:**
```cpp
string minWindow(string s, string t) {
    // Edge case
    if (s.length() < t.length()) return "";
    
    // STEP 1: Count required characters from t
    unordered_map<char, int> required;
    for (char c : t) {
        required[c]++;
    }
    
    // Track characters in current window
    unordered_map<char, int> window;
    
    // How many unique chars in t we've satisfied
    int have = 0;              // Unique chars satisfied
    int need = required.size(); // Unique chars needed
    
    // Result variables
    int minLen = INT_MAX;
    int minStart = 0;
    
    int left = 0;
    
    // STEP 2: Expand window with right pointer
    for (int right = 0; right < s.length(); right++) {
        char c = s[right];
        
        // Add current char to window
        window[c]++;
        
        // Check if we've satisfied this character's requirement
        if (required.count(c) && window[c] == required[c]) {
            have++;
        }
        
        // STEP 3: Shrink window while still valid
        // Try to minimize window while containing all of t
        while (have == need) {
            // Update result if current window is smaller
            int currLen = right - left + 1;
            if (currLen < minLen) {
                minLen = currLen;
                minStart = left;
            }
            
            // Try to shrink from left
            char leftChar = s[left];
            window[leftChar]--;
            
            // Check if removing leftChar breaks requirement
            if (required.count(leftChar) && 
                window[leftChar] < required[leftChar]) {
                have--;
            }
            
            left++;
        }
    }
    
    // Return result
    return minLen == INT_MAX ? "" : s.substr(minStart, minLen);
}

// Time: O(|s| + |t|) - each char in s processed at most twice
// Space: O(|s| + |t|) - for hash maps
```

**Visualization:**
```
s = "ADOBECODEBANC"
t = "ABC"
required = {A:1, B:1, C:1}

Step 1: Expand until valid
right=0: A     window={A:1}         have=1, need=3
right=1: AD    window={A:1,D:1}     have=1, need=3
right=2: ADO   window={A:1,D:1,O:1} have=1, need=3
right=3: ADOB  window={A:1,D:1,O:1,B:1} have=2, need=3
right=4: ADOBE window={A:1,D:1,O:1,B:1,E:1} have=2, need=3
right=5: ADOBEC window={A:1,D:1,O:1,B:1,E:1,C:1} have=3 ✓

Step 2: Now valid! Try to shrink
Current min: "ADOBEC" (length 6)

Shrink left:
left=1: DOBEC - Missing A! Stop shrinking
right=6: DOBECO - have=2

Continue expanding...
right=12: CODEBANC - have=3 ✓
Shrink: ODEBANC, DEBANC, EBANC, BANC ✓
Result: "BANC" (length 4)
```

**Key Insights:**
- **Two hashmaps**: `required` (what we need) and `window` (what we have)
- **have/need counters**: Track satisfaction of unique chars
- **Expand** until valid, then **shrink** to minimize
- **Update result** only when window is valid

---

### Problem 3: Permutation in String
**LeetCode 567 - Medium**

**Description:** Given two strings `s1` and `s2`, return `true` if `s2` contains a permutation of `s1`, or `false` otherwise. In other words, return `true` if one of `s1`'s permutations is a substring of `s2`.

**Example:**
```
Input: s1 = "ab", s2 = "eidbaooo"
Output: true
Explanation: s2 contains "ba" which is a permutation of "ab"

Input: s1 = "ab", s2 = "eidboaoo"
Output: false
```

**Solution:**
```cpp
bool checkInclusion(string s1, string s2) {
    int n1 = s1.length(), n2 = s2.length();
    if (n1 > n2) return false;
    
    // APPROACH: Fixed size sliding window of size s1.length()
    // Check if any window has same character frequency as s1
    
    // Count frequency in s1
    vector<int> s1Freq(26, 0);
    for (char c : s1) {
        s1Freq[c - 'a']++;
    }
    
    // Sliding window frequency in s2
    vector<int> windowFreq(26, 0);
    
    // STEP 1: Build initial window of size n1
    for (int i = 0; i < n1; i++) {
        windowFreq[s2[i] - 'a']++;
    }
    
    // Check if first window matches
    if (s1Freq == windowFreq) return true;
    
    // STEP 2: Slide the window
    for (int right = n1; right < n2; right++) {
        // Add new char on right
        windowFreq[s2[right] - 'a']++;
        
        // Remove old char on left
        int left = right - n1;  // Left boundary of window
        windowFreq[s2[left] - 'a']--;
        
        // Check if current window matches
        if (s1Freq == windowFreq) {
            return true;
        }
    }
    
    return false;
}

// Time: O(n2) - slide through s2, O(26) comparison is constant
// Space: O(1) - fixed size arrays (26 chars)
```

**Optimized Solution (avoid array comparison):**
```cpp
bool checkInclusion(string s1, string s2) {
    int n1 = s1.length(), n2 = s2.length();
    if (n1 > n2) return false;
    
    vector<int> s1Freq(26, 0), windowFreq(26, 0);
    
    // Count chars in s1
    for (char c : s1) {
        s1Freq[c - 'a']++;
    }
    
    // Use 'matches' to track how many chars have correct frequency
    int matches = 0;
    
    // Build initial window
    for (int i = 0; i < n1; i++) {
        windowFreq[s2[i] - 'a']++;
    }
    
    // Count initial matches
    for (int i = 0; i < 26; i++) {
        if (s1Freq[i] == windowFreq[i]) {
            matches++;
        }
    }
    
    // If all 26 match, we found permutation
    if (matches == 26) return true;
    
    // Slide window
    for (int right = n1; right < n2; right++) {
        int left = right - n1;
        
        // Add right char
        int rIdx = s2[right] - 'a';
        windowFreq[rIdx]++;
        if (windowFreq[rIdx] == s1Freq[rIdx]) {
            matches++;
        } else if (windowFreq[rIdx] == s1Freq[rIdx] + 1) {
            matches--;  // Was matching, now exceeds
        }
        
        // Remove left char
        int lIdx = s2[left] - 'a';
        windowFreq[lIdx]--;
        if (windowFreq[lIdx] == s1Freq[lIdx]) {
            matches++;
        } else if (windowFreq[lIdx] == s1Freq[lIdx] - 1) {
            matches--;  // Was matching, now too few
        }
        
        if (matches == 26) return true;
    }
    
    return false;
}
```

**Visualization:**
```
s1 = "ab"
s2 = "eidbaooo"

s1Freq: a=1, b=1

Window size = 2

Window 1: "ei"  → e=1, i=1  ✗
Window 2: "id"  → i=1, d=1  ✗
Window 3: "db"  → d=1, b=1  ✗
Window 4: "ba"  → b=1, a=1  ✓ MATCH!
```

---

### Problem 4: Subarray Sum Equals K
**LeetCode 560 - Medium**

**Description:** Given an array of integers `nums` and an integer `k`, return the total number of subarrays whose sum equals to `k`.

**Example:**
```
Input: nums = [1,1,1], k = 2
Output: 2
Explanation: [1,1] at index 0-1 and [1,1] at index 1-2

Input: nums = [1,2,3], k = 3
Output: 2
Explanation: [1,2] and [3]
```

**Solution (Prefix Sum + HashMap):**
```cpp
int subarraySum(vector<int>& nums, int k) {
    // KEY IDEA: Use prefix sum with hashmap
    // If prefixSum[j] - prefixSum[i] = k
    // Then subarray from i+1 to j has sum k
    // Rearranging: prefixSum[i] = prefixSum[j] - k
    
    // Map: prefix sum -> frequency
    unordered_map<int, int> prefixCount;
    prefixCount[0] = 1;  // Empty prefix (important!)
    
    int count = 0;
    int prefixSum = 0;
    
    for (int num : nums) {
        // Update current prefix sum
        prefixSum += num;
        
        // Check if there exists a prefix sum that
        // when subtracted from current sum gives k
        // i.e., current - previous = k
        // so previous = current - k
        int target = prefixSum - k;
        
        if (prefixCount.count(target)) {
            // Found target prefix sum(s)!
            // Each occurrence gives us one valid subarray
            count += prefixCount[target];
        }
        
        // Add current prefix sum to map
        prefixCount[prefixSum]++;
    }
    
    return count;
}

// Time: O(n) - single pass
// Space: O(n) - hashmap of prefix sums
```

**Visualization:**
```
nums = [1, 2, 3, -2, 1], k = 3

Index:       0  1  2   3  4
nums:        1  2  3  -2  1
prefixSum:   1  3  6   4  5

prefixCount map evolution:

Initial: {0: 1}

i=0, num=1:
  prefixSum = 1
  target = 1 - 3 = -2 (not in map)
  map: {0:1, 1:1}

i=1, num=2:
  prefixSum = 3
  target = 3 - 3 = 0 (found! count++)
  Subarray [1,2] (index 0-1) sums to 3 ✓
  map: {0:1, 1:1, 3:1}

i=2, num=3:
  prefixSum = 6
  target = 6 - 3 = 3 (found! count++)
  Subarray [3] (index 2-2) sums to 3 ✓
  map: {0:1, 1:1, 3:1, 6:1}

i=3, num=-2:
  prefixSum = 4
  target = 4 - 3 = 1 (found! count++)
  Subarray [2,3,-2] (index 1-3) sums to 3 ✓
  map: {0:1, 1:1, 3:1, 6:1, 4:1}

i=4, num=1:
  prefixSum = 5
  target = 5 - 3 = 2 (not in map)
  map: {0:1, 1:1, 3:1, 6:1, 4:1, 5:1}

Result: count = 3
```

**Why prefixCount[0] = 1?**
```
Example: nums = [3], k = 3
Without prefixCount[0] = 1:
  prefixSum = 3
  target = 3 - 3 = 0
  0 not in map → count = 0 ✗

With prefixCount[0] = 1:
  prefixSum = 3
  target = 3 - 3 = 0
  0 in map → count = 1 ✓ (subarray [3])
```

---

## 🎯 Pattern Recognition Guide

### Choose Two Pointers When:
- ✅ Array/list is **sorted** or has sequential property
- ✅ Need to find **pairs/triplets**
- ✅ Looking for something from **both ends**
- ✅ **In-place** modification needed
- ✅ Finding **cycle** in linked list

### Choose Sliding Window When:
- ✅ Problem mentions **subarray/substring**
- ✅ Need **contiguous** elements
- ✅ Looking for **min/max** length
- ✅ **Counting** subarrays with property
- ✅ Keywords: "consecutive", "window of size k"

---

## 💡 Common Mistakes to Avoid

### Two Pointers:
1. **Forgetting to skip duplicates** in 3Sum
2. **Wrong boundary conditions** (< vs <=)
3. **Not handling edge cases** (empty array, single element)
4. **Integer overflow** when calculating sum

### Sliding Window:
1. **Not updating window correctly** when shrinking
2. **Forgetting to initialize** (e.g., prefixCount[0] = 1)
3. **Wrong window size calculation** (right - left + 1)
4. **Using when elements must be non-contiguous** (need different approach)

---

## ⚡ Quick Reference

```cpp
// TWO POINTERS - Opposite Direction
int left = 0, right = n - 1;
while (left < right) {
    if (condition) left++;
    else right--;
}

// TWO POINTERS - Same Direction (Fast/Slow)
int slow = 0;
for (int fast = 0; fast < n; fast++) {
    if (valid) arr[slow++] = arr[fast];
}

// SLIDING WINDOW - Variable Size
int left = 0;
for (int right = 0; right < n; right++) {
    add(arr[right]);
    while (invalid) remove(arr[left++]);
    updateMax(right - left + 1);
}

// SLIDING WINDOW - Fixed Size K
for (int i = 0; i < n; i++) {
    add(arr[i]);
    if (i >= k - 1) {
        process();
        remove(arr[i - k + 1]);
    }
}
```

---

**Master these patterns and you'll solve 30+ LeetCode problems! 🚀**
