# Chapter 6: Heaps and Priority Queues

## Table of Contents
- [Introduction](#introduction)
- [Heap Properties](#heap-properties)
- [Implementation](#implementation)
- [Heap Operations](#heap-operations)
- [Priority Queue](#priority-queue)
- [Applications](#applications)
- [Practice Problems](#practice-problems)

---

## Introduction

### What is a Heap?

A heap is a specialized tree-based data structure that satisfies the heap property. It's commonly implemented as a binary heap using an array.

**Types:**
- **Max Heap**: Parent ≥ children (root is maximum)
- **Min Heap**: Parent ≤ children (root is minimum)

**Key Properties:**
- **Complete binary tree**: All levels filled except possibly last, filled left to right
- **Array representation**: Efficient storage without pointers
- **Insert**: O(log n)
- **Extract min/max**: O(log n)
- **Peek min/max**: O(1)
- **Build heap**: O(n)

---

## Heap Properties

### Array Representation

For node at index `i`:
- **Parent**: `(i - 1) / 2` (floor division)
- **Left child**: `2 * i + 1`
- **Right child**: `2 * i + 2`

```
Example Min Heap:
       1
      / \
     3   5
    / \ / \
   7  9 6  8

Array: [1, 3, 5, 7, 9, 6, 8]
Index:  0  1  2  3  4  5  6
```

---

## Implementation

### Min Heap

**JavaScript:**
```javascript
class MinHeap {
    constructor() {
        this.heap = [];
    }

    // Get parent, left child, right child indices
    getParentIndex(i) {
        return Math.floor((i - 1) / 2);
    }

    getLeftChildIndex(i) {
        return 2 * i + 1;
    }

    getRightChildIndex(i) {
        return 2 * i + 2;
    }

    // Helper methods
    hasParent(i) {
        return this.getParentIndex(i) >= 0;
    }

    hasLeftChild(i) {
        return this.getLeftChildIndex(i) < this.heap.length;
    }

    hasRightChild(i) {
        return this.getRightChildIndex(i) < this.heap.length;
    }

    parent(i) {
        return this.heap[this.getParentIndex(i)];
    }

    leftChild(i) {
        return this.heap[this.getLeftChildIndex(i)];
    }

    rightChild(i) {
        return this.heap[this.getRightChildIndex(i)];
    }

    swap(i, j) {
        [this.heap[i], this.heap[j]] = [this.heap[j], this.heap[i]];
    }

    // Get minimum element - O(1)
    peek() {
        if (this.heap.length === 0) return null;
        return this.heap[0];
    }

    // Extract minimum element - O(log n)
    poll() {
        if (this.heap.length === 0) return null;
        if (this.heap.length === 1) return this.heap.pop();

        const min = this.heap[0];
        this.heap[0] = this.heap.pop();
        this.heapifyDown();

        return min;
    }

    // Insert element - O(log n)
    insert(value) {
        this.heap.push(value);
        this.heapifyUp();
    }

    // Bubble up
    heapifyUp() {
        let index = this.heap.length - 1;

        while (this.hasParent(index) && this.parent(index) > this.heap[index]) {
            const parentIndex = this.getParentIndex(index);
            this.swap(index, parentIndex);
            index = parentIndex;
        }
    }

    // Bubble down
    heapifyDown() {
        let index = 0;

        while (this.hasLeftChild(index)) {
            let smallerChildIndex = this.getLeftChildIndex(index);

            if (this.hasRightChild(index) &&
                this.rightChild(index) < this.leftChild(index)) {
                smallerChildIndex = this.getRightChildIndex(index);
            }

            if (this.heap[index] < this.heap[smallerChildIndex]) {
                break;
            }

            this.swap(index, smallerChildIndex);
            index = smallerChildIndex;
        }
    }

    // Get size
    size() {
        return this.heap.length;
    }

    // Check if empty
    isEmpty() {
        return this.heap.length === 0;
    }

    // Build heap from array - O(n)
    static buildHeap(array) {
        const heap = new MinHeap();
        heap.heap = [...array];

        // Start from last parent and heapify down
        for (let i = Math.floor(heap.heap.length / 2) - 1; i >= 0; i--) {
            heap.heapifyDownFrom(i);
        }

        return heap;
    }

    heapifyDownFrom(index) {
        while (this.hasLeftChild(index)) {
            let smallerChildIndex = this.getLeftChildIndex(index);

            if (this.hasRightChild(index) &&
                this.rightChild(index) < this.leftChild(index)) {
                smallerChildIndex = this.getRightChildIndex(index);
            }

            if (this.heap[index] < this.heap[smallerChildIndex]) {
                break;
            }

            this.swap(index, smallerChildIndex);
            index = smallerChildIndex;
        }
    }
}

// Example usage
const minHeap = new MinHeap();
[5, 3, 7, 1, 9].forEach(val => minHeap.insert(val));
console.log(minHeap.peek());   // 1
console.log(minHeap.poll());   // 1
console.log(minHeap.poll());   // 3
```

**Python:**
```python
class MinHeap:
    def __init__(self):
        self.heap = []

    # Get parent, left child, right child indices
    def get_parent_index(self, i):
        return (i - 1) // 2

    def get_left_child_index(self, i):
        return 2 * i + 1

    def get_right_child_index(self, i):
        return 2 * i + 2

    # Helper methods
    def has_parent(self, i):
        return self.get_parent_index(i) >= 0

    def has_left_child(self, i):
        return self.get_left_child_index(i) < len(self.heap)

    def has_right_child(self, i):
        return self.get_right_child_index(i) < len(self.heap)

    def parent(self, i):
        return self.heap[self.get_parent_index(i)]

    def left_child(self, i):
        return self.heap[self.get_left_child_index(i)]

    def right_child(self, i):
        return self.heap[self.get_right_child_index(i)]

    def swap(self, i, j):
        self.heap[i], self.heap[j] = self.heap[j], self.heap[i]

    # Get minimum element - O(1)
    def peek(self):
        if not self.heap:
            return None
        return self.heap[0]

    # Extract minimum element - O(log n)
    def poll(self):
        if not self.heap:
            return None
        if len(self.heap) == 1:
            return self.heap.pop()

        min_val = self.heap[0]
        self.heap[0] = self.heap.pop()
        self.heapify_down()

        return min_val

    # Insert element - O(log n)
    def insert(self, value):
        self.heap.append(value)
        self.heapify_up()

    # Bubble up
    def heapify_up(self):
        index = len(self.heap) - 1

        while self.has_parent(index) and self.parent(index) > self.heap[index]:
            parent_index = self.get_parent_index(index)
            self.swap(index, parent_index)
            index = parent_index

    # Bubble down
    def heapify_down(self):
        index = 0

        while self.has_left_child(index):
            smaller_child_index = self.get_left_child_index(index)

            if (self.has_right_child(index) and
                self.right_child(index) < self.left_child(index)):
                smaller_child_index = self.get_right_child_index(index)

            if self.heap[index] < self.heap[smaller_child_index]:
                break

            self.swap(index, smaller_child_index)
            index = smaller_child_index

    # Get size
    def size(self):
        return len(self.heap)

    # Check if empty
    def is_empty(self):
        return len(self.heap) == 0

    # Build heap from array - O(n)
    @staticmethod
    def build_heap(array):
        heap = MinHeap()
        heap.heap = array.copy()

        # Start from last parent and heapify down
        for i in range(len(heap.heap) // 2 - 1, -1, -1):
            heap.heapify_down_from(i)

        return heap

    def heapify_down_from(self, index):
        while self.has_left_child(index):
            smaller_child_index = self.get_left_child_index(index)

            if (self.has_right_child(index) and
                self.right_child(index) < self.left_child(index)):
                smaller_child_index = self.get_right_child_index(index)

            if self.heap[index] < self.heap[smaller_child_index]:
                break

            self.swap(index, smaller_child_index)
            index = smaller_child_index

# Example usage
min_heap = MinHeap()
for val in [5, 3, 7, 1, 9]:
    min_heap.insert(val)
print(min_heap.peek())   # 1
print(min_heap.poll())   # 1
print(min_heap.poll())   # 3

# Python's built-in heapq (min heap by default)
import heapq

heap = []
heapq.heappush(heap, 5)
heapq.heappush(heap, 3)
heapq.heappush(heap, 7)
print(heapq.heappop(heap))  # 3
```

### Max Heap

**JavaScript:**
```javascript
class MaxHeap extends MinHeap {
    heapifyUp() {
        let index = this.heap.length - 1;

        while (this.hasParent(index) && this.parent(index) < this.heap[index]) {
            const parentIndex = this.getParentIndex(index);
            this.swap(index, parentIndex);
            index = parentIndex;
        }
    }

    heapifyDown() {
        let index = 0;

        while (this.hasLeftChild(index)) {
            let largerChildIndex = this.getLeftChildIndex(index);

            if (this.hasRightChild(index) &&
                this.rightChild(index) > this.leftChild(index)) {
                largerChildIndex = this.getRightChildIndex(index);
            }

            if (this.heap[index] > this.heap[largerChildIndex]) {
                break;
            }

            this.swap(index, largerChildIndex);
            index = largerChildIndex;
        }
    }
}
```

**Python:**
```python
class MaxHeap:
    def __init__(self):
        self.heap = []

    # Similar to MinHeap but reverse comparisons
    # Or use heapq with negative values:

# Using heapq for max heap (negate values)
import heapq

max_heap = []
heapq.heappush(max_heap, -5)
heapq.heappush(max_heap, -3)
heapq.heappush(max_heap, -7)
print(-heapq.heappop(max_heap))  # 7 (maximum)
```

---

## Heap Operations

### Heap Sort

**JavaScript:**
```javascript
function heapSort(array) {
    const heap = MinHeap.buildHeap(array);
    const sorted = [];

    while (!heap.isEmpty()) {
        sorted.push(heap.poll());
    }

    return sorted;
}

console.log(heapSort([5, 3, 7, 1, 9, 2]));  // [1, 2, 3, 5, 7, 9]

// Time: O(n log n), Space: O(n)
```

**Python:**
```python
def heap_sort(array):
    heap = MinHeap.build_heap(array)
    sorted_array = []

    while not heap.is_empty():
        sorted_array.append(heap.poll())

    return sorted_array

print(heap_sort([5, 3, 7, 1, 9, 2]))  # [1, 2, 3, 5, 7, 9]

# Using heapq
import heapq

def heap_sort_builtin(array):
    heapq.heapify(array)  # O(n) in-place heapify
    return [heapq.heappop(array) for _ in range(len(array))]

# Time: O(n log n), Space: O(1) for in-place
```

### Find Kth Largest Element

**JavaScript:**
```javascript
function findKthLargest(nums, k) {
    // Use min heap of size k
    const minHeap = new MinHeap();

    for (const num of nums) {
        minHeap.insert(num);

        if (minHeap.size() > k) {
            minHeap.poll();
        }
    }

    return minHeap.peek();
}

console.log(findKthLargest([3,2,1,5,6,4], 2));  // 5

// Time: O(n log k), Space: O(k)
```

**Python:**
```python
import heapq

def find_kth_largest(nums, k):
    # Use min heap of size k
    heap = []

    for num in nums:
        heapq.heappush(heap, num)

        if len(heap) > k:
            heapq.heappop(heap)

    return heap[0]

print(find_kth_largest([3,2,1,5,6,4], 2))  # 5

# Alternative: Use max heap approach
def find_kth_largest_v2(nums, k):
    heap = [-num for num in nums]
    heapq.heapify(heap)

    for _ in range(k - 1):
        heapq.heappop(heap)

    return -heap[0]

# Time: O(n log k), Space: O(k)
```

---

## Priority Queue

A priority queue is an abstract data type where each element has a priority. Elements with higher priority are served before elements with lower priority.

**Implementation using Heap:**

**JavaScript:**
```javascript
class PriorityQueue {
    constructor() {
        this.heap = [];
    }

    enqueue(value, priority) {
        this.heap.push({ value, priority });
        this.heapifyUp();
    }

    dequeue() {
        if (this.isEmpty()) return null;
        if (this.heap.length === 1) return this.heap.pop().value;

        const min = this.heap[0].value;
        this.heap[0] = this.heap.pop();
        this.heapifyDown();

        return min;
    }

    peek() {
        return this.isEmpty() ? null : this.heap[0].value;
    }

    isEmpty() {
        return this.heap.length === 0;
    }

    heapifyUp() {
        let index = this.heap.length - 1;

        while (index > 0) {
            const parentIndex = Math.floor((index - 1) / 2);

            if (this.heap[index].priority >= this.heap[parentIndex].priority) {
                break;
            }

            [this.heap[index], this.heap[parentIndex]] =
                [this.heap[parentIndex], this.heap[index]];
            index = parentIndex;
        }
    }

    heapifyDown() {
        let index = 0;

        while (2 * index + 1 < this.heap.length) {
            let smallerChildIndex = 2 * index + 1;
            const rightChildIndex = 2 * index + 2;

            if (rightChildIndex < this.heap.length &&
                this.heap[rightChildIndex].priority < this.heap[smallerChildIndex].priority) {
                smallerChildIndex = rightChildIndex;
            }

            if (this.heap[index].priority <= this.heap[smallerChildIndex].priority) {
                break;
            }

            [this.heap[index], this.heap[smallerChildIndex]] =
                [this.heap[smallerChildIndex], this.heap[index]];
            index = smallerChildIndex;
        }
    }
}

// Example usage
const pq = new PriorityQueue();
pq.enqueue("Task 1", 3);
pq.enqueue("Task 2", 1);  // Higher priority
pq.enqueue("Task 3", 2);

console.log(pq.dequeue());  // "Task 2" (priority 1)
console.log(pq.dequeue());  // "Task 3" (priority 2)
```

**Python:**
```python
import heapq

class PriorityQueue:
    def __init__(self):
        self.heap = []
        self.counter = 0  # For stable ordering

    def enqueue(self, value, priority):
        # Use counter to maintain stable ordering
        heapq.heappush(self.heap, (priority, self.counter, value))
        self.counter += 1

    def dequeue(self):
        if self.is_empty():
            return None
        return heapq.heappop(self.heap)[2]  # Return value

    def peek(self):
        if self.is_empty():
            return None
        return self.heap[0][2]

    def is_empty(self):
        return len(self.heap) == 0

# Example usage
pq = PriorityQueue()
pq.enqueue("Task 1", 3)
pq.enqueue("Task 2", 1)  # Higher priority
pq.enqueue("Task 3", 2)

print(pq.dequeue())  # "Task 2"
print(pq.dequeue())  # "Task 3"

# Python's queue.PriorityQueue (thread-safe)
from queue import PriorityQueue as PQ

pq2 = PQ()
pq2.put((3, "Task 1"))
pq2.put((1, "Task 2"))
pq2.put((2, "Task 3"))

print(pq2.get()[1])  # "Task 2"
```

---

## Applications

### 1. Merge K Sorted Lists

**JavaScript:**
```javascript
function mergeKLists(lists) {
    const minHeap = new MinHeap();
    const result = [];

    // Add first element from each list to heap
    for (let i = 0; i < lists.length; i++) {
        if (lists[i].length > 0) {
            minHeap.insert({ value: lists[i][0], listIndex: i, elementIndex: 0 });
        }
    }

    while (!minHeap.isEmpty()) {
        const { value, listIndex, elementIndex } = minHeap.poll();
        result.push(value);

        // Add next element from same list
        const nextIndex = elementIndex + 1;
        if (nextIndex < lists[listIndex].length) {
            minHeap.insert({
                value: lists[listIndex][nextIndex],
                listIndex,
                elementIndex: nextIndex
            });
        }
    }

    return result;
}

const lists = [[1, 4, 5], [1, 3, 4], [2, 6]];
console.log(mergeKLists(lists));  // [1, 1, 2, 3, 4, 4, 5, 6]

// Time: O(N log k), Space: O(k)
// where N is total elements, k is number of lists
```

**Python:**
```python
import heapq

def merge_k_sorted_lists(lists):
    heap = []
    result = []

    # Add first element from each list to heap
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst[0], i, 0))

    while heap:
        value, list_idx, element_idx = heapq.heappop(heap)
        result.append(value)

        # Add next element from same list
        next_idx = element_idx + 1
        if next_idx < len(lists[list_idx]):
            heapq.heappush(heap, (
                lists[list_idx][next_idx],
                list_idx,
                next_idx
            ))

    return result

lists = [[1, 4, 5], [1, 3, 4], [2, 6]]
print(merge_k_sorted_lists(lists))  # [1, 1, 2, 3, 4, 4, 5, 6]

# Time: O(N log k), Space: O(k)
```

### 2. Top K Frequent Elements

**JavaScript:**
```javascript
function topKFrequent(nums, k) {
    // Count frequencies
    const freq = new Map();
    for (const num of nums) {
        freq.set(num, (freq.get(num) || 0) + 1);
    }

    // Use min heap of size k
    const minHeap = new MinHeap();

    for (const [num, count] of freq) {
        minHeap.insert({ num, count });

        if (minHeap.size() > k) {
            minHeap.poll();
        }
    }

    return minHeap.heap.map(item => item.num);
}

console.log(topKFrequent([1,1,1,2,2,3], 2));  // [1, 2]

// Time: O(n log k), Space: O(n)
```

**Python:**
```python
import heapq
from collections import Counter

def top_k_frequent(nums, k):
    # Count frequencies
    freq = Counter(nums)

    # Use heap to get top k
    return heapq.nlargest(k, freq.keys(), key=freq.get)

print(top_k_frequent([1,1,1,2,2,3], 2))  # [1, 2]

# Alternative using min heap
def top_k_frequent_v2(nums, k):
    freq = Counter(nums)
    heap = []

    for num, count in freq.items():
        heapq.heappush(heap, (count, num))

        if len(heap) > k:
            heapq.heappop(heap)

    return [num for count, num in heap]

# Time: O(n log k), Space: O(n)
```

### 3. Median from Data Stream

**JavaScript:**
```javascript
class MedianFinder {
    constructor() {
        this.maxHeap = new MaxHeap();  // Lower half
        this.minHeap = new MinHeap();  // Upper half
    }

    addNum(num) {
        // Add to max heap (lower half)
        this.maxHeap.insert(num);

        // Balance: move max from lower to upper
        this.minHeap.insert(this.maxHeap.poll());

        // If upper half larger, rebalance
        if (this.minHeap.size() > this.maxHeap.size()) {
            this.maxHeap.insert(this.minHeap.poll());
        }
    }

    findMedian() {
        if (this.maxHeap.size() > this.minHeap.size()) {
            return this.maxHeap.peek();
        }

        return (this.maxHeap.peek() + this.minHeap.peek()) / 2;
    }
}

// Example
const mf = new MedianFinder();
mf.addNum(1);
mf.addNum(2);
console.log(mf.findMedian());  // 1.5
mf.addNum(3);
console.log(mf.findMedian());  // 2

// Add: O(log n), Find: O(1)
```

**Python:**
```python
import heapq

class MedianFinder:
    def __init__(self):
        self.max_heap = []  # Lower half (negated for max heap)
        self.min_heap = []  # Upper half

    def add_num(self, num):
        # Add to max heap (lower half)
        heapq.heappush(self.max_heap, -num)

        # Balance: move max from lower to upper
        heapq.heappush(self.min_heap, -heapq.heappop(self.max_heap))

        # If upper half larger, rebalance
        if len(self.min_heap) > len(self.max_heap):
            heapq.heappush(self.max_heap, -heapq.heappop(self.min_heap))

    def find_median(self):
        if len(self.max_heap) > len(self.min_heap):
            return -self.max_heap[0]

        return (-self.max_heap[0] + self.min_heap[0]) / 2

# Example
mf = MedianFinder()
mf.add_num(1)
mf.add_num(2)
print(mf.find_median())  # 1.5
mf.add_num(3)
print(mf.find_median())  # 2

# Add: O(log n), Find: O(1)
```

---

## Practice Problems

### Easy
1. **Last Stone Weight** - Simulate stone smashing
2. **Kth Largest Element in a Stream** - Design class for kth largest

### Medium
1. **Top K Frequent Elements** - Find k most frequent elements
2. **Kth Largest Element in an Array** - Find kth largest
3. **Merge K Sorted Lists** - Merge k sorted linked lists
4. **Task Scheduler** - Minimize task execution time

### Hard
1. **Find Median from Data Stream** - Maintain running median
2. **Sliding Window Median** - Median in sliding window
3. **IPO** - Maximize capital with constraints

---

## Key Takeaways

1. **Heaps enable O(log n) insertion and extraction** of min/max
2. **Perfect for priority-based** operations
3. **Build heap is O(n)**, not O(n log n)
4. **Array representation** is memory-efficient
5. **Two heaps technique** useful for median problems
6. **Use min heap for kth largest**, max heap for kth smallest

---

## Next Chapter

Continue to [Tries](./07-Tries.md) to learn about prefix tree data structures.

[← Previous: Hash Tables](./05-Hash-Tables.md) | [Next: Tries →](./07-Tries.md)
