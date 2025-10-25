# Chapter 3: Stacks and Queues

## Table of Contents
- [Introduction](#introduction)
- [Stacks](#stacks)
- [Queues](#queues)
- [Advanced Variations](#advanced-variations)
- [Applications](#applications)
- [Practice Problems](#practice-problems)

---

## Introduction

Stacks and queues are fundamental linear data structures that restrict access to elements based on specific ordering principles:
- **Stack**: Last In, First Out (LIFO)
- **Queue**: First In, First Out (FIFO)

---

## Stacks

### What is a Stack?

A stack is a linear data structure where elements are added and removed from the same end, called the "top." Think of it like a stack of plates - you can only add or remove from the top.

**Operations:**
- **Push**: Add element to top - O(1)
- **Pop**: Remove element from top - O(1)
- **Peek/Top**: View top element without removing - O(1)
- **isEmpty**: Check if stack is empty - O(1)

### Implementation

#### Using Array

**JavaScript:**
```javascript
class Stack {
    constructor() {
        this.items = [];
    }

    // Push element to top - O(1)
    push(element) {
        this.items.push(element);
    }

    // Remove and return top element - O(1)
    pop() {
        if (this.isEmpty()) {
            return null;
        }
        return this.items.pop();
    }

    // View top element - O(1)
    peek() {
        if (this.isEmpty()) {
            return null;
        }
        return this.items[this.items.length - 1];
    }

    // Check if stack is empty
    isEmpty() {
        return this.items.length === 0;
    }

    // Get stack size
    size() {
        return this.items.length;
    }

    // Clear stack
    clear() {
        this.items = [];
    }

    // Print stack
    print() {
        console.log(this.items.join(' -> '));
    }
}

// Example usage
const stack = new Stack();
stack.push(1);
stack.push(2);
stack.push(3);
console.log(stack.peek());  // 3
stack.pop();
console.log(stack.peek());  // 2
```

**Python:**
```python
class Stack:
    def __init__(self):
        self.items = []

    # Push element to top - O(1)
    def push(self, element):
        self.items.append(element)

    # Remove and return top element - O(1)
    def pop(self):
        if self.is_empty():
            return None
        return self.items.pop()

    # View top element - O(1)
    def peek(self):
        if self.is_empty():
            return None
        return self.items[-1]

    # Check if stack is empty
    def is_empty(self):
        return len(self.items) == 0

    # Get stack size
    def size(self):
        return len(self.items)

    # Clear stack
    def clear(self):
        self.items = []

    # Print stack
    def print_stack(self):
        print(' -> '.join(map(str, self.items)))

# Example usage
stack = Stack()
stack.push(1)
stack.push(2)
stack.push(3)
print(stack.peek())  # 3
stack.pop()
print(stack.peek())  # 2

# Python also has built-in list that works as stack
stack_list = []
stack_list.append(1)  # push
stack_list.append(2)
stack_list.pop()      # pop
```

#### Using Linked List

**JavaScript:**
```javascript
class StackNode {
    constructor(val) {
        this.val = val;
        this.next = null;
    }
}

class LinkedStack {
    constructor() {
        this.top = null;
        this.length = 0;
    }

    push(val) {
        const newNode = new StackNode(val);
        newNode.next = this.top;
        this.top = newNode;
        this.length++;
    }

    pop() {
        if (!this.top) return null;

        const val = this.top.val;
        this.top = this.top.next;
        this.length--;
        return val;
    }

    peek() {
        return this.top ? this.top.val : null;
    }

    isEmpty() {
        return this.length === 0;
    }

    size() {
        return this.length;
    }
}
```

**Python:**
```python
class StackNode:
    def __init__(self, val):
        self.val = val
        self.next = None

class LinkedStack:
    def __init__(self):
        self.top = None
        self.length = 0

    def push(self, val):
        new_node = StackNode(val)
        new_node.next = self.top
        self.top = new_node
        self.length += 1

    def pop(self):
        if not self.top:
            return None

        val = self.top.val
        self.top = self.top.next
        self.length -= 1
        return val

    def peek(self):
        return self.top.val if self.top else None

    def is_empty(self):
        return self.length == 0

    def size(self):
        return self.length
```

### Stack Applications

#### 1. Balanced Parentheses

**JavaScript:**
```javascript
function isValidParentheses(s) {
    const stack = [];
    const pairs = {
        '(': ')',
        '[': ']',
        '{': '}'
    };

    for (const char of s) {
        if (char in pairs) {
            // Opening bracket
            stack.push(char);
        } else {
            // Closing bracket
            if (stack.length === 0) return false;

            const last = stack.pop();
            if (pairs[last] !== char) return false;
        }
    }

    return stack.length === 0;
}

console.log(isValidParentheses("()[]{}"));     // true
console.log(isValidParentheses("([)]"));       // false
console.log(isValidParentheses("{[()]}"));     // true

// Time: O(n), Space: O(n)
```

**Python:**
```python
def is_valid_parentheses(s):
    stack = []
    pairs = {
        '(': ')',
        '[': ']',
        '{': '}'
    }

    for char in s:
        if char in pairs:
            # Opening bracket
            stack.append(char)
        else:
            # Closing bracket
            if not stack:
                return False

            last = stack.pop()
            if pairs[last] != char:
                return False

    return len(stack) == 0

print(is_valid_parentheses("()[]{}"))     # True
print(is_valid_parentheses("([)]"))       # False
print(is_valid_parentheses("{[()]}"))     # True

# Time: O(n), Space: O(n)
```

#### 2. Evaluate Reverse Polish Notation

**JavaScript:**
```javascript
function evalRPN(tokens) {
    const stack = [];
    const operators = {
        '+': (a, b) => a + b,
        '-': (a, b) => a - b,
        '*': (a, b) => a * b,
        '/': (a, b) => Math.trunc(a / b)
    };

    for (const token of tokens) {
        if (token in operators) {
            const b = stack.pop();
            const a = stack.pop();
            stack.push(operators[token](a, b));
        } else {
            stack.push(parseInt(token));
        }
    }

    return stack[0];
}

console.log(evalRPN(["2", "1", "+", "3", "*"]));  // 9 ((2 + 1) * 3)
console.log(evalRPN(["4", "13", "5", "/", "+"])); // 6 (4 + (13 / 5))

// Time: O(n), Space: O(n)
```

**Python:**
```python
def eval_rpn(tokens):
    stack = []
    operators = {
        '+': lambda a, b: a + b,
        '-': lambda a, b: a - b,
        '*': lambda a, b: a * b,
        '/': lambda a, b: int(a / b)  # Truncate towards zero
    }

    for token in tokens:
        if token in operators:
            b = stack.pop()
            a = stack.pop()
            stack.append(operators[token](a, b))
        else:
            stack.append(int(token))

    return stack[0]

print(eval_rpn(["2", "1", "+", "3", "*"]))  # 9
print(eval_rpn(["4", "13", "5", "/", "+"]))  # 6

# Time: O(n), Space: O(n)
```

#### 3. Min Stack (O(1) for min operation)

**JavaScript:**
```javascript
class MinStack {
    constructor() {
        this.stack = [];
        this.minStack = [];
    }

    push(val) {
        this.stack.push(val);

        if (this.minStack.length === 0 || val <= this.getMin()) {
            this.minStack.push(val);
        }
    }

    pop() {
        const val = this.stack.pop();

        if (val === this.getMin()) {
            this.minStack.pop();
        }

        return val;
    }

    top() {
        return this.stack[this.stack.length - 1];
    }

    getMin() {
        return this.minStack[this.minStack.length - 1];
    }
}

// Example
const minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
console.log(minStack.getMin());  // -3
minStack.pop();
console.log(minStack.getMin());  // -2

// All operations: O(1)
```

**Python:**
```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []

    def push(self, val):
        self.stack.append(val)

        if not self.min_stack or val <= self.get_min():
            self.min_stack.append(val)

    def pop(self):
        val = self.stack.pop()

        if val == self.get_min():
            self.min_stack.pop()

        return val

    def top(self):
        return self.stack[-1]

    def get_min(self):
        return self.min_stack[-1]

# Example
min_stack = MinStack()
min_stack.push(-2)
min_stack.push(0)
min_stack.push(-3)
print(min_stack.get_min())  # -3
min_stack.pop()
print(min_stack.get_min())  # -2

# All operations: O(1)
```

---

## Queues

### What is a Queue?

A queue is a linear data structure where elements are added at the rear (enqueue) and removed from the front (dequeue). Think of it like a line at a store - first person in line is served first.

**Operations:**
- **Enqueue**: Add element to rear - O(1)
- **Dequeue**: Remove element from front - O(1)
- **Front/Peek**: View front element - O(1)
- **isEmpty**: Check if queue is empty - O(1)

### Implementation

#### Using Array

**JavaScript:**
```javascript
class Queue {
    constructor() {
        this.items = [];
    }

    // Add element to rear - O(1)
    enqueue(element) {
        this.items.push(element);
    }

    // Remove and return front element - O(n) due to shift
    // For O(1) dequeue, use linked list or circular array
    dequeue() {
        if (this.isEmpty()) {
            return null;
        }
        return this.items.shift();
    }

    // View front element - O(1)
    front() {
        if (this.isEmpty()) {
            return null;
        }
        return this.items[0];
    }

    isEmpty() {
        return this.items.length === 0;
    }

    size() {
        return this.items.length;
    }

    clear() {
        this.items = [];
    }

    print() {
        console.log(this.items.join(' <- '));
    }
}

// Example usage
const queue = new Queue();
queue.enqueue(1);
queue.enqueue(2);
queue.enqueue(3);
console.log(queue.front());  // 1
queue.dequeue();
console.log(queue.front());  // 2
```

**Python:**
```python
from collections import deque

class Queue:
    def __init__(self):
        # Using deque for O(1) operations on both ends
        self.items = deque()

    # Add element to rear - O(1)
    def enqueue(self, element):
        self.items.append(element)

    # Remove and return front element - O(1)
    def dequeue(self):
        if self.is_empty():
            return None
        return self.items.popleft()

    # View front element - O(1)
    def front(self):
        if self.is_empty():
            return None
        return self.items[0]

    def is_empty(self):
        return len(self.items) == 0

    def size(self):
        return len(self.items)

    def clear(self):
        self.items.clear()

    def print_queue(self):
        print(' <- '.join(map(str, self.items)))

# Example usage
queue = Queue()
queue.enqueue(1)
queue.enqueue(2)
queue.enqueue(3)
print(queue.front())  # 1
queue.dequeue()
print(queue.front())  # 2
```

#### Using Linked List

**JavaScript:**
```javascript
class QueueNode {
    constructor(val) {
        this.val = val;
        this.next = null;
    }
}

class LinkedQueue {
    constructor() {
        this.front = null;
        this.rear = null;
        this.length = 0;
    }

    enqueue(val) {
        const newNode = new QueueNode(val);

        if (this.isEmpty()) {
            this.front = newNode;
            this.rear = newNode;
        } else {
            this.rear.next = newNode;
            this.rear = newNode;
        }

        this.length++;
    }

    dequeue() {
        if (this.isEmpty()) return null;

        const val = this.front.val;
        this.front = this.front.next;

        if (!this.front) {
            this.rear = null;
        }

        this.length--;
        return val;
    }

    peek() {
        return this.front ? this.front.val : null;
    }

    isEmpty() {
        return this.length === 0;
    }

    size() {
        return this.length;
    }
}

// All operations: O(1)
```

**Python:**
```python
class QueueNode:
    def __init__(self, val):
        self.val = val
        self.next = None

class LinkedQueue:
    def __init__(self):
        self.front = None
        self.rear = None
        self.length = 0

    def enqueue(self, val):
        new_node = QueueNode(val)

        if self.is_empty():
            self.front = new_node
            self.rear = new_node
        else:
            self.rear.next = new_node
            self.rear = new_node

        self.length += 1

    def dequeue(self):
        if self.is_empty():
            return None

        val = self.front.val
        self.front = self.front.next

        if not self.front:
            self.rear = None

        self.length -= 1
        return val

    def peek(self):
        return self.front.val if self.front else None

    def is_empty(self):
        return self.length == 0

    def size(self):
        return self.length

# All operations: O(1)
```

### Queue Applications

#### 1. Level Order Traversal (BFS)

**JavaScript:**
```javascript
function levelOrder(root) {
    if (!root) return [];

    const result = [];
    const queue = [root];

    while (queue.length > 0) {
        const levelSize = queue.length;
        const currentLevel = [];

        for (let i = 0; i < levelSize; i++) {
            const node = queue.shift();
            currentLevel.push(node.val);

            if (node.left) queue.push(node.left);
            if (node.right) queue.push(node.right);
        }

        result.push(currentLevel);
    }

    return result;
}

// Time: O(n), Space: O(n)
```

**Python:**
```python
from collections import deque

def level_order(root):
    if not root:
        return []

    result = []
    queue = deque([root])

    while queue:
        level_size = len(queue)
        current_level = []

        for _ in range(level_size):
            node = queue.popleft()
            current_level.append(node.val)

            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)

        result.append(current_level)

    return result

# Time: O(n), Space: O(n)
```

#### 2. Moving Average from Data Stream

**JavaScript:**
```javascript
class MovingAverage {
    constructor(size) {
        this.size = size;
        this.queue = [];
        this.sum = 0;
    }

    next(val) {
        this.queue.push(val);
        this.sum += val;

        if (this.queue.length > this.size) {
            this.sum -= this.queue.shift();
        }

        return this.sum / this.queue.length;
    }
}

// Example
const ma = new MovingAverage(3);
console.log(ma.next(1));   // 1.0
console.log(ma.next(10));  // 5.5
console.log(ma.next(3));   // 4.67
console.log(ma.next(5));   // 6.0
```

**Python:**
```python
from collections import deque

class MovingAverage:
    def __init__(self, size):
        self.size = size
        self.queue = deque()
        self.sum = 0

    def next(self, val):
        self.queue.append(val)
        self.sum += val

        if len(self.queue) > self.size:
            self.sum -= self.queue.popleft()

        return self.sum / len(self.queue)

# Example
ma = MovingAverage(3)
print(ma.next(1))   # 1.0
print(ma.next(10))  # 5.5
print(ma.next(3))   # 4.67
print(ma.next(5))   # 6.0
```

---

## Advanced Variations

### 1. Circular Queue

**JavaScript:**
```javascript
class CircularQueue {
    constructor(k) {
        this.queue = new Array(k);
        this.capacity = k;
        this.front = 0;
        this.rear = -1;
        this.size = 0;
    }

    enQueue(value) {
        if (this.isFull()) return false;

        this.rear = (this.rear + 1) % this.capacity;
        this.queue[this.rear] = value;
        this.size++;
        return true;
    }

    deQueue() {
        if (this.isEmpty()) return false;

        this.front = (this.front + 1) % this.capacity;
        this.size--;
        return true;
    }

    Front() {
        return this.isEmpty() ? -1 : this.queue[this.front];
    }

    Rear() {
        return this.isEmpty() ? -1 : this.queue[this.rear];
    }

    isEmpty() {
        return this.size === 0;
    }

    isFull() {
        return this.size === this.capacity;
    }
}

// All operations: O(1)
```

**Python:**
```python
class CircularQueue:
    def __init__(self, k):
        self.queue = [None] * k
        self.capacity = k
        self.front = 0
        self.rear = -1
        self.size = 0

    def en_queue(self, value):
        if self.is_full():
            return False

        self.rear = (self.rear + 1) % self.capacity
        self.queue[self.rear] = value
        self.size += 1
        return True

    def de_queue(self):
        if self.is_empty():
            return False

        self.front = (self.front + 1) % self.capacity
        self.size -= 1
        return True

    def front_value(self):
        return -1 if self.is_empty() else self.queue[self.front]

    def rear_value(self):
        return -1 if self.is_empty() else self.queue[self.rear]

    def is_empty(self):
        return self.size == 0

    def is_full(self):
        return self.size == self.capacity

# All operations: O(1)
```

### 2. Deque (Double-Ended Queue)

**JavaScript:**
```javascript
class Deque {
    constructor() {
        this.items = [];
    }

    // Add to front
    addFront(element) {
        this.items.unshift(element);
    }

    // Add to rear
    addRear(element) {
        this.items.push(element);
    }

    // Remove from front
    removeFront() {
        if (this.isEmpty()) return null;
        return this.items.shift();
    }

    // Remove from rear
    removeRear() {
        if (this.isEmpty()) return null;
        return this.items.pop();
    }

    peekFront() {
        return this.items[0];
    }

    peekRear() {
        return this.items[this.items.length - 1];
    }

    isEmpty() {
        return this.items.length === 0;
    }

    size() {
        return this.items.length;
    }
}
```

**Python:**
```python
from collections import deque

class Deque:
    def __init__(self):
        self.items = deque()

    # Add to front - O(1)
    def add_front(self, element):
        self.items.appendleft(element)

    # Add to rear - O(1)
    def add_rear(self, element):
        self.items.append(element)

    # Remove from front - O(1)
    def remove_front(self):
        if self.is_empty():
            return None
        return self.items.popleft()

    # Remove from rear - O(1)
    def remove_rear(self):
        if self.is_empty():
            return None
        return self.items.pop()

    def peek_front(self):
        return self.items[0] if self.items else None

    def peek_rear(self):
        return self.items[-1] if self.items else None

    def is_empty(self):
        return len(self.items) == 0

    def size(self):
        return len(self.items)

# Python's collections.deque can be used directly
```

### 3. Priority Queue (Min Heap)

**JavaScript:**
```javascript
class PriorityQueue {
    constructor() {
        this.heap = [];
    }

    enqueue(val, priority) {
        this.heap.push({val, priority});
        this.bubbleUp(this.heap.length - 1);
    }

    dequeue() {
        if (this.isEmpty()) return null;

        const min = this.heap[0];
        const end = this.heap.pop();

        if (this.heap.length > 0) {
            this.heap[0] = end;
            this.bubbleDown(0);
        }

        return min.val;
    }

    bubbleUp(index) {
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

    bubbleDown(index) {
        while (true) {
            let minIndex = index;
            const leftChild = 2 * index + 1;
            const rightChild = 2 * index + 2;

            if (leftChild < this.heap.length &&
                this.heap[leftChild].priority < this.heap[minIndex].priority) {
                minIndex = leftChild;
            }

            if (rightChild < this.heap.length &&
                this.heap[rightChild].priority < this.heap[minIndex].priority) {
                minIndex = rightChild;
            }

            if (minIndex === index) break;

            [this.heap[index], this.heap[minIndex]] =
                [this.heap[minIndex], this.heap[index]];
            index = minIndex;
        }
    }

    isEmpty() {
        return this.heap.length === 0;
    }
}

// Enqueue: O(log n), Dequeue: O(log n)
```

**Python:**
```python
import heapq

class PriorityQueue:
    def __init__(self):
        self.heap = []
        self.counter = 0  # For stable ordering

    def enqueue(self, val, priority):
        # Use counter for stable ordering when priorities are equal
        heapq.heappush(self.heap, (priority, self.counter, val))
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

    def size(self):
        return len(self.heap)

# Enqueue: O(log n), Dequeue: O(log n)
```

---

## Applications

### Common Use Cases for Stacks:
1. **Function call stack** (recursion)
2. **Undo/Redo** operations
3. **Expression evaluation** (postfix, infix)
4. **Backtracking algorithms**
5. **Browser history**
6. **Syntax parsing**

### Common Use Cases for Queues:
1. **BFS traversal**
2. **Task scheduling**
3. **Request handling** in web servers
4. **Print queue**
5. **Message queues**
6. **Buffering** in streaming

---

## Practice Problems

### Stack Problems (Easy)
1. **Valid Parentheses** - Check if brackets are balanced
2. **Baseball Game** - Calculate score using stack
3. **Remove All Adjacent Duplicates** - Use stack to remove duplicates

### Stack Problems (Medium)
1. **Daily Temperatures** - Next warmer day
2. **Decode String** - Decode encoded string
3. **Asteroid Collision** - Simulate asteroid collisions

### Stack Problems (Hard)
1. **Largest Rectangle in Histogram** - Find largest rectangle
2. **Basic Calculator** - Implement calculator with +, -, (, )
3. **Longest Valid Parentheses** - Find longest valid substring

### Queue Problems (Easy)
1. **Implement Stack using Queues** - Use two queues
2. **Number of Recent Calls** - Count recent requests

### Queue Problems (Medium)
1. **Design Circular Queue** - Implement circular queue
2. **Number of Islands** (BFS) - Count islands using queue
3. **Perfect Squares** - Minimum numbers to sum to n

---

## Key Takeaways

1. **Stacks use LIFO** - Last In, First Out
2. **Queues use FIFO** - First In, First Out
3. **Both can be implemented** with arrays or linked lists
4. **Stacks are great for** backtracking and recursive problems
5. **Queues are essential for** BFS and task scheduling
6. **All basic operations** should be O(1)

---

## Next Chapter

Continue to [Trees and Graphs](./04-Trees-and-Graphs.md) to learn about hierarchical and network data structures.

[← Previous: Linked Lists](./02-Linked-Lists.md) | [Next: Trees and Graphs →](./04-Trees-and-Graphs.md)
