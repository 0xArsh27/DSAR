# Sliding Window Technique

## What is Sliding Window?

Sliding window is an algorithmic technique where we maintain a subset (window) of elements as we slide through an array or string. It's particularly useful for solving subarray/substring problems with specific constraints.

## Why Use Sliding Window?

- **Efficiency:** Reduces time complexity from O(n²) or O(n³) to O(n)
- **Single Pass:** Processes data in one iteration
- **Memory Efficient:** Often uses O(1) or O(k) extra space
- **Real-world Applications:** Network packet analysis, signal processing, data streaming

## When to Use Sliding Window?

```
1. Problem asks for contiguous subarray/substring
2. Involves min/max/length calculations
3. Has constraints like "at most K", "without duplicates", etc.
4. Brute force would involve nested loops
```

---

## Core Concepts

### The Three Components

- **Window** - The current subset of elements being considered
- **Left Pointer** - Marks the start of the window
- **Right Pointer** - Marks the end of the window

### Two Types of Windows

**Type 1: Fixed Size Window**

```cpp
// Window size is constant
int windowSize = k;
for (int right = 0; right < n; right++) {
    // Add element at right to window
    // When window reaches size k, process it
    // Remove element at left when moving forward
}
```

**Type 2: Variable Size Window**

```cpp
// Window expands and shrinks based on conditions
int left = 0;
for (int right = 0; right < n; right++) {
    // Expand window by adding element at right
    // While condition is invalid, shrink window from left
    // Update answer when condition is valid
}
```

---

## Fundamental Patterns

### Pattern 1: Maximum Sum Subarray of Size K (Fixed Window)

```cpp
int maxSumSubarray(vector<int>& nums, int k) {
    if (nums.size() < k) return -1;
    
    // Calculate first window sum
    int windowSum = 0;
    for (int i = 0; i < k; i++) {
        windowSum += nums[i];
    }
    
    int maxSum = windowSum;
    
    // Slide the window
    for (int i = k; i < nums.size(); i++) {
        // Add new element, remove old element
        windowSum += nums[i] - nums[i - k];
        maxSum = max(maxSum, windowSum);
    }
    
    return maxSum;
}
```

**Time Complexity:** O(n)  
**Space Complexity:** O(1)

### Pattern 2: Longest Substring Without Repeating Characters (Variable Window)

