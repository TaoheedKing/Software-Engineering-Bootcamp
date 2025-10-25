# Chapter 2: Linked Lists

## Table of Contents
- [Introduction](#introduction)
- [Types of Linked Lists](#types-of-linked-lists)
- [Implementation](#implementation)
- [Common Operations](#common-operations)
- [Common Patterns](#common-patterns)
- [Time and Space Complexity](#time-and-space-complexity)
- [Practice Problems](#practice-problems)

---

## Introduction

A linked list is a linear data structure where elements are stored in nodes. Each node contains data and a reference (link) to the next node in the sequence. Unlike arrays, linked lists don't store elements in contiguous memory locations.

### Advantages
- **Dynamic size**: Grows and shrinks at runtime
- **Efficient insertion/deletion**: O(1) when you have a pointer to the location
- **No pre-allocation**: Memory allocated as needed

### Disadvantages
- **No random access**: Must traverse from head to access elements - O(n)
- **Extra memory**: Each node requires additional space for pointer(s)
- **Not cache-friendly**: Nodes scattered in memory

---

## Types of Linked Lists

### 1. Singly Linked List
Each node points to the next node. Last node points to null.

```
[data|next] -> [data|next] -> [data|next] -> null
```

### 2. Doubly Linked List
Each node has pointers to both next and previous nodes.

```
null <- [prev|data|next] <-> [prev|data|next] <-> [prev|data|next] -> null
```

### 3. Circular Linked List
Last node points back to the first node (can be singly or doubly).

```
     ┌─────────────────────────┐
     ↓                         |
[data|next] -> [data|next] -> [data|next]
```

---

## Implementation

### Singly Linked List

**JavaScript:**
```javascript
class ListNode {
    constructor(val = 0, next = null) {
        this.val = val;
        this.next = next;
    }
}

class LinkedList {
    constructor() {
        this.head = null;
        this.size = 0;
    }

    // Insert at the beginning - O(1)
    insertAtHead(val) {
        const newNode = new ListNode(val);
        newNode.next = this.head;
        this.head = newNode;
        this.size++;
    }

    // Insert at the end - O(n)
    insertAtTail(val) {
        const newNode = new ListNode(val);

        if (!this.head) {
            this.head = newNode;
        } else {
            let current = this.head;
            while (current.next) {
                current = current.next;
            }
            current.next = newNode;
        }
        this.size++;
    }

    // Insert at specific position - O(n)
    insertAt(val, index) {
        if (index < 0 || index > this.size) {
            return false;
        }

        if (index === 0) {
            this.insertAtHead(val);
            return true;
        }

        const newNode = new ListNode(val);
        let current = this.head;

        for (let i = 0; i < index - 1; i++) {
            current = current.next;
        }

        newNode.next = current.next;
        current.next = newNode;
        this.size++;
        return true;
    }

    // Delete from beginning - O(1)
    deleteHead() {
        if (!this.head) return null;

        const val = this.head.val;
        this.head = this.head.next;
        this.size--;
        return val;
    }

    // Delete from end - O(n)
    deleteTail() {
        if (!this.head) return null;

        if (!this.head.next) {
            const val = this.head.val;
            this.head = null;
            this.size--;
            return val;
        }

        let current = this.head;
        while (current.next.next) {
            current = current.next;
        }

        const val = current.next.val;
        current.next = null;
        this.size--;
        return val;
    }

    // Delete at specific position - O(n)
    deleteAt(index) {
        if (index < 0 || index >= this.size) {
            return null;
        }

        if (index === 0) {
            return this.deleteHead();
        }

        let current = this.head;
        for (let i = 0; i < index - 1; i++) {
            current = current.next;
        }

        const val = current.next.val;
        current.next = current.next.next;
        this.size--;
        return val;
    }

    // Search - O(n)
    find(val) {
        let current = this.head;
        let index = 0;

        while (current) {
            if (current.val === val) {
                return index;
            }
            current = current.next;
            index++;
        }

        return -1;
    }

    // Get value at index - O(n)
    get(index) {
        if (index < 0 || index >= this.size) {
            return null;
        }

        let current = this.head;
        for (let i = 0; i < index; i++) {
            current = current.next;
        }

        return current.val;
    }

    // Print list
    print() {
        const values = [];
        let current = this.head;

        while (current) {
            values.push(current.val);
            current = current.next;
        }

        console.log(values.join(' -> '));
    }

    // Reverse the list - O(n)
    reverse() {
        let prev = null;
        let current = this.head;

        while (current) {
            const next = current.next;
            current.next = prev;
            prev = current;
            current = next;
        }

        this.head = prev;
    }
}

// Example usage
const list = new LinkedList();
list.insertAtTail(1);
list.insertAtTail(2);
list.insertAtTail(3);
list.print();  // 1 -> 2 -> 3
list.reverse();
list.print();  // 3 -> 2 -> 1
```

**Python:**
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class LinkedList:
    def __init__(self):
        self.head = None
        self.size = 0

    # Insert at the beginning - O(1)
    def insert_at_head(self, val):
        new_node = ListNode(val)
        new_node.next = self.head
        self.head = new_node
        self.size += 1

    # Insert at the end - O(n)
    def insert_at_tail(self, val):
        new_node = ListNode(val)

        if not self.head:
            self.head = new_node
        else:
            current = self.head
            while current.next:
                current = current.next
            current.next = new_node

        self.size += 1

    # Insert at specific position - O(n)
    def insert_at(self, val, index):
        if index < 0 or index > self.size:
            return False

        if index == 0:
            self.insert_at_head(val)
            return True

        new_node = ListNode(val)
        current = self.head

        for _ in range(index - 1):
            current = current.next

        new_node.next = current.next
        current.next = new_node
        self.size += 1
        return True

    # Delete from beginning - O(1)
    def delete_head(self):
        if not self.head:
            return None

        val = self.head.val
        self.head = self.head.next
        self.size -= 1
        return val

    # Delete from end - O(n)
    def delete_tail(self):
        if not self.head:
            return None

        if not self.head.next:
            val = self.head.val
            self.head = None
            self.size -= 1
            return val

        current = self.head
        while current.next.next:
            current = current.next

        val = current.next.val
        current.next = None
        self.size -= 1
        return val

    # Delete at specific position - O(n)
    def delete_at(self, index):
        if index < 0 or index >= self.size:
            return None

        if index == 0:
            return self.delete_head()

        current = self.head
        for _ in range(index - 1):
            current = current.next

        val = current.next.val
        current.next = current.next.next
        self.size -= 1
        return val

    # Search - O(n)
    def find(self, val):
        current = self.head
        index = 0

        while current:
            if current.val == val:
                return index
            current = current.next
            index += 1

        return -1

    # Get value at index - O(n)
    def get(self, index):
        if index < 0 or index >= self.size:
            return None

        current = self.head
        for _ in range(index):
            current = current.next

        return current.val

    # Print list
    def print_list(self):
        values = []
        current = self.head

        while current:
            values.append(str(current.val))
            current = current.next

        print(' -> '.join(values))

    # Reverse the list - O(n)
    def reverse(self):
        prev = None
        current = self.head

        while current:
            next_node = current.next
            current.next = prev
            prev = current
            current = next_node

        self.head = prev

# Example usage
linked_list = LinkedList()
linked_list.insert_at_tail(1)
linked_list.insert_at_tail(2)
linked_list.insert_at_tail(3)
linked_list.print_list()  # 1 -> 2 -> 3
linked_list.reverse()
linked_list.print_list()  # 3 -> 2 -> 1
```

### Doubly Linked List

**JavaScript:**
```javascript
class DoublyListNode {
    constructor(val = 0, prev = null, next = null) {
        this.val = val;
        this.prev = prev;
        this.next = next;
    }
}

class DoublyLinkedList {
    constructor() {
        this.head = null;
        this.tail = null;
        this.size = 0;
    }

    // Insert at beginning - O(1)
    insertAtHead(val) {
        const newNode = new DoublyListNode(val);

        if (!this.head) {
            this.head = newNode;
            this.tail = newNode;
        } else {
            newNode.next = this.head;
            this.head.prev = newNode;
            this.head = newNode;
        }

        this.size++;
    }

    // Insert at end - O(1)
    insertAtTail(val) {
        const newNode = new DoublyListNode(val);

        if (!this.tail) {
            this.head = newNode;
            this.tail = newNode;
        } else {
            newNode.prev = this.tail;
            this.tail.next = newNode;
            this.tail = newNode;
        }

        this.size++;
    }

    // Delete from beginning - O(1)
    deleteHead() {
        if (!this.head) return null;

        const val = this.head.val;

        if (this.head === this.tail) {
            this.head = null;
            this.tail = null;
        } else {
            this.head = this.head.next;
            this.head.prev = null;
        }

        this.size--;
        return val;
    }

    // Delete from end - O(1)
    deleteTail() {
        if (!this.tail) return null;

        const val = this.tail.val;

        if (this.head === this.tail) {
            this.head = null;
            this.tail = null;
        } else {
            this.tail = this.tail.prev;
            this.tail.next = null;
        }

        this.size--;
        return val;
    }

    // Print forward
    printForward() {
        const values = [];
        let current = this.head;

        while (current) {
            values.push(current.val);
            current = current.next;
        }

        console.log(values.join(' <-> '));
    }

    // Print backward
    printBackward() {
        const values = [];
        let current = this.tail;

        while (current) {
            values.push(current.val);
            current = current.prev;
        }

        console.log(values.join(' <-> '));
    }
}

// Example usage
const dlist = new DoublyLinkedList();
dlist.insertAtTail(1);
dlist.insertAtTail(2);
dlist.insertAtTail(3);
dlist.printForward();   // 1 <-> 2 <-> 3
dlist.printBackward();  // 3 <-> 2 <-> 1
```

**Python:**
```python
class DoublyListNode:
    def __init__(self, val=0, prev=None, next=None):
        self.val = val
        self.prev = prev
        self.next = next

class DoublyLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None
        self.size = 0

    # Insert at beginning - O(1)
    def insert_at_head(self, val):
        new_node = DoublyListNode(val)

        if not self.head:
            self.head = new_node
            self.tail = new_node
        else:
            new_node.next = self.head
            self.head.prev = new_node
            self.head = new_node

        self.size += 1

    # Insert at end - O(1)
    def insert_at_tail(self, val):
        new_node = DoublyListNode(val)

        if not self.tail:
            self.head = new_node
            self.tail = new_node
        else:
            new_node.prev = self.tail
            self.tail.next = new_node
            self.tail = new_node

        self.size += 1

    # Delete from beginning - O(1)
    def delete_head(self):
        if not self.head:
            return None

        val = self.head.val

        if self.head == self.tail:
            self.head = None
            self.tail = None
        else:
            self.head = self.head.next
            self.head.prev = None

        self.size -= 1
        return val

    # Delete from end - O(1)
    def delete_tail(self):
        if not self.tail:
            return None

        val = self.tail.val

        if self.head == self.tail:
            self.head = None
            self.tail = None
        else:
            self.tail = self.tail.prev
            self.tail.next = None

        self.size -= 1
        return val

    # Print forward
    def print_forward(self):
        values = []
        current = self.head

        while current:
            values.append(str(current.val))
            current = current.next

        print(' <-> '.join(values))

    # Print backward
    def print_backward(self):
        values = []
        current = self.tail

        while current:
            values.append(str(current.val))
            current = current.prev

        print(' <-> '.join(values))

# Example usage
dlist = DoublyLinkedList()
dlist.insert_at_tail(1)
dlist.insert_at_tail(2)
dlist.insert_at_tail(3)
dlist.print_forward()   # 1 <-> 2 <-> 3
dlist.print_backward()  # 3 <-> 2 <-> 1
```

---

## Common Operations

### 1. Reverse a Linked List

**Iterative Approach:**

**JavaScript:**
```javascript
function reverseList(head) {
    let prev = null;
    let current = head;

    while (current) {
        const next = current.next;
        current.next = prev;
        prev = current;
        current = next;
    }

    return prev;
}

// Time: O(n), Space: O(1)
```

**Python:**
```python
def reverse_list(head):
    prev = None
    current = head

    while current:
        next_node = current.next
        current.next = prev
        prev = current
        current = next_node

    return prev

# Time: O(n), Space: O(1)
```

**Recursive Approach:**

**JavaScript:**
```javascript
function reverseListRecursive(head) {
    if (!head || !head.next) {
        return head;
    }

    const newHead = reverseListRecursive(head.next);
    head.next.next = head;
    head.next = null;

    return newHead;
}

// Time: O(n), Space: O(n) due to recursion stack
```

**Python:**
```python
def reverse_list_recursive(head):
    if not head or not head.next:
        return head

    new_head = reverse_list_recursive(head.next)
    head.next.next = head
    head.next = None

    return new_head

# Time: O(n), Space: O(n)
```

### 2. Detect Cycle (Floyd's Cycle Detection)

**JavaScript:**
```javascript
function hasCycle(head) {
    if (!head || !head.next) return false;

    let slow = head;
    let fast = head;

    while (fast && fast.next) {
        slow = slow.next;
        fast = fast.next.next;

        if (slow === fast) {
            return true;
        }
    }

    return false;
}

// Find cycle start if exists
function detectCycle(head) {
    if (!head || !head.next) return null;

    let slow = head;
    let fast = head;

    // Find meeting point
    while (fast && fast.next) {
        slow = slow.next;
        fast = fast.next.next;

        if (slow === fast) {
            // Cycle exists, find start
            slow = head;
            while (slow !== fast) {
                slow = slow.next;
                fast = fast.next;
            }
            return slow;
        }
    }

    return null;
}

// Time: O(n), Space: O(1)
```

**Python:**
```python
def has_cycle(head):
    if not head or not head.next:
        return False

    slow = head
    fast = head

    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

        if slow == fast:
            return True

    return False

def detect_cycle(head):
    if not head or not head.next:
        return None

    slow = head
    fast = head

    # Find meeting point
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

        if slow == fast:
            # Cycle exists, find start
            slow = head
            while slow != fast:
                slow = slow.next
                fast = fast.next
            return slow

    return None

# Time: O(n), Space: O(1)
```

### 3. Find Middle of Linked List

**JavaScript:**
```javascript
function findMiddle(head) {
    if (!head) return null;

    let slow = head;
    let fast = head;

    while (fast && fast.next) {
        slow = slow.next;
        fast = fast.next.next;
    }

    return slow;  // For even length, returns second middle
}

// Time: O(n), Space: O(1)
```

**Python:**
```python
def find_middle(head):
    if not head:
        return None

    slow = head
    fast = head

    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

    return slow  # For even length, returns second middle

# Time: O(n), Space: O(1)
```

### 4. Merge Two Sorted Lists

**JavaScript:**
```javascript
function mergeTwoLists(l1, l2) {
    const dummy = new ListNode(0);
    let current = dummy;

    while (l1 && l2) {
        if (l1.val <= l2.val) {
            current.next = l1;
            l1 = l1.next;
        } else {
            current.next = l2;
            l2 = l2.next;
        }
        current = current.next;
    }

    // Attach remaining nodes
    current.next = l1 || l2;

    return dummy.next;
}

// Recursive approach
function mergeTwoListsRecursive(l1, l2) {
    if (!l1) return l2;
    if (!l2) return l1;

    if (l1.val <= l2.val) {
        l1.next = mergeTwoListsRecursive(l1.next, l2);
        return l1;
    } else {
        l2.next = mergeTwoListsRecursive(l1, l2.next);
        return l2;
    }
}

// Time: O(n + m), Space: O(1) iterative, O(n + m) recursive
```

**Python:**
```python
def merge_two_lists(l1, l2):
    dummy = ListNode(0)
    current = dummy

    while l1 and l2:
        if l1.val <= l2.val:
            current.next = l1
            l1 = l1.next
        else:
            current.next = l2
            l2 = l2.next
        current = current.next

    # Attach remaining nodes
    current.next = l1 or l2

    return dummy.next

# Recursive approach
def merge_two_lists_recursive(l1, l2):
    if not l1:
        return l2
    if not l2:
        return l1

    if l1.val <= l2.val:
        l1.next = merge_two_lists_recursive(l1.next, l2)
        return l1
    else:
        l2.next = merge_two_lists_recursive(l1, l2.next)
        return l2

# Time: O(n + m), Space: O(1) iterative, O(n + m) recursive
```

### 5. Remove Nth Node From End

**JavaScript:**
```javascript
function removeNthFromEnd(head, n) {
    const dummy = new ListNode(0);
    dummy.next = head;

    let first = dummy;
    let second = dummy;

    // Move first pointer n+1 steps ahead
    for (let i = 0; i <= n; i++) {
        first = first.next;
    }

    // Move both pointers until first reaches end
    while (first) {
        first = first.next;
        second = second.next;
    }

    // Remove nth node
    second.next = second.next.next;

    return dummy.next;
}

// Time: O(n), Space: O(1)
```

**Python:**
```python
def remove_nth_from_end(head, n):
    dummy = ListNode(0)
    dummy.next = head

    first = dummy
    second = dummy

    # Move first pointer n+1 steps ahead
    for _ in range(n + 1):
        first = first.next

    # Move both pointers until first reaches end
    while first:
        first = first.next
        second = second.next

    # Remove nth node
    second.next = second.next.next

    return dummy.next

# Time: O(n), Space: O(1)
```

---

## Common Patterns

### Pattern 1: Two Pointers / Fast & Slow

Used for finding middle, detecting cycles, finding nth from end.

**Example: Palindrome Linked List**

**JavaScript:**
```javascript
function isPalindrome(head) {
    if (!head || !head.next) return true;

    // Find middle
    let slow = head, fast = head;
    while (fast && fast.next) {
        slow = slow.next;
        fast = fast.next.next;
    }

    // Reverse second half
    let prev = null, current = slow;
    while (current) {
        const next = current.next;
        current.next = prev;
        prev = current;
        current = next;
    }

    // Compare
    let left = head, right = prev;
    while (right) {
        if (left.val !== right.val) return false;
        left = left.next;
        right = right.next;
    }

    return true;
}

// Time: O(n), Space: O(1)
```

**Python:**
```python
def is_palindrome(head):
    if not head or not head.next:
        return True

    # Find middle
    slow, fast = head, head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next

    # Reverse second half
    prev, current = None, slow
    while current:
        next_node = current.next
        current.next = prev
        prev = current
        current = next_node

    # Compare
    left, right = head, prev
    while right:
        if left.val != right.val:
            return False
        left = left.next
        right = right.next

    return True

# Time: O(n), Space: O(1)
```

### Pattern 2: Dummy Node

Simplifies edge cases for insertion/deletion at head.

**Example: Remove Elements**

**JavaScript:**
```javascript
function removeElements(head, val) {
    const dummy = new ListNode(0);
    dummy.next = head;
    let current = dummy;

    while (current.next) {
        if (current.next.val === val) {
            current.next = current.next.next;
        } else {
            current = current.next;
        }
    }

    return dummy.next;
}

// Time: O(n), Space: O(1)
```

**Python:**
```python
def remove_elements(head, val):
    dummy = ListNode(0)
    dummy.next = head
    current = dummy

    while current.next:
        if current.next.val == val:
            current.next = current.next.next
        else:
            current = current.next

    return dummy.next

# Time: O(n), Space: O(1)
```

### Pattern 3: Multiple Pointers

**Example: Reorder List (L0 → Ln → L1 → Ln-1 → L2 → Ln-2 → ...)**

**JavaScript:**
```javascript
function reorderList(head) {
    if (!head || !head.next) return;

    // Find middle
    let slow = head, fast = head;
    while (fast.next && fast.next.next) {
        slow = slow.next;
        fast = fast.next.next;
    }

    // Reverse second half
    let prev = null, current = slow.next;
    slow.next = null;

    while (current) {
        const next = current.next;
        current.next = prev;
        prev = current;
        current = next;
    }

    // Merge two halves
    let first = head, second = prev;
    while (second) {
        const temp1 = first.next;
        const temp2 = second.next;

        first.next = second;
        second.next = temp1;

        first = temp1;
        second = temp2;
    }
}

// Time: O(n), Space: O(1)
```

**Python:**
```python
def reorder_list(head):
    if not head or not head.next:
        return

    # Find middle
    slow, fast = head, head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next

    # Reverse second half
    prev, current = None, slow.next
    slow.next = None

    while current:
        next_node = current.next
        current.next = prev
        prev = current
        current = next_node

    # Merge two halves
    first, second = head, prev
    while second:
        temp1 = first.next
        temp2 = second.next

        first.next = second
        second.next = temp1

        first = temp1
        second = temp2

# Time: O(n), Space: O(1)
```

---

## Time and Space Complexity

| Operation | Singly Linked List | Doubly Linked List |
|-----------|-------------------|-------------------|
| Access | O(n) | O(n) |
| Search | O(n) | O(n) |
| Insert at head | O(1) | O(1) |
| Insert at tail | O(n) without tail pointer, O(1) with | O(1) with tail pointer |
| Insert at position | O(n) | O(n) |
| Delete at head | O(1) | O(1) |
| Delete at tail | O(n) | O(1) with tail pointer |
| Delete at position | O(n) | O(n) |
| Space | O(n) | O(n) (more due to extra pointer) |

---

## Practice Problems

### Easy
1. **Reverse Linked List** - Reverse a singly linked list
2. **Merge Two Sorted Lists** - Merge two sorted linked lists
3. **Remove Duplicates from Sorted List** - Remove duplicate values
4. **Linked List Cycle** - Detect if a cycle exists

### Medium
1. **Add Two Numbers** - Add two numbers represented by linked lists
2. **Remove Nth Node From End** - Remove the nth node from the end
3. **Reorder List** - Reorder list in specific pattern
4. **Sort List** - Sort a linked list using O(n log n) time
5. **Copy List with Random Pointer** - Deep copy a list with random pointers

### Hard
1. **Merge k Sorted Lists** - Merge k sorted linked lists
2. **Reverse Nodes in k-Group** - Reverse nodes in groups of k
3. **LRU Cache** - Implement LRU cache using doubly linked list

---

## Key Takeaways

1. **Linked lists offer O(1) insertion/deletion** when you have a pointer to the location
2. **No random access** - must traverse from head
3. **Two pointers technique** (fast & slow) is very common
4. **Dummy nodes** simplify edge cases
5. **Reversing** is a fundamental operation used in many problems
6. **Recursion** often provides elegant solutions but uses O(n) space

---

## Next Chapter

Continue to [Stacks and Queues](./03-Stacks-and-Queues.md) to learn about LIFO and FIFO data structures.

[← Previous: Arrays and Strings](./01-Arrays-and-Strings.md) | [Next: Stacks and Queues →](./03-Stacks-and-Queues.md)
