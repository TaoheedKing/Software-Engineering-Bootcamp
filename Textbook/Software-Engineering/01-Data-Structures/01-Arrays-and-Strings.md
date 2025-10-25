# Chapter 1: Arrays and Strings

## Table of Contents
- [Introduction](#introduction)
- [Arrays](#arrays)
- [Strings](#strings)
- [Common Patterns](#common-patterns)
- [Time and Space Complexity](#time-and-space-complexity)
- [Practice Problems](#practice-problems)

---

## Introduction

Arrays and strings are the most fundamental data structures in programming. Understanding how to manipulate them efficiently is crucial for solving algorithmic problems and building real-world applications.

### Key Concepts
- **Arrays**: Contiguous memory locations storing elements of the same type
- **Strings**: Sequences of characters (often implemented as arrays)
- **Index-based access**: O(1) time complexity
- **Dynamic resizing**: Understanding amortized complexity

---

## Arrays

### What is an Array?

An array is a collection of elements stored in contiguous memory locations. Elements can be accessed directly using their index.

**Properties:**
- Fixed or dynamic size (depending on language/implementation)
- Random access: O(1)
- Sequential storage
- Cache-friendly due to locality of reference

### Array Operations

#### 1. Declaration and Initialization

**JavaScript:**
```javascript
// Declaration
const arr1 = [1, 2, 3, 4, 5];
const arr2 = new Array(5); // Array of length 5
const arr3 = Array.from({length: 5}, (_, i) => i); // [0, 1, 2, 3, 4]
const arr4 = Array(5).fill(0); // [0, 0, 0, 0, 0]

// Multi-dimensional arrays
const matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
];
```

**Python:**
```python
# Declaration
arr1 = [1, 2, 3, 4, 5]
arr2 = [0] * 5  # [0, 0, 0, 0, 0]
arr3 = list(range(5))  # [0, 1, 2, 3, 4]
arr4 = [i for i in range(5)]  # List comprehension

# Multi-dimensional arrays
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Using NumPy for efficient arrays
import numpy as np
np_arr = np.array([1, 2, 3, 4, 5])
```

#### 2. Accessing Elements

**JavaScript:**
```javascript
const arr = [10, 20, 30, 40, 50];

// Access by index
console.log(arr[0]);  // 10
console.log(arr[arr.length - 1]);  // 50 (last element)

// Destructuring
const [first, second, ...rest] = arr;
console.log(first);  // 10
console.log(rest);   // [30, 40, 50]
```

**Python:**
```python
arr = [10, 20, 30, 40, 50]

# Access by index
print(arr[0])   # 10
print(arr[-1])  # 50 (last element, negative indexing)

# Slicing
print(arr[1:4])   # [20, 30, 40]
print(arr[:3])    # [10, 20, 30]
print(arr[2:])    # [30, 40, 50]
print(arr[::2])   # [10, 30, 50] (every 2nd element)
```

#### 3. Insertion

**JavaScript:**
```javascript
const arr = [1, 2, 3];

// Add to end - O(1) amortized
arr.push(4);  // [1, 2, 3, 4]

// Add to beginning - O(n)
arr.unshift(0);  // [0, 1, 2, 3, 4]

// Insert at specific position - O(n)
arr.splice(2, 0, 1.5);  // [0, 1, 1.5, 2, 3, 4]

console.log(arr);
```

**Python:**
```python
arr = [1, 2, 3]

# Add to end - O(1) amortized
arr.append(4)  # [1, 2, 3, 4]

# Add to beginning - O(n)
arr.insert(0, 0)  # [0, 1, 2, 3, 4]

# Insert at specific position - O(n)
arr.insert(2, 1.5)  # [0, 1, 1.5, 2, 3, 4]

# Extend with another list
arr.extend([5, 6])  # [0, 1, 1.5, 2, 3, 4, 5, 6]
```

#### 4. Deletion

**JavaScript:**
```javascript
const arr = [1, 2, 3, 4, 5];

// Remove from end - O(1)
const last = arr.pop();  // arr = [1, 2, 3, 4], last = 5

// Remove from beginning - O(n)
const first = arr.shift();  // arr = [2, 3, 4], first = 1

// Remove at specific position - O(n)
arr.splice(1, 1);  // arr = [2, 4] (removes element at index 1)

// Remove by value
const index = arr.indexOf(4);
if (index !== -1) arr.splice(index, 1);
```

**Python:**
```python
arr = [1, 2, 3, 4, 5]

# Remove from end - O(1)
last = arr.pop()  # arr = [1, 2, 3, 4], last = 5

# Remove from beginning - O(n)
first = arr.pop(0)  # arr = [2, 3, 4], first = 1

# Remove at specific position - O(n)
del arr[1]  # arr = [2, 4]

# Remove by value - O(n)
arr.remove(4)  # arr = [2] (removes first occurrence)
```

#### 5. Searching

**JavaScript:**
```javascript
const arr = [10, 20, 30, 40, 50];

// Linear search - O(n)
const index = arr.indexOf(30);  // 2
const exists = arr.includes(30);  // true

// Find element
const found = arr.find(x => x > 25);  // 30
const foundIndex = arr.findIndex(x => x > 25);  // 2

// Binary search (sorted array) - O(log n)
function binarySearch(arr, target) {
    let left = 0, right = arr.length - 1;

    while (left <= right) {
        const mid = Math.floor((left + right) / 2);

        if (arr[mid] === target) return mid;
        if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }

    return -1;
}

console.log(binarySearch(arr, 30));  // 2
```

**Python:**
```python
arr = [10, 20, 30, 40, 50]

# Linear search - O(n)
index = arr.index(30)  # 2
exists = 30 in arr  # True

# Binary search (sorted array) - O(log n)
def binary_search(arr, target):
    left, right = 0, len(arr) - 1

    while left <= right:
        mid = (left + right) // 2

        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1

print(binary_search(arr, 30))  # 2

# Using bisect module
import bisect
index = bisect.bisect_left(arr, 30)
```

#### 6. Traversal

**JavaScript:**
```javascript
const arr = [1, 2, 3, 4, 5];

// For loop
for (let i = 0; i < arr.length; i++) {
    console.log(arr[i]);
}

// For...of loop
for (const element of arr) {
    console.log(element);
}

// forEach method
arr.forEach((element, index) => {
    console.log(`Index ${index}: ${element}`);
});

// Map (transformation)
const doubled = arr.map(x => x * 2);  // [2, 4, 6, 8, 10]

// Filter
const evens = arr.filter(x => x % 2 === 0);  // [2, 4]

// Reduce
const sum = arr.reduce((acc, curr) => acc + curr, 0);  // 15
```

**Python:**
```python
arr = [1, 2, 3, 4, 5]

# For loop with index
for i in range(len(arr)):
    print(arr[i])

# For loop (direct iteration)
for element in arr:
    print(element)

# Enumerate (with index)
for index, element in enumerate(arr):
    print(f"Index {index}: {element}")

# List comprehension (transformation)
doubled = [x * 2 for x in arr]  # [2, 4, 6, 8, 10]

# Filter
evens = [x for x in arr if x % 2 == 0]  # [2, 4]

# Map function
doubled = list(map(lambda x: x * 2, arr))

# Reduce
from functools import reduce
sum_val = reduce(lambda acc, curr: acc + curr, arr, 0)  # 15
```

---

## Strings

### What is a String?

A string is a sequence of characters. In most languages, strings are implemented as arrays of characters with additional methods and properties.

**Important Differences:**
- **JavaScript**: Strings are immutable
- **Python**: Strings are immutable
- **Immutability**: Operations create new strings rather than modifying existing ones

### String Operations

#### 1. Declaration and Basic Operations

**JavaScript:**
```javascript
// Declaration
const str1 = "Hello";
const str2 = 'World';
const str3 = `Hello ${str2}`;  // Template literal

// Length
console.log(str1.length);  // 5

// Access characters
console.log(str1[0]);  // 'H'
console.log(str1.charAt(0));  // 'H'

// Concatenation
const combined = str1 + " " + str2;  // "Hello World"
const combined2 = str1.concat(" ", str2);  // "Hello World"
```

**Python:**
```python
# Declaration
str1 = "Hello"
str2 = 'World'
str3 = f"Hello {str2}"  # f-string

# Length
print(len(str1))  # 5

# Access characters
print(str1[0])   # 'H'
print(str1[-1])  # 'o' (last character)

# Concatenation
combined = str1 + " " + str2  # "Hello World"

# Join multiple strings
words = ["Hello", "World"]
combined = " ".join(words)  # "Hello World"
```

#### 2. String Manipulation

**JavaScript:**
```javascript
const str = "Hello World";

// Substring
console.log(str.substring(0, 5));   // "Hello"
console.log(str.slice(0, 5));       // "Hello"
console.log(str.slice(-5));         // "World"

// Case conversion
console.log(str.toLowerCase());     // "hello world"
console.log(str.toUpperCase());     // "HELLO WORLD"

// Trim whitespace
const padded = "  hello  ";
console.log(padded.trim());         // "hello"
console.log(padded.trimStart());    // "hello  "
console.log(padded.trimEnd());      // "  hello"

// Replace
console.log(str.replace("World", "JavaScript"));  // "Hello JavaScript"
console.log(str.replaceAll("l", "L"));  // "HeLLo WorLd"

// Split
const words = str.split(" ");  // ["Hello", "World"]
const chars = str.split("");   // ['H', 'e', 'l', 'l', 'o', ' ', 'W', 'o', 'r', 'l', 'd']

// Repeat
console.log("ha".repeat(3));  // "hahaha"
```

**Python:**
```python
str_val = "Hello World"

# Substring (slicing)
print(str_val[0:5])    # "Hello"
print(str_val[:5])     # "Hello"
print(str_val[-5:])    # "World"

# Case conversion
print(str_val.lower())       # "hello world"
print(str_val.upper())       # "HELLO WORLD"
print(str_val.capitalize())  # "Hello world"
print(str_val.title())       # "Hello World"

# Trim whitespace
padded = "  hello  "
print(padded.strip())   # "hello"
print(padded.lstrip())  # "hello  "
print(padded.rstrip())  # "  hello"

# Replace
print(str_val.replace("World", "Python"))  # "Hello Python"

# Split
words = str_val.split(" ")  # ["Hello", "World"]
chars = list(str_val)       # ['H', 'e', 'l', 'l', 'o', ' ', 'W', 'o', 'r', 'l', 'd']

# Repeat
print("ha" * 3)  # "hahaha"

# Join
print("-".join(words))  # "Hello-World"
```

#### 3. String Searching

**JavaScript:**
```javascript
const str = "The quick brown fox jumps over the lazy dog";

// Search methods
console.log(str.indexOf("quick"));      // 4
console.log(str.lastIndexOf("o"));      // 41
console.log(str.includes("fox"));       // true
console.log(str.startsWith("The"));     // true
console.log(str.endsWith("dog"));       // true

// Regular expressions
const regex = /\b\w{5}\b/g;  // Find all 5-letter words
const matches = str.match(regex);  // ["quick", "brown", "jumps"]

// Test pattern
console.log(/fox/.test(str));  // true

// Search with index
console.log(str.search(/fox/));  // 16
```

**Python:**
```python
import re

str_val = "The quick brown fox jumps over the lazy dog"

# Search methods
print(str_val.find("quick"))       # 4
print(str_val.rfind("o"))          # 41
print("fox" in str_val)            # True
print(str_val.startswith("The"))   # True
print(str_val.endswith("dog"))     # True

# Count occurrences
print(str_val.count("o"))  # 4

# Regular expressions
matches = re.findall(r'\b\w{5}\b', str_val)  # ['quick', 'brown', 'jumps']

# Test pattern
print(bool(re.search(r'fox', str_val)))  # True

# Search with match object
match = re.search(r'fox', str_val)
if match:
    print(match.start())  # 16
```

---

## Common Patterns

### Pattern 1: Two Pointers

Used for searching pairs, reversing, or partitioning arrays.

**Problem: Reverse a string**

**JavaScript:**
```javascript
function reverseString(s) {
    // Convert to array (strings are immutable)
    const arr = s.split('');
    let left = 0;
    let right = arr.length - 1;

    while (left < right) {
        // Swap
        [arr[left], arr[right]] = [arr[right], arr[left]];
        left++;
        right--;
    }

    return arr.join('');
}

console.log(reverseString("hello"));  // "olleh"

// Time: O(n), Space: O(n) due to array conversion
```

**Python:**
```python
def reverse_string(s):
    # Convert to list (strings are immutable)
    arr = list(s)
    left, right = 0, len(arr) - 1

    while left < right:
        # Swap
        arr[left], arr[right] = arr[right], arr[left]
        left += 1
        right -= 1

    return ''.join(arr)

print(reverse_string("hello"))  # "olleh"

# Pythonic way
print("hello"[::-1])  # "olleh"

# Time: O(n), Space: O(n)
```

**Problem: Two Sum (sorted array)**

**JavaScript:**
```javascript
function twoSum(numbers, target) {
    let left = 0;
    let right = numbers.length - 1;

    while (left < right) {
        const sum = numbers[left] + numbers[right];

        if (sum === target) {
            return [left, right];
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }

    return [-1, -1];
}

console.log(twoSum([2, 7, 11, 15], 9));  // [0, 1]

// Time: O(n), Space: O(1)
```

**Python:**
```python
def two_sum(numbers, target):
    left, right = 0, len(numbers) - 1

    while left < right:
        current_sum = numbers[left] + numbers[right]

        if current_sum == target:
            return [left, right]
        elif current_sum < target:
            left += 1
        else:
            right -= 1

    return [-1, -1]

print(two_sum([2, 7, 11, 15], 9))  # [0, 1]

# Time: O(n), Space: O(1)
```

### Pattern 2: Sliding Window

Used for finding subarrays or substrings with specific properties.

**Problem: Maximum sum of subarray of size k**

**JavaScript:**
```javascript
function maxSubarraySum(arr, k) {
    if (arr.length < k) return null;

    // Calculate sum of first window
    let maxSum = 0;
    for (let i = 0; i < k; i++) {
        maxSum += arr[i];
    }

    let currentSum = maxSum;

    // Slide the window
    for (let i = k; i < arr.length; i++) {
        currentSum = currentSum - arr[i - k] + arr[i];
        maxSum = Math.max(maxSum, currentSum);
    }

    return maxSum;
}

console.log(maxSubarraySum([1, 4, 2, 10, 23, 3, 1, 0, 20], 4));  // 39

// Time: O(n), Space: O(1)
```

**Python:**
```python
def max_subarray_sum(arr, k):
    if len(arr) < k:
        return None

    # Calculate sum of first window
    max_sum = sum(arr[:k])
    current_sum = max_sum

    # Slide the window
    for i in range(k, len(arr)):
        current_sum = current_sum - arr[i - k] + arr[i]
        max_sum = max(max_sum, current_sum)

    return max_sum

print(max_subarray_sum([1, 4, 2, 10, 23, 3, 1, 0, 20], 4))  # 39

# Time: O(n), Space: O(1)
```

**Problem: Longest substring without repeating characters**

**JavaScript:**
```javascript
function lengthOfLongestSubstring(s) {
    const seen = new Map();
    let left = 0;
    let maxLength = 0;

    for (let right = 0; right < s.length; right++) {
        const char = s[right];

        if (seen.has(char) && seen.get(char) >= left) {
            left = seen.get(char) + 1;
        }

        seen.set(char, right);
        maxLength = Math.max(maxLength, right - left + 1);
    }

    return maxLength;
}

console.log(lengthOfLongestSubstring("abcabcbb"));  // 3 ("abc")
console.log(lengthOfLongestSubstring("bbbbb"));     // 1 ("b")
console.log(lengthOfLongestSubstring("pwwkew"));    // 3 ("wke")

// Time: O(n), Space: O(min(n, m)) where m is charset size
```

**Python:**
```python
def length_of_longest_substring(s):
    seen = {}
    left = 0
    max_length = 0

    for right, char in enumerate(s):
        if char in seen and seen[char] >= left:
            left = seen[char] + 1

        seen[char] = right
        max_length = max(max_length, right - left + 1)

    return max_length

print(length_of_longest_substring("abcabcbb"))  # 3
print(length_of_longest_substring("bbbbb"))     # 1
print(length_of_longest_substring("pwwkew"))    # 3

# Time: O(n), Space: O(min(n, m))
```

### Pattern 3: Array Manipulation

**Problem: Rotate array k steps to the right**

**JavaScript:**
```javascript
function rotate(nums, k) {
    k = k % nums.length;

    // Helper function to reverse array segment
    const reverse = (start, end) => {
        while (start < end) {
            [nums[start], nums[end]] = [nums[end], nums[start]];
            start++;
            end--;
        }
    };

    // Reverse entire array
    reverse(0, nums.length - 1);
    // Reverse first k elements
    reverse(0, k - 1);
    // Reverse remaining elements
    reverse(k, nums.length - 1);

    return nums;
}

console.log(rotate([1, 2, 3, 4, 5, 6, 7], 3));  // [5, 6, 7, 1, 2, 3, 4]

// Time: O(n), Space: O(1)
```

**Python:**
```python
def rotate(nums, k):
    k = k % len(nums)

    def reverse(start, end):
        while start < end:
            nums[start], nums[end] = nums[end], nums[start]
            start += 1
            end -= 1

    # Reverse entire array
    reverse(0, len(nums) - 1)
    # Reverse first k elements
    reverse(0, k - 1)
    # Reverse remaining elements
    reverse(k, len(nums) - 1)

    return nums

print(rotate([1, 2, 3, 4, 5, 6, 7], 3))  # [5, 6, 7, 1, 2, 3, 4]

# Pythonic way
def rotate_pythonic(nums, k):
    k = k % len(nums)
    return nums[-k:] + nums[:-k]

# Time: O(n), Space: O(1) for in-place, O(n) for pythonic
```

### Pattern 4: String Palindrome Check

**JavaScript:**
```javascript
function isPalindrome(s) {
    // Clean and normalize
    s = s.toLowerCase().replace(/[^a-z0-9]/g, '');

    let left = 0;
    let right = s.length - 1;

    while (left < right) {
        if (s[left] !== s[right]) {
            return false;
        }
        left++;
        right--;
    }

    return true;
}

console.log(isPalindrome("A man, a plan, a canal: Panama"));  // true
console.log(isPalindrome("race a car"));  // false

// Time: O(n), Space: O(n) due to string cleaning
```

**Python:**
```python
def is_palindrome(s):
    # Clean and normalize
    s = ''.join(char.lower() for char in s if char.isalnum())

    left, right = 0, len(s) - 1

    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1

    return True

print(is_palindrome("A man, a plan, a canal: Panama"))  # True
print(is_palindrome("race a car"))  # False

# Alternative
def is_palindrome_pythonic(s):
    s = ''.join(char.lower() for char in s if char.isalnum())
    return s == s[::-1]

# Time: O(n), Space: O(n)
```

---

## Time and Space Complexity

### Array Operations Complexity

| Operation | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Access by index | O(1) | O(1) |
| Search (unsorted) | O(n) | O(1) |
| Search (sorted) | O(log n) binary search | O(1) |
| Insert at end | O(1) amortized | O(1) |
| Insert at beginning | O(n) | O(1) |
| Insert at middle | O(n) | O(1) |
| Delete from end | O(1) | O(1) |
| Delete from beginning | O(n) | O(1) |
| Delete from middle | O(n) | O(1) |

### String Operations Complexity

| Operation | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Access character | O(1) | O(1) |
| Concatenation | O(n + m) | O(n + m) |
| Substring | O(k) where k is substring length | O(k) |
| Search | O(n × m) naive, O(n) with KMP | O(1) or O(m) |
| Replace | O(n) | O(n) |

---

## Practice Problems

### Easy

1. **Remove Duplicates from Sorted Array**
   - Given a sorted array, remove duplicates in-place
   - Return the new length

2. **Merge Sorted Arrays**
   - Merge two sorted arrays into one sorted array

3. **Valid Anagram**
   - Check if two strings are anagrams

4. **First Unique Character**
   - Find the first non-repeating character in a string

### Medium

1. **Three Sum**
   - Find all unique triplets that sum to zero

2. **Longest Palindromic Substring**
   - Find the longest palindromic substring

3. **Container With Most Water**
   - Find two lines that together with x-axis form a container with maximum water

4. **Group Anagrams**
   - Group strings that are anagrams

### Hard

1. **Trapping Rain Water**
   - Calculate how much water can be trapped after raining

2. **Minimum Window Substring**
   - Find minimum window in string S that contains all characters of string T

3. **First Missing Positive**
   - Find the smallest missing positive integer

---

## Key Takeaways

1. **Arrays provide O(1) access** but O(n) insertion/deletion at arbitrary positions
2. **Strings are immutable** in JavaScript and Python - operations create new strings
3. **Two pointers technique** is powerful for sorted arrays and palindrome problems
4. **Sliding window** efficiently solves subarray/substring problems
5. **Hash maps** can optimize many array/string problems from O(n²) to O(n)
6. **Understanding complexity** helps choose the right approach

---

## Next Chapter

Continue to [Linked Lists](./02-Linked-Lists.md) to learn about dynamic data structures.

[← Back to Data Structures](../01-Data-Structures/) | [Next: Linked Lists →](./02-Linked-Lists.md)