```cpp
int lengthOfLongestSubstring(string s) {
    vector<int> lastSeen(256, -1); // Store last index of each character
    int left = 0, maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s[right];
        
        // If character seen before and within current window
        if (lastSeen[c] >= left) {
            left = lastSeen[c] + 1; // Move left past the duplicate
        }
        
        lastSeen[c] = right; // Update last seen position
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

**Key Insight:** When we find a duplicate, we don't need to move left one by one. We can jump directly to position after the previous occurrence.

### Pattern 3: Minimum Window Substring (Variable Window with Map)

```cpp
string minWindow(string s, string t) {
    if (s.empty() || t.empty()) return "";
    
    // Frequency map for characters in t
    unordered_map<char, int> targetFreq;
    for (char c : t) {
        targetFreq[c]++;
    }
    
    int left = 0, right = 0;
    int minLeft = 0, minLen = INT_MAX;
    int required = t.size(); // Characters needed to match t
    
    while (right < s.size()) {
        char c = s[right];
        
        // If this character is in t
        if (targetFreq.count(c)) {
            if (targetFreq[c] > 0) {
                required--; // We need one less character
            }
            targetFreq[c]--;
        }
        right++;
        
        // When we have all characters, try to shrink
        while (required == 0) {
            // Update minimum window
            if (right - left < minLen) {
                minLen = right - left;
                minLeft = left;
            }
            
            // Remove left character from window
            char leftChar = s[left];
            if (targetFreq.count(leftChar)) {
                targetFreq[leftChar]++;
                if (targetFreq[leftChar] > 0) {
                    required++; // We need this character again
                }
            }
            left++;
        }
    }
    
    return minLen == INT_MAX ? "" : s.substr(minLeft, minLen);
}
```

### Pattern 4: Longest Substring with At Most K Distinct Characters

```cpp
int lengthOfLongestSubstringKDistinct(string s, int k) {
    if (k == 0) return 0;
    
    unordered_map<char, int> freq;
    int left = 0, maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s[right];
        freq[c]++;
        
        // Shrink window if we have more than k distinct chars
        while (freq.size() > k) {
            char leftChar = s[left];
            freq[leftChar]--;
            if (freq[leftChar] == 0) {
                freq.erase(leftChar);
            }
            left++;
        }
        
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

---
## Advanced Techniques

### Technique 1: At Most K vs Exactly K Trick

For problems like "subarrays with exactly K distinct integers", we can use:

```
exactly(K) = atMost(K) - atMost(K-1)
```

```cpp
int subarraysWithKDistinct(vector<int>& nums, int k) {
    return atMostKDistinct(nums, k) - atMostKDistinct(nums, k - 1);
}

int atMostKDistinct(vector<int>& nums, int k) {
    if (k <= 0) return 0;
    
    unordered_map<int, int> freq;
    int left = 0, count = 0;
    
    for (int right = 0; right < nums.size(); right++) {
        freq[nums[right]]++;
        
        while (freq.size() > k) {
            freq[nums[left]]--;
            if (freq[nums[left]] == 0) {
                freq.erase(nums[left]);
            }
            left++;
        }
        
        // All subarrays ending at right with at most k distinct
        count += right - left + 1;
    }
    
    return count;
}
```

### Technique 2: Prefix Sum with Sliding Window

```cpp
// Number of subarrays with sum equal to K
int subarraySumEqualsK(vector<int>& nums, int k) {
    unordered_map<int, int> prefixSumCount;
    prefixSumCount[0] = 1; // Base case
    
    int sum = 0, count = 0;
    
    for (int num : nums) {
        sum += num;
        
        // If (sum - k) exists in map, we found subarrays
        if (prefixSumCount.count(sum - k)) {
            count += prefixSumCount[sum - k];
        }
        
        prefixSumCount[sum]++;
    }
    
    return count;
}
```

### Technique 3: Monotonic Queue for Sliding Window Maximum

```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    if (nums.empty() || k == 0) return {};
    
    deque<int> dq; // Stores indices, maintains decreasing order of values
    vector<int> result;
    
    for (int i = 0; i < nums.size(); i++) {
        // Remove indices outside window
        while (!dq.empty() && dq.front() <= i - k) {
            dq.pop_front();
        }
        
        // Remove smaller elements from back
        while (!dq.empty() && nums[dq.back()] <= nums[i]) {
            dq.pop_back();
        }
        
        dq.push_back(i);
        
        // Add to result when window is complete
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    
    return result;
}
```

**Why deque?** O(1) operations at both ends, maintains elements in decreasing order.

### Technique 4: Multiple Pointers within Window

```cpp
// Longest Substring with At Most Two Distinct Characters (optimized)
int lengthOfLongestSubstringTwoDistinct(string s) {
    if (s.empty()) return 0;
    
    unordered_map<char, int> lastSeen;
    int left = 0, maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        lastSeen[s[right]] = right;
        
        if (lastSeen.size() > 2) {
            // Find character with smallest last seen index
            int minIndex = right;
            char charToRemove;
            
            for (auto& [ch, idx] : lastSeen) {
                if (idx < minIndex) {
                    minIndex = idx;
                    charToRemove = ch;
                }
            }
            
            // Remove that character
            lastSeen.erase(charToRemove);
            left = minIndex + 1;
        }
        
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

---
## Problem Classification

### Category 1: Fixed Size Window Problems
- Maximum/minimum sum of subarray of size K
- First negative in every window of size K
- Count occurrences of anagrams
- Maximum of all subarrays of size K

### Category 2: Variable Size Window (Longest Subarray)
- Longest substring without repeating characters
- Longest substring with at most K distinct characters
- Longest subarray with sum ≤ K
- Longest subarray with ones after replacement

### Category 3: Variable Size Window (Shortest Subarray)
- Minimum window substring
- Smallest subarray with sum ≥ K
- Minimum size subarray sum

### Category 4: Count Problems
- Count subarrays with exactly K distinct integers
- Subarrays with product less than K
- Binary subarrays with sum K

### Category 5: String Pattern Matching
- Find all anagrams in a string
- Permutation in string
- Minimum window substring

---

## Templates & Code Patterns

### Template 1: Basic Variable Window

```cpp
int slidingWindowTemplate(vector<int>& nums, int k) {
    int left = 0, right = 0;
    int windowSum = 0;
    int result = 0;
    
    while (right < nums.size()) {
        // 1. Expand window (add nums[right])
        windowSum += nums[right];
        right++;
        
        // 2. Shrink window while condition is invalid
        while (windowConditionInvalid(windowSum, k)) {
            windowSum -= nums[left];
            left++;
        }
        
        // 3. Update result (window is valid here)
        result = max(result, right - left);
    }
    
    return result;
}
```

### Template 2: Frequency Map Window

```cpp
int slidingWindowWithMap(string s, int k) {
    unordered_map<char, int> freq;
    int left = 0, maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        // 1. Add current character to window
        freq[s[right]]++;
        
        // 2. Shrink window if invalid
        while (freq.size() > k) {
            freq[s[left]]--;
            if (freq[s[left]] == 0) {
                freq.erase(s[left]);
            }
            left++;
        }
        
        // 3. Update answer
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

### Template 3: Optimized Shrinking (Jump Left)

```cpp
int optimizedSlidingWindow(string s) {
    vector<int> lastSeen(256, -1);
    int left = 0, maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s[right];
        
        // If duplicate found, jump left pointer
        if (lastSeen[c] >= left) {
            left = lastSeen[c] + 1;
        }
        
        lastSeen[c] = right;
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

### Template 4: Fixed Window with Precalculation

```cpp
int fixedWindowK(vector<int>& nums, int k) {
    if (nums.size() < k) return 0;
    
    // Calculate first window
    int windowSum = 0;
    for (int i = 0; i < k; i++) {
        windowSum += nums[i];
    }
    
    int result = windowSum;
    
    // Slide window
    for (int i = k; i < nums.size(); i++) {
        windowSum += nums[i] - nums[i - k];
        result = operation(result, windowSum); // max/min/etc.
    }
    
    return result;
}
```

---
## Common Variations

### Variation 1: Circular Array Problems

```cpp
// Maximum sum circular subarray
int maxSubarraySumCircular(vector<int>& nums) {
    int total = 0;
    int maxSum = nums[0], curMax = 0;
    int minSum = nums[0], curMin = 0;
    
    for (int num : nums) {
        // Kadane's algorithm for max subarray
        curMax = max(curMax + num, num);
        maxSum = max(maxSum, curMax);
        
        // Kadane's algorithm for min subarray
        curMin = min(curMin + num, num);
        minSum = min(minSum, curMin);
        
        total += num;
    }
    
    // If all numbers are negative
    if (maxSum < 0) return maxSum;
    
    // max(case1: normal max, case2: circular max)
    return max(maxSum, total - minSum);
}
```

### Variation 2: Two Arrays Simultaneously

```cpp
// Minimum window substring covering characters from two strings
string minWindowTwoStrings(string s, string t1, string t2) {
    unordered_map<char, int> need1, need2;
    for (char c : t1) need1[c]++;
    for (char c : t2) need2[c]++;
    
    unordered_map<char, int> have1, have2;
    int left = 0, minLen = INT_MAX, start = 0;
    int satisfied1 = 0, satisfied2 = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s[right];
        
        // Update have maps
        if (need1.count(c)) {
            have1[c]++;
            if (have1[c] == need1[c]) satisfied1++;
        }
        if (need2.count(c)) {
            have2[c]++;
            if (have2[c] == need2[c]) satisfied2++;
        }
        
        // Shrink while both conditions satisfied
        while (satisfied1 == need1.size() && satisfied2 == need2.size()) {
            if (right - left + 1 < minLen) {
                minLen = right - left + 1;
                start = left;
            }
            
            char leftChar = s[left];
            if (need1.count(leftChar)) {
                if (have1[leftChar] == need1[leftChar]) satisfied1--;
                have1[leftChar]--;
            }
            if (need2.count(leftChar)) {
                if (have2[leftChar] == need2[leftChar]) satisfied2--;
                have2[leftChar]--;
            }
            left++;
        }
    }
    
    return minLen == INT_MAX ? "" : s.substr(start, minLen);
}
```

### Variation 3: Multiple Constraints

```cpp
// Subarrays with sum divisible by K
int subarraysDivByK(vector<int>& nums, int k) {
    vector<int> count(k, 0);
    count[0] = 1; // For sum 0
    
    int prefixSum = 0, result = 0;
    
    for (int num : nums) {
        prefixSum += num;
        
        // Handle negative remainders
        int remainder = (prefixSum % k + k) % k;
        
        // If we've seen this remainder before, we found divisible subarrays
        result += count[remainder];
        
        count[remainder]++;
    }
    
    return result;
}
```

---
## Optimization Tips

### Tip 1: Choose the Right Data Structure

```cpp
// For character frequency: vector<int>(128) or vector<int>(256)
vector<int> freq(128, 0); // ASCII
vector<int> freq(256, 0); // Extended ASCII

// For integer frequency with range:
if (range <= 10^5) use vector<int>
else use unordered_map<int, int>

// For maintaining order in window: deque
deque<int> dq; // For sliding window maximum
```

### Tip 2: Early Termination

```cpp
// If K is greater than total distinct characters possible
int longestSubstringKDistinct(string s, int k) {
    if (k <= 0) return 0;
    if (k >= s.length()) return s.length(); // Early return
    
    // Rest of the code...
}
```

### Tip 3: Precompute when Possible

```cpp
// Instead of computing sum repeatedly
int minSubArrayLen(int target, vector<int>& nums) {
    // Precompute prefix sums
    vector<int> prefix(nums.size() + 1, 0);
    for (int i = 0; i < nums.size(); i++) {
        prefix[i + 1] = prefix[i] + nums[i];
    }
    
    // Then use sliding window or binary search
    // ...
}
```

### Tip 4: Space Optimization for Frequency

```cpp
// Use integer instead of map when possible
int longestOnes(vector<int>& nums, int k) {
    int left = 0, right = 0;
    int zeroCount = 0; // Track only what matters
    int maxLen = 0;
    
    while (right < nums.size()) {
        if (nums[right] == 0) {
            zeroCount++;
        }
        
        while (zeroCount > k) {
            if (nums[left] == 0) {
                zeroCount--;
            }
            left++;
        }
        
        maxLen = max(maxLen, right - left + 1);
        right++;
    }
    
    return maxLen;
}
```

---
## Practice Problems

### Level 1: Basic Understanding
- Maximum Average Subarray I (Fixed window)
- Contains Duplicate II (Fixed window with set)
- Find All Anagrams in a String (Fixed window with frequency)
- Permutation in String (Fixed window check)

### Level 2: Pattern Application
- Longest Substring Without Repeating Characters
- Longest Substring with At Most K Distinct Characters
- Fruit Into Baskets (Same as above)
- Minimum Size Subarray Sum
- Subarray Product Less Than K

### Level 3: Advanced Variations
- Minimum Window Substring (Complex frequency tracking)
- Sliding Window Maximum (Monotonic deque)
- Count Subarrays With Exactly K Distinct Integers (AtMost trick)
- Longest Repeating Character Replacement (Max frequency tracking)
- Binary Subarrays With Sum (Prefix sum + map)

### Level 4: Mastery Level
- Subarrays with K Different Integers (Advanced counting)
- Maximum Points You Can Obtain from Cards (Two ends sliding window)
- Get Equal Substrings Within Budget (Binary search + sliding window)
- Longest Mountain in Array (Multiple phases)
- Constrained Subsequence Sum (DP + deque)

---

## Interview Guide

### Step-by-Step Problem Solving

**Step 1: Understand the Problem**

```
Ask yourself:
1. Is it a subarray/substring problem? ✓
2. Does it ask for min/max/length? ✓
3. Are there constraints (K distinct, sum ≥ target, etc.)? ✓
4. If yes to all, consider sliding window!
```

**Step 2: Identify Window Type**

```
Is window size fixed?
    Yes → Fixed window template
    No → Variable window template
    
What needs to be tracked in window?
    - Sum → maintain running sum
    - Distinct characters → use map/set
    - Frequency → use frequency array/map
    - Maximum/minimum → use deque/heap
```

**Step 3: Choose Data Structures**

```cpp
// Based on constraints:
if (alphabet size ≤ 256) → vector<int>(256)
if (value range small) → vector<int>
if (value range large) → unordered_map
if (need order) → deque
if (need quick max/min) → multiset or priority_queue
```

**Step 4: Implement Template**

```cpp
int solveProblem(vector<int>& nums, int k) {
    // 1. Initialize
    DataStructure window;
    int left = 0, result = 0;
    
    // 2. Expand window
    for (int right = 0; right < nums.size(); right++) {
        window.add(nums[right]);
        
        // 3. Shrink while invalid
        while (window.invalid(k)) {
            window.remove(nums[left]);
            left++;
        }
        
        // 4. Update result
        result = update(result, window);
    }
    
    return result;
}
```

**Step 5: Test Edge Cases**

```
Test with:
1. Empty input
2. Single element
3. K = 0, K = 1
4. All identical elements
5. All distinct elements
6. Large K (greater than possible)
7. Negative numbers (if allowed)
```