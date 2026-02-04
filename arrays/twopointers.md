# Two Pointers Technique

## Part 1: Core Concept Intuition

### What are Two Pointers?
Imagine you have two fingers pointing at different positions in an array/list. You move them intelligently based on conditions to solve problems efficiently.

### Why Use Two Pointers?
- **Brute Force:** O(n²) - Check all pairs
- **Two Pointers:** O(n) - One pass through array
- **Memory:** O(1) - No extra space needed

---

## Part 2: The 5 Fundamental Patterns 🖐️

### Pattern 1: Opposite Ends Pointers

**Template:**
```cpp
// Template
int left = 0, right = n-1;
while (left < right) {
    // Process elements at left and right
    // Move pointers based on condition
    if (condition) left++;
    else right--;
}
```

**Classic Example: Two Sum (Sorted Array)**

```cpp
vector<int> twoSum(vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    
    while (left < right) {
        int sum = nums[left] + nums[right];
        
        if (sum == target) {
            return {left, right};
        }
        else if (sum < target) {
            left++; // Need larger sum
        }
        else {
            right--; // Need smaller sum
        }
    }
    return {}; // No solution
}
```

### Pattern 2: Fast & Slow Pointers (Tortoise & Hare)

**Template:**
```cpp
// Template
int slow = 0, fast = 0;
while (fast < n) {
    // Slow moves conditionally
    // Fast moves every iteration
    if (condition) {
        // Process with slow
        slow++;
    }
    fast++;
}
```

**Example: Remove Duplicates from Sorted Array**

```cpp
int removeDuplicates(vector<int>& nums) {
    if (nums.empty()) return 0;
    
    int slow = 1; // Position to place next unique element
    
    for (int fast = 1; fast < nums.size(); fast++) {
        if (nums[fast] != nums[fast - 1]) {
            nums[slow] = nums[fast];
            slow++;
        }
    }
    return slow; // New length
}
```

### Pattern 3: Sliding Window (Special Two Pointers)

**Template:**
```cpp
// Template
int left = 0, right = 0;
while (right < n) {
    // Expand window
    window.add(nums[right]);
    right++;
    
    // Shrink window while condition invalid
    while (condition_to_shrink) {
        window.remove(nums[left]);
        left++;
    }
    
    // Update answer
}
```

**Example: Longest Substring Without Repeating Characters**

```cpp
int lengthOfLongestSubstring(string s) {
    vector<int> charIndex(256, -1); // Track last seen index
    int left = 0, maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        // If char seen before and within current window
        if (charIndex[s[right]] >= left) {
            left = charIndex[s[right]] + 1; // Move left past duplicate
        }
        charIndex[s[right]] = right; // Update last seen index
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

### Pattern 4: Three Pointers

**Template:**
```cpp
// Template (for 3-color sort/Dutch flag)
int low = 0, mid = 0, high = n-1;
while (mid <= high) {
    if (nums[mid] == 0) {
        swap(nums[low], nums[mid]);
        low++; mid++;
    }
    else if (nums[mid] == 1) {
        mid++;
    }
    else {
        swap(nums[mid], nums[high]);
        high--;
    }
}
```

### Pattern 5: Merge Two Sorted Arrays

**Template:**
```cpp
// Template
int i = 0, j = 0, k = 0;
while (i < m && j < n) {
    if (arr1[i] < arr2[j]) {
        result[k++] = arr1[i++];
    } else {
        result[k++] = arr2[j++];
    }
}
// Copy remaining elements
```

---

## Part 3: Mental Models & Decision Framework 🧠

### When to Use Which Pattern?

```
1. Array is SORTED and need to find pairs/triplets?
   → Opposite Ends Pointers

2. Need to detect cycles or find middle?
   → Fast & Slow Pointers

3. Need to find subarray/substring with some property?
   → Sliding Window

4. Array has 3 distinct values (like 0,1,2)?
   → Three Pointers (Dutch Flag)

5. Need to process two arrays simultaneously?
   → Parallel Pointers
