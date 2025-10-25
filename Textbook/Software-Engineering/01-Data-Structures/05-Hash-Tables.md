# Chapter 5: Hash Tables

## Table of Contents
- [Introduction](#introduction)
- [Hash Functions](#hash-functions)
- [Collision Resolution](#collision-resolution)
- [Implementation](#implementation)
- [Common Operations](#common-operations)
- [Applications](#applications)
- [Practice Problems](#practice-problems)

---

## Introduction

### What is a Hash Table?

A hash table (also called hash map) is a data structure that implements an associative array, mapping keys to values. It uses a hash function to compute an index into an array of buckets or slots, from which the desired value can be found.

**Key Properties:**
- **Average O(1)** for search, insert, and delete
- **Worst case O(n)** when many collisions occur
- **Space complexity**: O(n)

**Use Cases:**
- Database indexing
- Caching (LRU cache)
- Counting frequencies
- Finding duplicates
- Two-sum type problems

---

## Hash Functions

### What Makes a Good Hash Function?

1. **Deterministic**: Same key always produces same hash
2. **Uniform distribution**: Minimizes collisions
3. **Fast to compute**: O(1) time
4. **Minimize clustering**: Avoid grouping of hash values

### Common Hash Functions

**JavaScript:**
```javascript
// Simple hash function for strings
function simpleHash(key, tableSize) {
    let hash = 0;
    for (let i = 0; i < key.length; i++) {
        hash += key.charCodeAt(i);
    }
    return hash % tableSize;
}

// Better hash function (polynomial rolling hash)
function polynomialHash(key, tableSize) {
    const PRIME = 31;
    let hash = 0;
    for (let i = 0; i < key.length; i++) {
        hash = (hash * PRIME + key.charCodeAt(i)) % tableSize;
    }
    return hash;
}

// DJB2 hash function (widely used)
function djb2Hash(key, tableSize) {
    let hash = 5381;
    for (let i = 0; i < key.length; i++) {
        hash = ((hash << 5) + hash) + key.charCodeAt(i);
    }
    return Math.abs(hash) % tableSize;
}
```

**Python:**
```python
# Simple hash function for strings
def simple_hash(key, table_size):
    hash_value = 0
    for char in key:
        hash_value += ord(char)
    return hash_value % table_size

# Better hash function (polynomial rolling hash)
def polynomial_hash(key, table_size):
    PRIME = 31
    hash_value = 0
    for char in key:
        hash_value = (hash_value * PRIME + ord(char)) % table_size
    return hash_value

# DJB2 hash function
def djb2_hash(key, table_size):
    hash_value = 5381
    for char in key:
        hash_value = ((hash_value << 5) + hash_value) + ord(char)
    return abs(hash_value) % table_size

# Python's built-in hash() is very efficient
def python_hash(key, table_size):
    return hash(key) % table_size
```

---

## Collision Resolution

When two keys hash to the same index, we have a collision. Two main strategies:

### 1. Chaining (Open Hashing)

Store colliding elements in a linked list at each bucket.

**Advantages:**
- Simple to implement
- Hash table never fills up
- Less sensitive to hash function

**Disadvantages:**
- Extra memory for linked lists
- Cache performance may suffer

**JavaScript:**
```javascript
class HashTableChaining {
    constructor(size = 53) {
        this.size = size;
        this.table = new Array(size);
    }

    _hash(key) {
        const PRIME = 31;
        let hash = 0;
        for (let i = 0; i < Math.min(key.length, 100); i++) {
            hash = (hash * PRIME + key.charCodeAt(i)) % this.size;
        }
        return hash;
    }

    set(key, value) {
        const index = this._hash(key);

        if (!this.table[index]) {
            this.table[index] = [];
        }

        // Check if key exists, update if so
        for (let pair of this.table[index]) {
            if (pair[0] === key) {
                pair[1] = value;
                return;
            }
        }

        // Add new key-value pair
        this.table[index].push([key, value]);
    }

    get(key) {
        const index = this._hash(key);
        const bucket = this.table[index];

        if (bucket) {
            for (let pair of bucket) {
                if (pair[0] === key) {
                    return pair[1];
                }
            }
        }

        return undefined;
    }

    delete(key) {
        const index = this._hash(key);
        const bucket = this.table[index];

        if (bucket) {
            for (let i = 0; i < bucket.length; i++) {
                if (bucket[i][0] === key) {
                    bucket.splice(i, 1);
                    return true;
                }
            }
        }

        return false;
    }

    has(key) {
        return this.get(key) !== undefined;
    }

    keys() {
        const keysArr = [];
        for (let bucket of this.table) {
            if (bucket) {
                for (let pair of bucket) {
                    keysArr.push(pair[0]);
                }
            }
        }
        return keysArr;
    }

    values() {
        const valuesArr = [];
        for (let bucket of this.table) {
            if (bucket) {
                for (let pair of bucket) {
                    valuesArr.push(pair[1]);
                }
            }
        }
        return valuesArr;
    }
}

// Example usage
const ht = new HashTableChaining();
ht.set("name", "Alice");
ht.set("age", 25);
ht.set("city", "NYC");
console.log(ht.get("name"));  // "Alice"
console.log(ht.keys());       // ["name", "age", "city"]

// Average: O(1), Worst: O(n) for all operations
```

**Python:**
```python
class HashTableChaining:
    def __init__(self, size=53):
        self.size = size
        self.table = [[] for _ in range(size)]

    def _hash(self, key):
        PRIME = 31
        hash_value = 0
        for i, char in enumerate(key[:100]):  # Limit to first 100 chars
            hash_value = (hash_value * PRIME + ord(char)) % self.size
        return hash_value

    def set(self, key, value):
        index = self._hash(key)

        # Check if key exists, update if so
        for pair in self.table[index]:
            if pair[0] == key:
                pair[1] = value
                return

        # Add new key-value pair
        self.table[index].append([key, value])

    def get(self, key):
        index = self._hash(key)
        bucket = self.table[index]

        for pair in bucket:
            if pair[0] == key:
                return pair[1]

        return None

    def delete(self, key):
        index = self._hash(key)
        bucket = self.table[index]

        for i, pair in enumerate(bucket):
            if pair[0] == key:
                bucket.pop(i)
                return True

        return False

    def has(self, key):
        return self.get(key) is not None

    def keys(self):
        keys_list = []
        for bucket in self.table:
            for pair in bucket:
                keys_list.append(pair[0])
        return keys_list

    def values(self):
        values_list = []
        for bucket in self.table:
            for pair in bucket:
                values_list.append(pair[1])
        return values_list

# Example usage
ht = HashTableChaining()
ht.set("name", "Alice")
ht.set("age", 25)
ht.set("city", "NYC")
print(ht.get("name"))  # "Alice"
print(ht.keys())       # ["name", "age", "city"]

# Average: O(1), Worst: O(n)
```

### 2. Open Addressing

Store all elements in the hash table array itself. When collision occurs, probe for next available slot.

#### Linear Probing

**JavaScript:**
```javascript
class HashTableLinearProbing {
    constructor(size = 53) {
        this.size = size;
        this.keys = new Array(size);
        this.values = new Array(size);
        this.count = 0;
    }

    _hash(key) {
        const PRIME = 31;
        let hash = 0;
        for (let i = 0; i < Math.min(key.length, 100); i++) {
            hash = (hash * PRIME + key.charCodeAt(i)) % this.size;
        }
        return hash;
    }

    set(key, value) {
        if (this.count >= this.size * 0.7) {
            this._resize();
        }

        let index = this._hash(key);

        // Linear probing
        while (this.keys[index] !== undefined) {
            if (this.keys[index] === key) {
                // Key exists, update value
                this.values[index] = value;
                return;
            }
            index = (index + 1) % this.size;
        }

        this.keys[index] = key;
        this.values[index] = value;
        this.count++;
    }

    get(key) {
        let index = this._hash(key);
        let probeCount = 0;

        while (this.keys[index] !== undefined && probeCount < this.size) {
            if (this.keys[index] === key) {
                return this.values[index];
            }
            index = (index + 1) % this.size;
            probeCount++;
        }

        return undefined;
    }

    delete(key) {
        let index = this._hash(key);
        let probeCount = 0;

        while (this.keys[index] !== undefined && probeCount < this.size) {
            if (this.keys[index] === key) {
                this.keys[index] = undefined;
                this.values[index] = undefined;
                this.count--;

                // Rehash subsequent entries in cluster
                this._rehashCluster(index);
                return true;
            }
            index = (index + 1) % this.size;
            probeCount++;
        }

        return false;
    }

    _rehashCluster(startIndex) {
        let index = (startIndex + 1) % this.size;

        while (this.keys[index] !== undefined) {
            const keyToRehash = this.keys[index];
            const valueToRehash = this.values[index];

            this.keys[index] = undefined;
            this.values[index] = undefined;
            this.count--;

            this.set(keyToRehash, valueToRehash);
            index = (index + 1) % this.size;
        }
    }

    _resize() {
        const oldKeys = this.keys;
        const oldValues = this.values;

        this.size *= 2;
        this.keys = new Array(this.size);
        this.values = new Array(this.size);
        this.count = 0;

        for (let i = 0; i < oldKeys.length; i++) {
            if (oldKeys[i] !== undefined) {
                this.set(oldKeys[i], oldValues[i]);
            }
        }
    }

    loadFactor() {
        return this.count / this.size;
    }
}

// Example usage
const ht2 = new HashTableLinearProbing();
ht2.set("apple", 5);
ht2.set("banana", 3);
ht2.set("orange", 7);
console.log(ht2.get("banana"));  // 3
```

**Python:**
```python
class HashTableLinearProbing:
    def __init__(self, size=53):
        self.size = size
        self.keys = [None] * size
        self.values = [None] * size
        self.count = 0

    def _hash(self, key):
        PRIME = 31
        hash_value = 0
        for char in key[:100]:
            hash_value = (hash_value * PRIME + ord(char)) % self.size
        return hash_value

    def set(self, key, value):
        if self.count >= self.size * 0.7:
            self._resize()

        index = self._hash(key)

        # Linear probing
        while self.keys[index] is not None:
            if self.keys[index] == key:
                # Key exists, update value
                self.values[index] = value
                return
            index = (index + 1) % self.size

        self.keys[index] = key
        self.values[index] = value
        self.count += 1

    def get(self, key):
        index = self._hash(key)
        probe_count = 0

        while self.keys[index] is not None and probe_count < self.size:
            if self.keys[index] == key:
                return self.values[index]
            index = (index + 1) % self.size
            probe_count += 1

        return None

    def delete(self, key):
        index = self._hash(key)
        probe_count = 0

        while self.keys[index] is not None and probe_count < self.size:
            if self.keys[index] == key:
                self.keys[index] = None
                self.values[index] = None
                self.count -= 1

                # Rehash subsequent entries in cluster
                self._rehash_cluster(index)
                return True
            index = (index + 1) % self.size
            probe_count += 1

        return False

    def _rehash_cluster(self, start_index):
        index = (start_index + 1) % self.size

        while self.keys[index] is not None:
            key_to_rehash = self.keys[index]
            value_to_rehash = self.values[index]

            self.keys[index] = None
            self.values[index] = None
            self.count -= 1

            self.set(key_to_rehash, value_to_rehash)
            index = (index + 1) % self.size

    def _resize(self):
        old_keys = self.keys
        old_values = self.values

        self.size *= 2
        self.keys = [None] * self.size
        self.values = [None] * self.size
        self.count = 0

        for i in range(len(old_keys)):
            if old_keys[i] is not None:
                self.set(old_keys[i], old_values[i])

    def load_factor(self):
        return self.count / self.size

# Example usage
ht2 = HashTableLinearProbing()
ht2.set("apple", 5)
ht2.set("banana", 3)
ht2.set("orange", 7)
print(ht2.get("banana"))  # 3
```

#### Quadratic Probing & Double Hashing

**JavaScript:**
```javascript
// Quadratic Probing: probe at i, i+1², i+2², i+3², ...
class HashTableQuadratic {
    _probe(index, i) {
        return (index + i * i) % this.size;
    }
}

// Double Hashing: use second hash function for probe step
class HashTableDoubleHash {
    _hash2(key) {
        const PRIME = 7;
        return PRIME - (this._hash(key) % PRIME);
    }

    _probe(index, i, key) {
        return (index + i * this._hash2(key)) % this.size;
    }
}
```

**Python:**
```python
# Quadratic Probing
class HashTableQuadratic:
    def _probe(self, index, i):
        return (index + i * i) % self.size

# Double Hashing
class HashTableDoubleHash:
    def _hash2(self, key):
        PRIME = 7
        return PRIME - (self._hash(key) % PRIME)

    def _probe(self, index, i, key):
        return (index + i * self._hash2(key)) % self.size
```

---

## Implementation

### Using Built-in Hash Tables

**JavaScript:**
```javascript
// Map (maintains insertion order)
const map = new Map();
map.set("name", "Alice");
map.set("age", 25);
console.log(map.get("name"));     // "Alice"
console.log(map.has("age"));      // true
console.log(map.size);            // 2
map.delete("age");

// Object (plain object as hash table)
const obj = {};
obj["name"] = "Bob";
obj["age"] = 30;
console.log(obj.name);            // "Bob"
console.log("age" in obj);        // true

// Set (for unique values only)
const set = new Set();
set.add("apple");
set.add("banana");
set.add("apple");  // Duplicate, won't be added
console.log(set.size);            // 2
console.log(set.has("apple"));    // true
```

**Python:**
```python
# Dictionary (built-in hash table)
dict_table = {}
dict_table["name"] = "Alice"
dict_table["age"] = 25
print(dict_table.get("name"))      # "Alice"
print("age" in dict_table)         # True
del dict_table["age"]

# Set (for unique values only)
set_table = set()
set_table.add("apple")
set_table.add("banana")
set_table.add("apple")  # Duplicate, won't be added
print(len(set_table))              # 2
print("apple" in set_table)        # True

# Counter (for counting frequencies)
from collections import Counter
counter = Counter(["a", "b", "a", "c", "b", "a"])
print(counter)                     # Counter({'a': 3, 'b': 2, 'c': 1})
print(counter.most_common(2))      # [('a', 3), ('b', 2)]

# DefaultDict (with default values)
from collections import defaultdict
dd = defaultdict(int)  # Default value is 0
dd["count"] += 1
print(dd["count"])                 # 1
print(dd["nonexistent"])           # 0 (default)
```

---

## Common Operations

### 1. Frequency Counting

**JavaScript:**
```javascript
function frequencyCount(arr) {
    const freq = new Map();

    for (const item of arr) {
        freq.set(item, (freq.get(item) || 0) + 1);
    }

    return freq;
}

console.log(frequencyCount([1, 2, 2, 3, 3, 3]));
// Map { 1 => 1, 2 => 2, 3 => 3 }

// Time: O(n), Space: O(n)
```

**Python:**
```python
def frequency_count(arr):
    freq = {}

    for item in arr:
        freq[item] = freq.get(item, 0) + 1

    return freq

print(frequency_count([1, 2, 2, 3, 3, 3]))
# {1: 1, 2: 2, 3: 3}

# Using Counter
from collections import Counter
print(Counter([1, 2, 2, 3, 3, 3]))

# Time: O(n), Space: O(n)
```

### 2. Two Sum

**JavaScript:**
```javascript
function twoSum(nums, target) {
    const map = new Map();

    for (let i = 0; i < nums.length; i++) {
        const complement = target - nums[i];

        if (map.has(complement)) {
            return [map.get(complement), i];
        }

        map.set(nums[i], i);
    }

    return [];
}

console.log(twoSum([2, 7, 11, 15], 9));  // [0, 1]

// Time: O(n), Space: O(n)
```

**Python:**
```python
def two_sum(nums, target):
    num_map = {}

    for i, num in enumerate(nums):
        complement = target - num

        if complement in num_map:
            return [num_map[complement], i]

        num_map[num] = i

    return []

print(two_sum([2, 7, 11, 15], 9))  # [0, 1]

# Time: O(n), Space: O(n)
```

### 3. Group Anagrams

**JavaScript:**
```javascript
function groupAnagrams(strs) {
    const map = new Map();

    for (const str of strs) {
        const sorted = str.split('').sort().join('');

        if (!map.has(sorted)) {
            map.set(sorted, []);
        }

        map.get(sorted).push(str);
    }

    return Array.from(map.values());
}

console.log(groupAnagrams(["eat", "tea", "tan", "ate", "nat", "bat"]));
// [["eat", "tea", "ate"], ["tan", "nat"], ["bat"]]

// Time: O(n * k log k) where k is max string length
// Space: O(n * k)
```

**Python:**
```python
def group_anagrams(strs):
    anagram_map = {}

    for s in strs:
        sorted_str = ''.join(sorted(s))

        if sorted_str not in anagram_map:
            anagram_map[sorted_str] = []

        anagram_map[sorted_str].append(s)

    return list(anagram_map.values())

print(group_anagrams(["eat", "tea", "tan", "ate", "nat", "bat"]))
# [["eat", "tea", "ate"], ["tan", "nat"], ["bat"]]

# Alternative using defaultdict
from collections import defaultdict

def group_anagrams_v2(strs):
    anagram_map = defaultdict(list)

    for s in strs:
        sorted_str = ''.join(sorted(s))
        anagram_map[sorted_str].append(s)

    return list(anagram_map.values())

# Time: O(n * k log k), Space: O(n * k)
```

### 4. Longest Consecutive Sequence

**JavaScript:**
```javascript
function longestConsecutive(nums) {
    const numSet = new Set(nums);
    let maxLength = 0;

    for (const num of numSet) {
        // Only start counting from sequence start
        if (!numSet.has(num - 1)) {
            let currentNum = num;
            let currentLength = 1;

            while (numSet.has(currentNum + 1)) {
                currentNum++;
                currentLength++;
            }

            maxLength = Math.max(maxLength, currentLength);
        }
    }

    return maxLength;
}

console.log(longestConsecutive([100, 4, 200, 1, 3, 2]));  // 4 ([1,2,3,4])

// Time: O(n), Space: O(n)
```

**Python:**
```python
def longest_consecutive(nums):
    num_set = set(nums)
    max_length = 0

    for num in num_set:
        # Only start counting from sequence start
        if num - 1 not in num_set:
            current_num = num
            current_length = 1

            while current_num + 1 in num_set:
                current_num += 1
                current_length += 1

            max_length = max(max_length, current_length)

    return max_length

print(longest_consecutive([100, 4, 200, 1, 3, 2]))  # 4

# Time: O(n), Space: O(n)
```

---

## Applications

### 1. LRU Cache

**JavaScript:**
```javascript
class LRUCache {
    constructor(capacity) {
        this.capacity = capacity;
        this.cache = new Map();
    }

    get(key) {
        if (!this.cache.has(key)) {
            return -1;
        }

        // Move to end (most recently used)
        const value = this.cache.get(key);
        this.cache.delete(key);
        this.cache.set(key, value);

        return value;
    }

    put(key, value) {
        // Remove if exists
        if (this.cache.has(key)) {
            this.cache.delete(key);
        }

        // Add to end
        this.cache.set(key, value);

        // Remove least recently used if over capacity
        if (this.cache.size > this.capacity) {
            const firstKey = this.cache.keys().next().value;
            this.cache.delete(firstKey);
        }
    }
}

// Example
const lru = new LRUCache(2);
lru.put(1, 1);
lru.put(2, 2);
console.log(lru.get(1));    // 1
lru.put(3, 3);              // Evicts key 2
console.log(lru.get(2));    // -1 (not found)

// Both operations: O(1)
```

**Python:**
```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = OrderedDict()

    def get(self, key):
        if key not in self.cache:
            return -1

        # Move to end (most recently used)
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key, value):
        # Remove if exists
        if key in self.cache:
            self.cache.move_to_end(key)

        self.cache[key] = value

        # Remove least recently used if over capacity
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)

# Example
lru = LRUCache(2)
lru.put(1, 1)
lru.put(2, 2)
print(lru.get(1))    # 1
lru.put(3, 3)        # Evicts key 2
print(lru.get(2))    # -1

# Both operations: O(1)
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

    // Bucket sort by frequency
    const buckets = Array(nums.length + 1).fill(null).map(() => []);
    for (const [num, count] of freq) {
        buckets[count].push(num);
    }

    // Collect top k
    const result = [];
    for (let i = buckets.length - 1; i >= 0 && result.length < k; i--) {
        result.push(...buckets[i]);
    }

    return result.slice(0, k);
}

console.log(topKFrequent([1,1,1,2,2,3], 2));  // [1, 2]

// Time: O(n), Space: O(n)
```

**Python:**
```python
from collections import Counter

def top_k_frequent(nums, k):
    # Count frequencies
    freq = Counter(nums)

    # Bucket sort by frequency
    buckets = [[] for _ in range(len(nums) + 1)]
    for num, count in freq.items():
        buckets[count].append(num)

    # Collect top k
    result = []
    for i in range(len(buckets) - 1, -1, -1):
        result.extend(buckets[i])
        if len(result) >= k:
            break

    return result[:k]

print(top_k_frequent([1,1,1,2,2,3], 2))  # [1, 2]

# Alternative using heap
import heapq

def top_k_frequent_heap(nums, k):
    freq = Counter(nums)
    return heapq.nlargest(k, freq.keys(), key=freq.get)

# Time: O(n), Space: O(n)
```

---

## Practice Problems

### Easy
1. **Contains Duplicate** - Check if array has duplicates
2. **Valid Anagram** - Check if two strings are anagrams
3. **Two Sum** - Find two numbers that sum to target
4. **Intersection of Two Arrays** - Find common elements

### Medium
1. **Group Anagrams** - Group strings that are anagrams
2. **Top K Frequent Elements** - Find k most frequent elements
3. **Longest Consecutive Sequence** - Find longest consecutive sequence
4. **Subarray Sum Equals K** - Count subarrays with sum k
5. **4Sum II** - Count tuples from 4 arrays that sum to 0

### Hard
1. **LRU Cache** - Implement Least Recently Used cache
2. **First Missing Positive** - Find smallest positive integer not in array
3. **Substring with Concatenation of All Words** - Find all starting indices

---

## Key Takeaways

1. **Hash tables provide O(1) average** lookup, insert, delete
2. **Good hash function** minimizes collisions
3. **Load factor** should be kept under 0.7 for performance
4. **Chaining vs Open Addressing**: Trade-offs between memory and cache performance
5. **Perfect for counting, grouping, and lookup** problems
6. **Built-in implementations** (Map, Set, dict) are highly optimized

---

## Next Chapter

Continue to [Heaps and Priority Queues](./06-Heaps.md) to learn about efficient priority-based data structures.

[← Previous: Trees and Graphs](./04-Trees-and-Graphs.md) | [Next: Heaps →](./06-Heaps.md)
