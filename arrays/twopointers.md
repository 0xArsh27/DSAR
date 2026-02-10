# Two Pointers Technique — Reference (DSA Ready)

## Overview

Two pointers is a family of techniques that uses two indices/pointers to traverse a data structure with O(1) extra space and typically O(n) time. Use it when you can make progress from both ends or maintain a window over the input.

Complexity summary:
- Brute force: O(n²)
- Two pointers: O(n)
- Extra space: O(1) (unless the algorithm needs auxiliary structures)

---

## Fundamental Patterns

### 1) Opposite-ends (two-sum, pair problems)

Template:
```cpp
int left = 0, right = n - 1;
while (left < right) {
    // compute using nums[left] and nums[right]
    if (condition_to_move_left) left++;
    else right--;
}
```

Example — Two Sum (sorted array):
```cpp
vector<int> twoSum(const vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    while (left < right) {
        int sum = nums[left] + nums[right];
        if (sum == target) return {left, right};
        if (sum < target) left++; else right--;
    }
    return {};
}
```

### 2) Fast & slow (cycle detection, middle finding)

Template:
```cpp
int slow = start, fast = start;
while (fast < n && /* fast + 1 < n */) {
    slow = next(slow);
    fast = next(next(fast));
}
```

Example — remove duplicates in-place:
```cpp
int removeDuplicates(vector<int>& nums) {
    if (nums.empty()) return 0;
    int slow = 1;
    for (int fast = 1; fast < nums.size(); ++fast) {
        if (nums[fast] != nums[fast - 1]) nums[slow++] = nums[fast];
    }
    return slow;
}
```

### 3) Sliding window (variable-size window problems)

Template:
```cpp
int left = 0, right = 0;
while (right < n) {
    // expand using nums[right++]
    while (need_to_shrink_window) {
        // shrink using nums[left++]
    }
    // update answer
}
```

Example — longest substring without repeating characters:
```cpp
int lengthOfLongestSubstring(const string& s) {
    vector<int> last(256, -1);
    int left = 0, maxLen = 0;
    for (int right = 0; right < (int)s.size(); ++right) {
        if (last[(unsigned char)s[right]] >= left) left = last[(unsigned char)s[right]] + 1;
        last[(unsigned char)s[right]] = right;
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

### 4) Three pointers (Dutch national flag)

Template:
```cpp
int low = 0, mid = 0, high = n - 1;
while (mid <= high) {
    if (nums[mid] == 0) swap(nums[low++], nums[mid++]);
    else if (nums[mid] == 1) ++mid;
    else swap(nums[mid], nums[high--]);
}
```

### 5) Parallel/merge pointers (merge two sorted lists)

Template:
```cpp
int i = 0, j = 0, k = 0;
while (i < m && j < n) result[k++] = (arr1[i] < arr2[j]) ? arr1[i++] : arr2[j++];
// copy remaining
```

---

## Decision Framework (When to use which)

Checklist:
- Is the input sorted or can it be sorted affordably?
- Are you looking for pairs/triplets, a subarray, or a relation between two arrays?
- Can you maintain state with two indices and constant extra memory?
- What is the stop condition and how will pointers move to guarantee progress?

Mnemonic: CHOOSE — Check sorted, How many pointers, Opposite or same direction, Order of movement, Stop condition, Edge cases.

---

## Useful Patterns & Tricks

- Next-valid position (compact arrays / remove elements): move valid elements forward and track `validPos`.
- Trapping rain water: use opposite-ends with leftMax/rightMax to compute trapped water in O(n).
- In-place reversal: swap symmetric elements moving two pointers toward center.

Example — trapping rain water:
```cpp
int trap(const vector<int>& height) {
    int left = 0, right = height.size() - 1;
    int leftMax = 0, rightMax = 0, water = 0;
    while (left < right) {
        if (height[left] < height[right]) {
            leftMax = max(leftMax, height[left]);
            water += leftMax - height[left];
            ++left;
        } else {
            rightMax = max(rightMax, height[right]);
            water += rightMax - height[right];
            --right;
        }
    }
    return water;
}
```

---

## Representative Problems (concise)

Container With Most Water — key insight: move the shorter line.
```cpp
int maxArea(const vector<int>& height) {
    int left = 0, right = height.size() - 1, best = 0;
    while (left < right) {
        int h = min(height[left], height[right]);
        best = max(best, h * (right - left));
        if (height[left] < height[right]) ++left; else --right;
    }
    return best;
}
```

3Sum — sort, fix one element, two-pointer for the remaining pair.

Minimum Window Substring — sliding window with counts for required characters.

---

## Practice roadmap

- Beginner: Two Sum II, Remove Duplicates, Reverse String
- Intermediate: 3Sum, Container With Most Water, Trapping Rain Water
- Advanced: Minimum Window Substring, Subarrays with K distinct, Longest Mountain

---

## Common Pitfalls

- Off-by-one errors when updating boundaries
- Not guaranteeing pointer progress (risk of infinite loop)
- Failing to handle duplicates when required
- Using the wrong stop condition (e.g., <= vs <)

Debug helper:
```cpp
void debugTwoPointers(const vector<int>& nums, int left, int right) {
    cout << "L=" << left << " R=" << right << " [";
    for (int i = left; i <= right; ++i) cout << nums[i] << (i==right?"":", ");
    cout << "]\n";
}
```

---

## DSA-ready checklist

- Know which pattern fits the problem and why.
- Identify and test edge cases: empty input, single element, all equal values.
- Write O(n) two-pointer solutions before considering more complex approaches.
- Practice transforming brute-force pair loops into two-pointer scans.

_Concise, interview-focused reference for two-pointer techniques._

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
