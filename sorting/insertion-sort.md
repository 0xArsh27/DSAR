## Insertion Sort Algorithm

Insertion Sort is a simple and intuitive sorting algorithm that works similarly to sorting playing cards in your hands. It is efficient for small datasets and partially sorted arrays.

### Steps:
1. **Iterate**: Start from the second element of the array.
2. **Compare**: Compare the current element (key) with its predecessor.
3. **Shift**: If the key is smaller than its predecessor, shift the greater elements one position up to make space for the key.

### Implementation:
```cpp
#include <vector>

void insertionSort(std::vector<int>& arr) {
    int n = arr.size();
    for (int i = 1; i < n; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j = j - 1;
        }
        arr[j + 1] = key;
    }
}
```

### Example Usage:
```cpp
#include <iostream>
#include <vector>

int main() {
    std::vector<int> arr = {12, 11, 13, 5, 6};

    insertionSort(arr);

    std::cout << "Sorted array: ";
    for (int num : arr) {
        std::cout << num << " ";
    }
    return 0;
}
```