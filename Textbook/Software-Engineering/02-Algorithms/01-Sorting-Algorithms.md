# Chapter 1: Sorting Algorithms

## Table of Contents
- [Introduction](#introduction)
- [Simple Sorting Algorithms](#simple-sorting-algorithms)
- [Efficient Sorting Algorithms](#efficient-sorting-algorithms)
- [Specialized Sorting Algorithms](#specialized-sorting-algorithms)
- [Comparison and Selection](#comparison-and-selection)
- [Practice Problems](#practice-problems)

---

## Introduction

Sorting is the process of arranging elements in a specific order (ascending or descending). It's one of the most fundamental operations in computer science.

**Why Sorting Matters:**
- Enables binary search (O(log n) vs O(n))
- Data analysis and statistics
- Database indexing
- Algorithm optimization

**Stability:** A sorting algorithm is stable if it preserves the relative order of equal elements.

---

## Simple Sorting Algorithms

### 1. Bubble Sort

Repeatedly swaps adjacent elements if they're in wrong order.

**JavaScript:**
```javascript
function bubbleSort(arr) {
    const n = arr.length;

    for (let i = 0; i < n - 1; i++) {
        let swapped = false;

        for (let j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
                swapped = true;
            }
        }

        // Optimization: if no swaps, array is sorted
        if (!swapped) break;
    }

    return arr;
}

console.log(bubbleSort([64, 34, 25, 12, 22, 11, 90]));
// [11, 12, 22, 25, 34, 64, 90]

// Time: O(n²) worst/average, O(n) best
// Space: O(1)
// Stable: Yes
```

**Python:**
```python
def bubble_sort(arr):
    n = len(arr)

    for i in range(n - 1):
        swapped = False

        for j in range(n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                swapped = True

        # Optimization: if no swaps, array is sorted
        if not swapped:
            break

    return arr

print(bubble_sort([64, 34, 25, 12, 22, 11, 90]))
# [11, 12, 22, 25, 34, 64, 90]

# Time: O(n²) worst/average, O(n) best
# Space: O(1)
# Stable: Yes
```

### 2. Selection Sort

Finds minimum element and places it at beginning.

**JavaScript:**
```javascript
function selectionSort(arr) {
    const n = arr.length;

    for (let i = 0; i < n - 1; i++) {
        let minIndex = i;

        // Find minimum element in remaining array
        for (let j = i + 1; j < n; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }

        // Swap minimum with first element
        if (minIndex !== i) {
            [arr[i], arr[minIndex]] = [arr[minIndex], arr[i]];
        }
    }

    return arr;
}

console.log(selectionSort([64, 25, 12, 22, 11]));
// [11, 12, 22, 25, 64]

// Time: O(n²) all cases
// Space: O(1)
// Stable: No (can be made stable)
```

**Python:**
```python
def selection_sort(arr):
    n = len(arr)

    for i in range(n - 1):
        min_index = i

        # Find minimum element in remaining array
        for j in range(i + 1, n):
            if arr[j] < arr[min_index]:
                min_index = j

        # Swap minimum with first element
        if min_index != i:
            arr[i], arr[min_index] = arr[min_index], arr[i]

    return arr

print(selection_sort([64, 25, 12, 22, 11]))
# [11, 12, 22, 25, 64]

# Time: O(n²) all cases
# Space: O(1)
# Stable: No
```

### 3. Insertion Sort

Builds sorted array one element at a time.

**JavaScript:**
```javascript
function insertionSort(arr) {
    const n = arr.length;

    for (let i = 1; i < n; i++) {
        const key = arr[i];
        let j = i - 1;

        // Move elements greater than key one position ahead
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }

        arr[j + 1] = key;
    }

    return arr;
}

console.log(insertionSort([12, 11, 13, 5, 6]));
// [5, 6, 11, 12, 13]

// Time: O(n²) worst/average, O(n) best
// Space: O(1)
// Stable: Yes
// Good for: Small arrays, nearly sorted arrays
```

**Python:**
```python
def insertion_sort(arr):
    n = len(arr)

    for i in range(1, n):
        key = arr[i]
        j = i - 1

        # Move elements greater than key one position ahead
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1

        arr[j + 1] = key

    return arr

print(insertion_sort([12, 11, 13, 5, 6]))
# [5, 6, 11, 12, 13]

# Time: O(n²) worst/average, O(n) best
# Space: O(1)
# Stable: Yes
```

---

## Efficient Sorting Algorithms

### 1. Merge Sort

Divide and conquer algorithm that divides array, sorts halves, and merges them.

**JavaScript:**
```javascript
function mergeSort(arr) {
    if (arr.length <= 1) return arr;

    const mid = Math.floor(arr.length / 2);
    const left = mergeSort(arr.slice(0, mid));
    const right = mergeSort(arr.slice(mid));

    return merge(left, right);
}

function merge(left, right) {
    const result = [];
    let i = 0, j = 0;

    while (i < left.length && j < right.length) {
        if (left[i] <= right[j]) {
            result.push(left[i++]);
        } else {
            result.push(right[j++]);
        }
    }

    return result.concat(left.slice(i)).concat(right.slice(j));
}

console.log(mergeSort([38, 27, 43, 3, 9, 82, 10]));
// [3, 9, 10, 27, 38, 43, 82]

// Time: O(n log n) all cases
// Space: O(n)
// Stable: Yes
// Good for: Linked lists, external sorting
```

**Python:**
```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])

    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0

    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1

    result.extend(left[i:])
    result.extend(right[j:])
    return result

print(merge_sort([38, 27, 43, 3, 9, 82, 10]))
# [3, 9, 10, 27, 38, 43, 82]

# Time: O(n log n) all cases
# Space: O(n)
# Stable: Yes
```

### 2. Quick Sort

Divide and conquer using pivot partitioning.

**JavaScript:**
```javascript
function quickSort(arr, low = 0, high = arr.length - 1) {
    if (low < high) {
        const pivotIndex = partition(arr, low, high);
        quickSort(arr, low, pivotIndex - 1);
        quickSort(arr, pivotIndex + 1, high);
    }
    return arr;
}

function partition(arr, low, high) {
    const pivot = arr[high];
    let i = low - 1;

    for (let j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            [arr[i], arr[j]] = [arr[j], arr[i]];
        }
    }

    [arr[i + 1], arr[high]] = [arr[high], arr[i + 1]];
    return i + 1;
}

// Optimized with random pivot
function quickSortRandom(arr, low = 0, high = arr.length - 1) {
    if (low < high) {
        // Random pivot selection
        const randomIndex = low + Math.floor(Math.random() * (high - low + 1));
        [arr[randomIndex], arr[high]] = [arr[high], arr[randomIndex]];

        const pivotIndex = partition(arr, low, high);
        quickSortRandom(arr, low, pivotIndex - 1);
        quickSortRandom(arr, pivotIndex + 1, high);
    }
    return arr;
}

console.log(quickSort([10, 7, 8, 9, 1, 5]));
// [1, 5, 7, 8, 9, 10]

// Time: O(n log n) average, O(n²) worst
// Space: O(log n) average (recursion stack)
// Stable: No
// Good for: General purpose, in-place sorting
```

**Python:**
```python
def quick_sort(arr, low=0, high=None):
    if high is None:
        high = len(arr) - 1

    if low < high:
        pivot_index = partition(arr, low, high)
        quick_sort(arr, low, pivot_index - 1)
        quick_sort(arr, pivot_index + 1, high)

    return arr

def partition(arr, low, high):
    pivot = arr[high]
    i = low - 1

    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]

    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1

# Optimized with random pivot
import random

def quick_sort_random(arr, low=0, high=None):
    if high is None:
        high = len(arr) - 1

    if low < high:
        # Random pivot selection
        random_index = random.randint(low, high)
        arr[random_index], arr[high] = arr[high], arr[random_index]

        pivot_index = partition(arr, low, high)
        quick_sort_random(arr, low, pivot_index - 1)
        quick_sort_random(arr, pivot_index + 1, high)

    return arr

print(quick_sort([10, 7, 8, 9, 1, 5]))
# [1, 5, 7, 8, 9, 10]

# Time: O(n log n) average, O(n²) worst
# Space: O(log n) average
# Stable: No
```

### 3. Heap Sort

Uses heap data structure for sorting.

**JavaScript:**
```javascript
function heapSort(arr) {
    const n = arr.length;

    // Build max heap
    for (let i = Math.floor(n / 2) - 1; i >= 0; i--) {
        heapify(arr, n, i);
    }

    // Extract elements from heap
    for (let i = n - 1; i > 0; i--) {
        [arr[0], arr[i]] = [arr[i], arr[0]];
        heapify(arr, i, 0);
    }

    return arr;
}

function heapify(arr, n, i) {
    let largest = i;
    const left = 2 * i + 1;
    const right = 2 * i + 2;

    if (left < n && arr[left] > arr[largest]) {
        largest = left;
    }

    if (right < n && arr[right] > arr[largest]) {
        largest = right;
    }

    if (largest !== i) {
        [arr[i], arr[largest]] = [arr[largest], arr[i]];
        heapify(arr, n, largest);
    }
}

console.log(heapSort([12, 11, 13, 5, 6, 7]));
// [5, 6, 7, 11, 12, 13]

// Time: O(n log n) all cases
// Space: O(1)
// Stable: No
// Good for: Memory-constrained environments
```

**Python:**
```python
def heap_sort(arr):
    n = len(arr)

    # Build max heap
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)

    # Extract elements from heap
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify(arr, i, 0)

    return arr

def heapify(arr, n, i):
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2

    if left < n and arr[left] > arr[largest]:
        largest = left

    if right < n and arr[right] > arr[largest]:
        largest = right

    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)

print(heap_sort([12, 11, 13, 5, 6, 7]))
# [5, 6, 7, 11, 12, 13]

# Time: O(n log n) all cases
# Space: O(1)
# Stable: No
```

---

## Specialized Sorting Algorithms

### 1. Counting Sort

For integers in a known range.

**JavaScript:**
```javascript
function countingSort(arr) {
    if (arr.length === 0) return arr;

    const max = Math.max(...arr);
    const min = Math.min(...arr);
    const range = max - min + 1;

    const count = new Array(range).fill(0);
    const output = new Array(arr.length);

    // Count occurrences
    for (const num of arr) {
        count[num - min]++;
    }

    // Cumulative count
    for (let i = 1; i < range; i++) {
        count[i] += count[i - 1];
    }

    // Build output array
    for (let i = arr.length - 1; i >= 0; i--) {
        const num = arr[i];
        output[count[num - min] - 1] = num;
        count[num - min]--;
    }

    return output;
}

console.log(countingSort([4, 2, 2, 8, 3, 3, 1]));
// [1, 2, 2, 3, 3, 4, 8]

// Time: O(n + k) where k is range
// Space: O(k)
// Stable: Yes
// Good for: Small range of integers
```

**Python:**
```python
def counting_sort(arr):
    if not arr:
        return arr

    max_val = max(arr)
    min_val = min(arr)
    range_size = max_val - min_val + 1

    count = [0] * range_size
    output = [0] * len(arr)

    # Count occurrences
    for num in arr:
        count[num - min_val] += 1

    # Cumulative count
    for i in range(1, range_size):
        count[i] += count[i - 1]

    # Build output array (reverse for stability)
    for i in range(len(arr) - 1, -1, -1):
        num = arr[i]
        output[count[num - min_val] - 1] = num
        count[num - min_val] -= 1

    return output

print(counting_sort([4, 2, 2, 8, 3, 3, 1]))
# [1, 2, 2, 3, 3, 4, 8]

# Time: O(n + k)
# Space: O(k)
# Stable: Yes
```

### 2. Radix Sort

Sorts by processing individual digits.

**JavaScript:**
```javascript
function radixSort(arr) {
    const max = Math.max(...arr);
    let exp = 1;

    while (Math.floor(max / exp) > 0) {
        countingSortByDigit(arr, exp);
        exp *= 10;
    }

    return arr;
}

function countingSortByDigit(arr, exp) {
    const output = new Array(arr.length);
    const count = new Array(10).fill(0);

    // Count occurrences of digits
    for (const num of arr) {
        const digit = Math.floor(num / exp) % 10;
        count[digit]++;
    }

    // Cumulative count
    for (let i = 1; i < 10; i++) {
        count[i] += count[i - 1];
    }

    // Build output array
    for (let i = arr.length - 1; i >= 0; i--) {
        const num = arr[i];
        const digit = Math.floor(num / exp) % 10;
        output[count[digit] - 1] = num;
        count[digit]--;
    }

    // Copy to original array
    for (let i = 0; i < arr.length; i++) {
        arr[i] = output[i];
    }
}

console.log(radixSort([170, 45, 75, 90, 802, 24, 2, 66]));
// [2, 24, 45, 66, 75, 90, 170, 802]

// Time: O(d * (n + k)) where d is digits, k is base (10)
// Space: O(n + k)
// Stable: Yes
// Good for: Fixed-length integers
```

**Python:**
```python
def radix_sort(arr):
    max_val = max(arr)
    exp = 1

    while max_val // exp > 0:
        counting_sort_by_digit(arr, exp)
        exp *= 10

    return arr

def counting_sort_by_digit(arr, exp):
    output = [0] * len(arr)
    count = [0] * 10

    # Count occurrences of digits
    for num in arr:
        digit = (num // exp) % 10
        count[digit] += 1

    # Cumulative count
    for i in range(1, 10):
        count[i] += count[i - 1]

    # Build output array
    for i in range(len(arr) - 1, -1, -1):
        num = arr[i]
        digit = (num // exp) % 10
        output[count[digit] - 1] = num
        count[digit] -= 1

    # Copy to original array
    for i in range(len(arr)):
        arr[i] = output[i]

print(radix_sort([170, 45, 75, 90, 802, 24, 2, 66]))
# [2, 24, 45, 66, 75, 90, 170, 802]

# Time: O(d * (n + k))
# Space: O(n + k)
# Stable: Yes
```

---

## Comparison and Selection

### Sorting Algorithm Comparison

| Algorithm | Best | Average | Worst | Space | Stable |
|-----------|------|---------|-------|-------|--------|
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) | No |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | O(1) | No |
| Counting Sort | O(n + k) | O(n + k) | O(n + k) | O(k) | Yes |
| Radix Sort | O(d(n+k)) | O(d(n+k)) | O(d(n+k)) | O(n+k) | Yes |

### When to Use Each Algorithm

- **Insertion Sort**: Small arrays (< 10 elements), nearly sorted data
- **Merge Sort**: Stable sort needed, linked lists, external sorting
- **Quick Sort**: General purpose, average O(n log n) performance
- **Heap Sort**: Memory constrained, guaranteed O(n log n)
- **Counting/Radix Sort**: Integer arrays with known small range

---

## Practice Problems

### Easy
1. **Sort Array** - Implement various sorting algorithms
2. **Merge Sorted Array** - Merge two sorted arrays
3. **Sort Colors** - Dutch National Flag problem

### Medium
1. **Kth Largest Element** - Find kth largest using quickselect
2. **Top K Frequent Elements** - Use sorting/heap
3. **Sort List** - Sort linked list
4. **Wiggle Sort** - Arrange elements in wave form

### Hard
1. **Count of Smaller Numbers After Self** - Modified merge sort
2. **Reverse Pairs** - Count reverse pairs during merge sort
3. **Maximum Gap** - Find maximum gap in sorted form

---

## Key Takeaways

1. **Choose based on constraints**: data size, memory, stability
2. **O(n log n) is optimal** for comparison-based sorting
3. **Non-comparison sorts** can achieve linear time for specific data
4. **Quick sort is fastest** in practice (good cache locality)
5. **Merge sort is best** when stability matters
6. **Understand trade-offs** between time, space, and stability

---

## Next Chapter

Continue to [Searching Algorithms](./02-Searching-Algorithms.md) to learn efficient search techniques.

[← Back to Algorithms](../02-Algorithms/) | [Next: Searching Algorithms →](./02-Searching-Algorithms.md)