```

### The "CHOOSE" Framework for Problem Solving

- **C** - Check if array is sorted (sort if allowed)
- **H** - How many pointers needed? (2, 3, or more)
- **O** - Opposite ends or same direction?
- **O** - Order of movement? (when to move which pointer)
- **S** - Stop condition? (while left < right, etc.)
- **E** - Edge cases? (empty, single element, duplicates)

---

## Part 4: Advanced Techniques & Tricks ⚡

### Trick 1: Pointer for Next Valid Position

```cpp
// Move element to next valid position
int validPos = 0;
for (int i = 0; i < n; i++) {
    if (isValid(nums[i])) {
        nums[validPos++] = nums[i];
    }
}
// validPos is now new length
```

### Trick 2: Rain Water Trapping Pattern

```cpp
int trap(vector<int>& height) {
    int left = 0, right = height.size() - 1;
    int leftMax = 0, rightMax = 0;
    int water = 0;
    
    while (left < right) {
        if (height[left] < height[right]) {
            height[left] >= leftMax ? 
                leftMax = height[left] : 
                water += leftMax - height[left];
            left++;
        } else {
            height[right] >= rightMax ? 
                rightMax = height[right] : 
                water += rightMax - height[right];
            right--;
        }
    }
    return water;
}
```

### Trick 3: In-place Reversal/Modification

```cpp
void reverseString(vector<char>& s) {
    int left = 0, right = s.size() - 1;
    while (left < right) {
        swap(s[left], s[right]);
        left++;
        right--;
    }
}
```

---

## Part 5: Solving Hard Problems - Step by Step 🚀

### Problem 1: Container With Most Water

```cpp
int maxArea(vector<int>& height) {
    int left = 0, right = height.size() - 1;
    int maxWater = 0;
    
    while (left < right) {
        // Calculate current area
        int width = right - left;
        int currentHeight = min(height[left], height[right]);
        maxWater = max(maxWater, width * currentHeight);
        
        // Move the shorter line (KEY INSIGHT!)
        if (height[left] < height[right]) {
            left++;
        } else {
            right--;
        }
    }
    return maxWater;
}
```

**Insight:** Always move the shorter line, as it's limiting the area.

### Problem 2: 3Sum (Triplets summing to zero)

```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    vector<vector<int>> result;
    sort(nums.begin(), nums.end()); // Sort first!
    
    for (int i = 0; i < nums.size(); i++) {
        // Skip duplicates
        if (i > 0 && nums[i] == nums[i-1]) continue;
        
        int left = i + 1, right = nums.size() - 1;
        int target = -nums[i]; // Because nums[i] + nums[left] + nums[right] = 0
        
        while (left < right) {
            int sum = nums[left] + nums[right];
            
            if (sum == target) {
                result.push_back({nums[i], nums[left], nums[right]});
                
                // Skip duplicates
                while (left < right && nums[left] == nums[left+1]) left++;
                while (left < right && nums[right] == nums[right-1]) right--;
                
                left++; right--;
            }
            else if (sum < target) {
                left++;
            }
            else {
                right--;
            }
        }
    }
    return result;
}
```

### Problem 3: Minimum Window Substring (Hard)

```cpp
string minWindow(string s, string t) {
    vector<int> freq(128, 0);
    for (char c : t) freq[c]++;
    
    int left = 0, right = 0;
    int minLeft = 0, minLen = INT_MAX;
    int required = t.size();
    
    while (right < s.size()) {
        // Expand window
        if (freq[s[right]] > 0) required--;
        freq[s[right]]--;
        right++;
        
        // Contract window when all chars found
        while (required == 0) {
            if (right - left < minLen) {
                minLen = right - left;
                minLeft = left;
            }
            
            // Move left pointer
            freq[s[left]]++;
            if (freq[s[left]] > 0) required++;
            left++;
        }
    }
    
    return minLen == INT_MAX ? "" : s.substr(minLeft, minLen);
}
```

---

## Part 6: Practice Strategy 🏋️

### Beginner (Build Foundation)
- Two Sum II (Sorted Array)
- Remove Duplicates
- Squares of Sorted Array
- Valid Palindrome

### Intermediate (Pattern Recognition)
- 3Sum
- Container With Most Water
- Trapping Rain Water
- Longest Substring Without Repeats

### Advanced (Competitive Level)
- Minimum Window Substring
- Subarrays with K Different Integers
- Longest Mountain in Array
- Boats to Save People

---

## Part 7: Common Pitfalls & Debugging 🐛

### Watch Out For:
- Off-by-one errors in loop conditions
- Forgetting to sort when needed
- Not handling duplicates in problems like 3Sum
- Infinite loops when pointers don't move
- Array index out of bounds

### Debug Template:

```cpp
void debugTwoPointers(vector<int>& nums, int left, int right) {
    cout << "Left: " << left << " (" << nums[left] << ") | ";
    cout << "Right: " << right << " (" << nums[right] << ") | ";
    cout << "Window: [";
    for (int i = left; i <= right; i++) {
        cout << nums[i] << " ";
    }
    cout << "]" << endl;
}
```

---

## Part 8: Quick Reference Cheat Sheet 📝

```cpp
// 1. Opposite Ends (Sorted Array Problems)
// Use when: Finding pairs, triplets, reversing
// Movement: Move smaller/larger pointer based on comparison

// 2. Fast & Slow (Cycle Detection)
// Use when: Finding middle, cycle detection, remove duplicates
// Movement: Fast moves 2 steps, slow moves 1 step

// 3. Sliding Window (Subarray Problems)
// Use when: Finding min/max length subarray with condition
// Movement: Expand right, shrink left when condition breaks

// 4. Three Pointers (Partitioning)
// Use when: Sorting 3 values, merging 3 arrays
// Movement: Each pointer tracks different section
```
