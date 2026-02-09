# Hash Map Patterns (LeetCode)

## What is a hash map?
A hash map stores data as key → value pairs. Common keys include numbers, characters, or strings; values are often frequencies, indices, booleans, or lists.

### Common C++ map types
- `unordered_map` — average O(1) operations (hash table)
- `map` — ordered keys, O(log n) operations (balanced BST)

---

## A) Complement lookup (Two Sum)
When you need to find a pair that sums to a target, use a hash map to store seen values and their indices.

Example pattern (Two Sum):
```cpp
unordered_map<int,int> mp;
for (int i = 0; i < n; ++i) {
    int need = target - nums[i];
    if (mp.count(need)) return {mp[need], i};
    mp[nums[i]] = i;
}
```

## B) Prefix-sum + hash map
Count subarrays with a given sum, or related problems, by storing prefix-sum frequencies.

Key idea: if current prefix sum is `sum`, we look for prior occurrences of `sum - k`.

Example pattern (count subarrays with sum = k):
```cpp
unordered_map<int,int> freq;
freq[0] = 1; // base case
int sum = 0, ans = 0;
for (int x : nums) {
    sum += x;
    if (freq.count(sum - k)) ans += freq[sum - k];
    ++freq[sum];
}
```

## C) Store first occurrence index
When finding longest subarray/substring with a property, store the first index where a prefix metric appeared.

Pattern (longest subarray with sum = k / similar problems):
```cpp
unordered_map<int,int> firstIndex;
firstIndex[0] = -1;

int sum = 0, best = 0;
for (int i = 0; i < n; ++i) {
    sum += nums[i];
    if (firstIndex.count(sum)) {
        best = max(best, i - firstIndex[sum]);
    } else {
        firstIndex[sum] = i;
    }
}
```

## D) Sliding window + hash map (strings)
Useful for longest substring without repeating characters, finding anagrams, and minimum window substring problems.

Pattern (longest substring without repeating characters):
```cpp
unordered_map<char,int> count;
int l = 0, ans = 0;
for (int r = 0; r < n; ++r) {
    ++count[s[r]];
    while (count[s[r]] > 1) {
        --count[s[l]];
        ++l;
    }
    ans = max(ans, r - l + 1);
}
```

## E) Grouping (key → list)
Group items (e.g., anagrams) by a canonical key such as the sorted string or a frequency signature.

Pattern (group anagrams):
```cpp
unordered_map<string, vector<string>> groups;
for (const string &s : strs) {
    string key = s;
    sort(key.begin(), key.end());
    groups[key].push_back(s);
}
```

## F) Hash set (existence only)
If you only need to check existence (seen / not seen), use `unordered_set`.

Pattern examples:
```cpp
// deduplicate or check membership
unordered_set<int> st(nums.begin(), nums.end());

// check consecutive sequences (example approach)
for (int x : nums) {
    if (!st.count(x - 1)) {
        int cur = x;
        int len = 1;
        while (st.count(cur + 1)) {
            ++cur; ++len;
        }
        best = max(best, len);
    }
}
```

---
Keep these patterns in mind and adapt the small variations to the problem constraints (e.g., value ranges, memory limits, and whether ordering matters).
