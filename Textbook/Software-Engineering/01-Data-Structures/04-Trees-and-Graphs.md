# Chapter 4: Trees and Graphs

## Table of Contents
- [Trees](#trees)
- [Binary Trees](#binary-trees)
- [Binary Search Trees](#binary-search-trees)
- [Balanced Trees](#balanced-trees)
- [Tree Traversals](#tree-traversals)
- [Graphs](#graphs)
- [Graph Representations](#graph-representations)
- [Graph Traversals](#graph-traversals)
- [Common Problems](#common-problems)

---

## Trees

### What is a Tree?

A tree is a hierarchical data structure consisting of nodes connected by edges. It has the following properties:
- One node is designated as the **root**
- Every node (except root) has exactly one parent
- No cycles exist
- There's exactly one path between any two nodes

**Terminology:**
- **Root**: Top node with no parent
- **Parent**: Node with children
- **Child**: Node with a parent
- **Leaf**: Node with no children
- **Height**: Longest path from node to leaf
- **Depth**: Path length from root to node
- **Level**: Depth + 1

---

## Binary Trees

### What is a Binary Tree?

A binary tree is a tree where each node has at most two children (left and right).

**Types:**
- **Full Binary Tree**: Every node has 0 or 2 children
- **Complete Binary Tree**: All levels filled except possibly last, filled left to right
- **Perfect Binary Tree**: All internal nodes have 2 children, all leaves at same level
- **Balanced Binary Tree**: Height difference between left and right subtrees ≤ 1

### Implementation

**JavaScript:**
```javascript
class TreeNode {
    constructor(val = 0, left = null, right = null) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}

class BinaryTree {
    constructor() {
        this.root = null;
    }

    // Insert using level-order (for complete tree)
    insert(val) {
        const newNode = new TreeNode(val);

        if (!this.root) {
            this.root = newNode;
            return;
        }

        const queue = [this.root];

        while (queue.length > 0) {
            const node = queue.shift();

            if (!node.left) {
                node.left = newNode;
                return;
            } else {
                queue.push(node.left);
            }

            if (!node.right) {
                node.right = newNode;
                return;
            } else {
                queue.push(node.right);
            }
        }
    }

    // Get tree height
    height(node = this.root) {
        if (!node) return -1;
        return 1 + Math.max(this.height(node.left), this.height(node.right));
    }

    // Count nodes
    size(node = this.root) {
        if (!node) return 0;
        return 1 + this.size(node.left) + this.size(node.right);
    }

    // Check if balanced
    isBalanced(node = this.root) {
        if (!node) return true;

        const leftHeight = this.height(node.left);
        const rightHeight = this.height(node.right);

        return Math.abs(leftHeight - rightHeight) <= 1 &&
               this.isBalanced(node.left) &&
               this.isBalanced(node.right);
    }
}
```

**Python:**
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class BinaryTree:
    def __init__(self):
        self.root = None

    # Insert using level-order (for complete tree)
    def insert(self, val):
        new_node = TreeNode(val)

        if not self.root:
            self.root = new_node
            return

        from collections import deque
        queue = deque([self.root])

        while queue:
            node = queue.popleft()

            if not node.left:
                node.left = new_node
                return
            else:
                queue.append(node.left)

            if not node.right:
                node.right = new_node
                return
            else:
                queue.append(node.right)

    # Get tree height
    def height(self, node=None):
        if node is None:
            node = self.root
        if not node:
            return -1
        return 1 + max(self.height(node.left), self.height(node.right))

    # Count nodes
    def size(self, node=None):
        if node is None:
            node = self.root
        if not node:
            return 0
        return 1 + self.size(node.left) + self.size(node.right)

    # Check if balanced
    def is_balanced(self, node=None):
        if node is None:
            node = self.root
        if not node:
            return True

        left_height = self.height(node.left)
        right_height = self.height(node.right)

        return (abs(left_height - right_height) <= 1 and
                self.is_balanced(node.left) and
                self.is_balanced(node.right))
```

---

## Binary Search Trees

### What is a BST?

A Binary Search Tree is a binary tree with the ordering property:
- All values in left subtree < node value
- All values in right subtree > node value
- Both subtrees are also BSTs

This property enables O(log n) search, insert, and delete operations (in balanced trees).

### Implementation

**JavaScript:**
```javascript
class BST {
    constructor() {
        this.root = null;
    }

    // Insert - O(h) where h is height
    insert(val) {
        this.root = this._insertRecursive(this.root, val);
    }

    _insertRecursive(node, val) {
        if (!node) return new TreeNode(val);

        if (val < node.val) {
            node.left = this._insertRecursive(node.left, val);
        } else if (val > node.val) {
            node.right = this._insertRecursive(node.right, val);
        }

        return node;
    }

    // Search - O(h)
    search(val) {
        return this._searchRecursive(this.root, val);
    }

    _searchRecursive(node, val) {
        if (!node) return false;

        if (val === node.val) return true;
        if (val < node.val) return this._searchRecursive(node.left, val);
        return this._searchRecursive(node.right, val);
    }

    // Find minimum
    findMin(node = this.root) {
        if (!node) return null;
        while (node.left) {
            node = node.left;
        }
        return node.val;
    }

    // Find maximum
    findMax(node = this.root) {
        if (!node) return null;
        while (node.right) {
            node = node.right;
        }
        return node.val;
    }

    // Delete - O(h)
    delete(val) {
        this.root = this._deleteRecursive(this.root, val);
    }

    _deleteRecursive(node, val) {
        if (!node) return null;

        if (val < node.val) {
            node.left = this._deleteRecursive(node.left, val);
        } else if (val > node.val) {
            node.right = this._deleteRecursive(node.right, val);
        } else {
            // Node found - handle 3 cases

            // Case 1: Leaf node
            if (!node.left && !node.right) {
                return null;
            }

            // Case 2: One child
            if (!node.left) return node.right;
            if (!node.right) return node.left;

            // Case 3: Two children
            // Replace with inorder successor (min in right subtree)
            node.val = this.findMin(node.right);
            node.right = this._deleteRecursive(node.right, node.val);
        }

        return node;
    }

    // Validate BST
    isValidBST(node = this.root, min = null, max = null) {
        if (!node) return true;

        if ((min !== null && node.val <= min) ||
            (max !== null && node.val >= max)) {
            return false;
        }

        return this.isValidBST(node.left, min, node.val) &&
               this.isValidBST(node.right, node.val, max);
    }
}

// Example usage
const bst = new BST();
[50, 30, 70, 20, 40, 60, 80].forEach(val => bst.insert(val));
console.log(bst.search(40));  // true
console.log(bst.search(25));  // false
```

**Python:**
```python
class BST:
    def __init__(self):
        self.root = None

    # Insert - O(h) where h is height
    def insert(self, val):
        self.root = self._insert_recursive(self.root, val)

    def _insert_recursive(self, node, val):
        if not node:
            return TreeNode(val)

        if val < node.val:
            node.left = self._insert_recursive(node.left, val)
        elif val > node.val:
            node.right = self._insert_recursive(node.right, val)

        return node

    # Search - O(h)
    def search(self, val):
        return self._search_recursive(self.root, val)

    def _search_recursive(self, node, val):
        if not node:
            return False

        if val == node.val:
            return True
        elif val < node.val:
            return self._search_recursive(node.left, val)
        else:
            return self._search_recursive(node.right, val)

    # Find minimum
    def find_min(self, node=None):
        if node is None:
            node = self.root
        if not node:
            return None

        while node.left:
            node = node.left
        return node.val

    # Find maximum
    def find_max(self, node=None):
        if node is None:
            node = self.root
        if not node:
            return None

        while node.right:
            node = node.right
        return node.val

    # Delete - O(h)
    def delete(self, val):
        self.root = self._delete_recursive(self.root, val)

    def _delete_recursive(self, node, val):
        if not node:
            return None

        if val < node.val:
            node.left = self._delete_recursive(node.left, val)
        elif val > node.val:
            node.right = self._delete_recursive(node.right, val)
        else:
            # Node found - handle 3 cases

            # Case 1: Leaf node
            if not node.left and not node.right:
                return None

            # Case 2: One child
            if not node.left:
                return node.right
            if not node.right:
                return node.left

            # Case 3: Two children
            # Replace with inorder successor
            node.val = self.find_min(node.right)
            node.right = self._delete_recursive(node.right, node.val)

        return node

    # Validate BST
    def is_valid_bst(self, node=None, min_val=None, max_val=None):
        if node is None:
            node = self.root
        if not node:
            return True

        if ((min_val is not None and node.val <= min_val) or
            (max_val is not None and node.val >= max_val)):
            return False

        return (self.is_valid_bst(node.left, min_val, node.val) and
                self.is_valid_bst(node.right, node.val, max_val))

# Example usage
bst = BST()
for val in [50, 30, 70, 20, 40, 60, 80]:
    bst.insert(val)
print(bst.search(40))  # True
print(bst.search(25))  # False
```

---

## Tree Traversals

### 1. Depth-First Search (DFS)

#### Inorder (Left, Root, Right)
Returns sorted order for BST

**JavaScript:**
```javascript
function inorderTraversal(root) {
    const result = [];

    function traverse(node) {
        if (!node) return;

        traverse(node.left);
        result.push(node.val);
        traverse(node.right);
    }

    traverse(root);
    return result;
}

// Iterative with stack
function inorderIterative(root) {
    const result = [];
    const stack = [];
    let current = root;

    while (current || stack.length > 0) {
        // Go to leftmost node
        while (current) {
            stack.push(current);
            current = current.left;
        }

        current = stack.pop();
        result.push(current.val);
        current = current.right;
    }

    return result;
}

// Time: O(n), Space: O(h) recursive, O(n) worst case
```

**Python:**
```python
def inorder_traversal(root):
    result = []

    def traverse(node):
        if not node:
            return

        traverse(node.left)
        result.append(node.val)
        traverse(node.right)

    traverse(root)
    return result

# Iterative with stack
def inorder_iterative(root):
    result = []
    stack = []
    current = root

    while current or stack:
        # Go to leftmost node
        while current:
            stack.append(current)
            current = current.left

        current = stack.pop()
        result.append(current.val)
        current = current.right

    return result

# Time: O(n), Space: O(h)
```

#### Preorder (Root, Left, Right)
Used for creating copy of tree

**JavaScript:**
```javascript
function preorderTraversal(root) {
    const result = [];

    function traverse(node) {
        if (!node) return;

        result.push(node.val);
        traverse(node.left);
        traverse(node.right);
    }

    traverse(root);
    return result;
}

// Iterative
function preorderIterative(root) {
    if (!root) return [];

    const result = [];
    const stack = [root];

    while (stack.length > 0) {
        const node = stack.pop();
        result.push(node.val);

        // Push right first so left is processed first
        if (node.right) stack.push(node.right);
        if (node.left) stack.push(node.left);
    }

    return result;
}
```

**Python:**
```python
def preorder_traversal(root):
    result = []

    def traverse(node):
        if not node:
            return

        result.append(node.val)
        traverse(node.left)
        traverse(node.right)

    traverse(root)
    return result

# Iterative
def preorder_iterative(root):
    if not root:
        return []

    result = []
    stack = [root]

    while stack:
        node = stack.pop()
        result.append(node.val)

        # Push right first so left is processed first
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)

    return result
```

#### Postorder (Left, Right, Root)
Used for deleting tree

**JavaScript:**
```javascript
function postorderTraversal(root) {
    const result = [];

    function traverse(node) {
        if (!node) return;

        traverse(node.left);
        traverse(node.right);
        result.push(node.val);
    }

    traverse(root);
    return result;
}

// Iterative (using two stacks)
function postorderIterative(root) {
    if (!root) return [];

    const result = [];
    const stack1 = [root];
    const stack2 = [];

    while (stack1.length > 0) {
        const node = stack1.pop();
        stack2.push(node);

        if (node.left) stack1.push(node.left);
        if (node.right) stack1.push(node.right);
    }

    while (stack2.length > 0) {
        result.push(stack2.pop().val);
    }

    return result;
}
```

**Python:**
```python
def postorder_traversal(root):
    result = []

    def traverse(node):
        if not node:
            return

        traverse(node.left)
        traverse(node.right)
        result.append(node.val)

    traverse(root)
    return result

# Iterative (using two stacks)
def postorder_iterative(root):
    if not root:
        return []

    result = []
    stack1 = [root]
    stack2 = []

    while stack1:
        node = stack1.pop()
        stack2.append(node)

        if node.left:
            stack1.append(node.left)
        if node.right:
            stack1.append(node.right)

    while stack2:
        result.append(stack2.pop().val)

    return result
```

### 2. Breadth-First Search (BFS) / Level-Order

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

// Time: O(n), Space: O(w) where w is max width
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

# Time: O(n), Space: O(w) where w is max width
```

---

## Balanced Trees

### AVL Tree

Self-balancing BST where height difference between left and right subtrees ≤ 1.

**Rotations:**

**JavaScript:**
```javascript
class AVLNode extends TreeNode {
    constructor(val) {
        super(val);
        this.height = 1;
    }
}

class AVLTree {
    getHeight(node) {
        return node ? node.height : 0;
    }

    getBalance(node) {
        return node ? this.getHeight(node.left) - this.getHeight(node.right) : 0;
    }

    updateHeight(node) {
        node.height = 1 + Math.max(
            this.getHeight(node.left),
            this.getHeight(node.right)
        );
    }

    // Right rotation
    rotateRight(y) {
        const x = y.left;
        const T2 = x.right;

        x.right = y;
        y.left = T2;

        this.updateHeight(y);
        this.updateHeight(x);

        return x;
    }

    // Left rotation
    rotateLeft(x) {
        const y = x.right;
        const T2 = y.left;

        y.left = x;
        x.right = T2;

        this.updateHeight(x);
        this.updateHeight(y);

        return y;
    }

    insert(node, val) {
        // 1. Perform normal BST insertion
        if (!node) return new AVLNode(val);

        if (val < node.val) {
            node.left = this.insert(node.left, val);
        } else if (val > node.val) {
            node.right = this.insert(node.right, val);
        } else {
            return node; // Duplicates not allowed
        }

        // 2. Update height
        this.updateHeight(node);

        // 3. Get balance factor
        const balance = this.getBalance(node);

        // 4. Balance the tree

        // Left Left Case
        if (balance > 1 && val < node.left.val) {
            return this.rotateRight(node);
        }

        // Right Right Case
        if (balance < -1 && val > node.right.val) {
            return this.rotateLeft(node);
        }

        // Left Right Case
        if (balance > 1 && val > node.left.val) {
            node.left = this.rotateLeft(node.left);
            return this.rotateRight(node);
        }

        // Right Left Case
        if (balance < -1 && val < node.right.val) {
            node.right = this.rotateRight(node.right);
            return this.rotateLeft(node);
        }

        return node;
    }
}

// All operations: O(log n)
```

**Python:**
```python
class AVLNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None
        self.height = 1

class AVLTree:
    def get_height(self, node):
        return node.height if node else 0

    def get_balance(self, node):
        if not node:
            return 0
        return self.get_height(node.left) - self.get_height(node.right)

    def update_height(self, node):
        node.height = 1 + max(
            self.get_height(node.left),
            self.get_height(node.right)
        )

    # Right rotation
    def rotate_right(self, y):
        x = y.left
        T2 = x.right

        x.right = y
        y.left = T2

        self.update_height(y)
        self.update_height(x)

        return x

    # Left rotation
    def rotate_left(self, x):
        y = x.right
        T2 = y.left

        y.left = x
        x.right = T2

        self.update_height(x)
        self.update_height(y)

        return y

    def insert(self, node, val):
        # 1. Perform normal BST insertion
        if not node:
            return AVLNode(val)

        if val < node.val:
            node.left = self.insert(node.left, val)
        elif val > node.val:
            node.right = self.insert(node.right, val)
        else:
            return node  # Duplicates not allowed

        # 2. Update height
        self.update_height(node)

        # 3. Get balance factor
        balance = self.get_balance(node)

        # 4. Balance the tree

        # Left Left Case
        if balance > 1 and val < node.left.val:
            return self.rotate_right(node)

        # Right Right Case
        if balance < -1 and val > node.right.val:
            return self.rotate_left(node)

        # Left Right Case
        if balance > 1 and val > node.left.val:
            node.left = self.rotate_left(node.left)
            return self.rotate_right(node)

        # Right Left Case
        if balance < -1 and val < node.right.val:
            node.right = self.rotate_right(node.right)
            return self.rotate_left(node)

        return node

# All operations: O(log n)
```

---

## Graphs

### What is a Graph?

A graph is a non-linear data structure consisting of:
- **Vertices (V)**: Nodes
- **Edges (E)**: Connections between nodes

**Types:**
- **Directed**: Edges have direction (A → B)
- **Undirected**: Edges are bidirectional (A — B)
- **Weighted**: Edges have weights/costs
- **Unweighted**: All edges have equal weight

---

## Graph Representations

### 1. Adjacency Matrix

2D array where matrix[i][j] = 1 if edge exists from i to j.

**JavaScript:**
```javascript
class GraphMatrix {
    constructor(numVertices) {
        this.numVertices = numVertices;
        this.matrix = Array(numVertices).fill(null)
            .map(() => Array(numVertices).fill(0));
    }

    addEdge(src, dest, weight = 1) {
        this.matrix[src][dest] = weight;
        // For undirected graph, uncomment:
        // this.matrix[dest][src] = weight;
    }

    removeEdge(src, dest) {
        this.matrix[src][dest] = 0;
        // For undirected graph:
        // this.matrix[dest][src] = 0;
    }

    hasEdge(src, dest) {
        return this.matrix[src][dest] !== 0;
    }

    print() {
        console.log(this.matrix.map(row => row.join(' ')).join('\n'));
    }
}

// Space: O(V²)
```

**Python:**
```python
class GraphMatrix:
    def __init__(self, num_vertices):
        self.num_vertices = num_vertices
        self.matrix = [[0] * num_vertices for _ in range(num_vertices)]

    def add_edge(self, src, dest, weight=1):
        self.matrix[src][dest] = weight
        # For undirected graph:
        # self.matrix[dest][src] = weight

    def remove_edge(self, src, dest):
        self.matrix[src][dest] = 0
        # For undirected graph:
        # self.matrix[dest][src] = 0

    def has_edge(self, src, dest):
        return self.matrix[src][dest] != 0

    def print_graph(self):
        for row in self.matrix:
            print(' '.join(map(str, row)))

# Space: O(V²)
```

### 2. Adjacency List

Array of lists where list[i] contains all neighbors of vertex i.

**JavaScript:**
```javascript
class GraphList {
    constructor() {
        this.adjacencyList = new Map();
    }

    addVertex(vertex) {
        if (!this.adjacencyList.has(vertex)) {
            this.adjacencyList.set(vertex, []);
        }
    }

    addEdge(src, dest, weight = 1) {
        this.addVertex(src);
        this.addVertex(dest);

        this.adjacencyList.get(src).push({ node: dest, weight });
        // For undirected graph:
        // this.adjacencyList.get(dest).push({ node: src, weight });
    }

    removeEdge(src, dest) {
        if (this.adjacencyList.has(src)) {
            this.adjacencyList.set(
                src,
                this.adjacencyList.get(src).filter(edge => edge.node !== dest)
            );
        }
        // For undirected graph:
        // if (this.adjacencyList.has(dest)) {
        //     this.adjacencyList.set(
        //         dest,
        //         this.adjacencyList.get(dest).filter(edge => edge.node !== src)
        //     );
        // }
    }

    getNeighbors(vertex) {
        return this.adjacencyList.get(vertex) || [];
    }

    print() {
        for (const [vertex, edges] of this.adjacencyList) {
            console.log(`${vertex} -> ${edges.map(e => e.node).join(', ')}`);
        }
    }
}

// Space: O(V + E)
```

**Python:**
```python
from collections import defaultdict

class GraphList:
    def __init__(self):
        self.adjacency_list = defaultdict(list)

    def add_vertex(self, vertex):
        if vertex not in self.adjacency_list:
            self.adjacency_list[vertex] = []

    def add_edge(self, src, dest, weight=1):
        self.add_vertex(src)
        self.add_vertex(dest)

        self.adjacency_list[src].append({'node': dest, 'weight': weight})
        # For undirected graph:
        # self.adjacency_list[dest].append({'node': src, 'weight': weight})

    def remove_edge(self, src, dest):
        if src in self.adjacency_list:
            self.adjacency_list[src] = [
                edge for edge in self.adjacency_list[src]
                if edge['node'] != dest
            ]
        # For undirected graph:
        # if dest in self.adjacency_list:
        #     self.adjacency_list[dest] = [
        #         edge for edge in self.adjacency_list[dest]
        #         if edge['node'] != src
        #     ]

    def get_neighbors(self, vertex):
        return self.adjacency_list.get(vertex, [])

    def print_graph(self):
        for vertex, edges in self.adjacency_list.items():
            neighbors = ', '.join(str(edge['node']) for edge in edges)
            print(f"{vertex} -> {neighbors}")

# Space: O(V + E)
```

---

## Graph Traversals

### 1. Depth-First Search (DFS)

Explores as far as possible along each branch before backtracking.

**JavaScript:**
```javascript
function dfs(graph, start) {
    const visited = new Set();
    const result = [];

    function traverse(vertex) {
        if (!vertex || visited.has(vertex)) return;

        visited.add(vertex);
        result.push(vertex);

        const neighbors = graph.getNeighbors(vertex);
        for (const edge of neighbors) {
            traverse(edge.node);
        }
    }

    traverse(start);
    return result;
}

// Iterative DFS
function dfsIterative(graph, start) {
    const visited = new Set();
    const result = [];
    const stack = [start];

    while (stack.length > 0) {
        const vertex = stack.pop();

        if (!visited.has(vertex)) {
            visited.add(vertex);
            result.push(vertex);

            const neighbors = graph.getNeighbors(vertex);
            for (const edge of neighbors.reverse()) {
                if (!visited.has(edge.node)) {
                    stack.push(edge.node);
                }
            }
        }
    }

    return result;
}

// Time: O(V + E), Space: O(V)
```

**Python:**
```python
def dfs(graph, start):
    visited = set()
    result = []

    def traverse(vertex):
        if not vertex or vertex in visited:
            return

        visited.add(vertex)
        result.append(vertex)

        neighbors = graph.get_neighbors(vertex)
        for edge in neighbors:
            traverse(edge['node'])

    traverse(start)
    return result

# Iterative DFS
def dfs_iterative(graph, start):
    visited = set()
    result = []
    stack = [start]

    while stack:
        vertex = stack.pop()

        if vertex not in visited:
            visited.add(vertex)
            result.append(vertex)

            neighbors = graph.get_neighbors(vertex)
            for edge in reversed(neighbors):
                if edge['node'] not in visited:
                    stack.append(edge['node'])

    return result

# Time: O(V + E), Space: O(V)
```

### 2. Breadth-First Search (BFS)

Explores all vertices at current depth before moving to next depth.

**JavaScript:**
```javascript
function bfs(graph, start) {
    const visited = new Set();
    const result = [];
    const queue = [start];
    visited.add(start);

    while (queue.length > 0) {
        const vertex = queue.shift();
        result.push(vertex);

        const neighbors = graph.getNeighbors(vertex);
        for (const edge of neighbors) {
            if (!visited.has(edge.node)) {
                visited.add(edge.node);
                queue.push(edge.node);
            }
        }
    }

    return result;
}

// Time: O(V + E), Space: O(V)
```

**Python:**
```python
from collections import deque

def bfs(graph, start):
    visited = set()
    result = []
    queue = deque([start])
    visited.add(start)

    while queue:
        vertex = queue.popleft()
        result.append(vertex)

        neighbors = graph.get_neighbors(vertex)
        for edge in neighbors:
            if edge['node'] not in visited:
                visited.add(edge['node'])
                queue.append(edge['node'])

    return result

# Time: O(V + E), Space: O(V)
```

---

## Common Problems

### 1. Lowest Common Ancestor (LCA)

**JavaScript:**
```javascript
function lowestCommonAncestor(root, p, q) {
    if (!root || root === p || root === q) {
        return root;
    }

    const left = lowestCommonAncestor(root.left, p, q);
    const right = lowestCommonAncestor(root.right, p, q);

    if (left && right) return root;
    return left || right;
}

// Time: O(n), Space: O(h)
```

**Python:**
```python
def lowest_common_ancestor(root, p, q):
    if not root or root == p or root == q:
        return root

    left = lowest_common_ancestor(root.left, p, q)
    right = lowest_common_ancestor(root.right, p, q)

    if left and right:
        return root
    return left or right

# Time: O(n), Space: O(h)
```

### 2. Maximum Path Sum

**JavaScript:**
```javascript
function maxPathSum(root) {
    let maxSum = -Infinity;

    function maxGain(node) {
        if (!node) return 0;

        const leftGain = Math.max(maxGain(node.left), 0);
        const rightGain = Math.max(maxGain(node.right), 0);

        const pathSum = node.val + leftGain + rightGain;
        maxSum = Math.max(maxSum, pathSum);

        return node.val + Math.max(leftGain, rightGain);
    }

    maxGain(root);
    return maxSum;
}

// Time: O(n), Space: O(h)
```

**Python:**
```python
def max_path_sum(root):
    max_sum = float('-inf')

    def max_gain(node):
        nonlocal max_sum

        if not node:
            return 0

        left_gain = max(max_gain(node.left), 0)
        right_gain = max(max_gain(node.right), 0)

        path_sum = node.val + left_gain + right_gain
        max_sum = max(max_sum, path_sum)

        return node.val + max(left_gain, right_gain)

    max_gain(root)
    return max_sum

# Time: O(n), Space: O(h)
```

### 3. Cycle Detection in Directed Graph

**JavaScript:**
```javascript
function hasCycle(graph) {
    const visited = new Set();
    const recursionStack = new Set();

    function dfs(vertex) {
        visited.add(vertex);
        recursionStack.add(vertex);

        const neighbors = graph.getNeighbors(vertex);
        for (const edge of neighbors) {
            const neighbor = edge.node;

            if (!visited.has(neighbor)) {
                if (dfs(neighbor)) return true;
            } else if (recursionStack.has(neighbor)) {
                return true;
            }
        }

        recursionStack.delete(vertex);
        return false;
    }

    for (const vertex of graph.adjacencyList.keys()) {
        if (!visited.has(vertex)) {
            if (dfs(vertex)) return true;
        }
    }

    return false;
}

// Time: O(V + E), Space: O(V)
```

**Python:**
```python
def has_cycle(graph):
    visited = set()
    recursion_stack = set()

    def dfs(vertex):
        visited.add(vertex)
        recursion_stack.add(vertex)

        neighbors = graph.get_neighbors(vertex)
        for edge in neighbors:
            neighbor = edge['node']

            if neighbor not in visited:
                if dfs(neighbor):
                    return True
            elif neighbor in recursion_stack:
                return True

        recursion_stack.remove(vertex)
        return False

    for vertex in graph.adjacency_list.keys():
        if vertex not in visited:
            if dfs(vertex):
                return True

    return False

# Time: O(V + E), Space: O(V)
```

---

## Practice Problems

### Tree Problems (Easy)
1. **Maximum Depth of Binary Tree**
2. **Same Tree** - Check if two trees are identical
3. **Invert Binary Tree**
4. **Symmetric Tree**

### Tree Problems (Medium)
1. **Validate Binary Search Tree**
2. **Binary Tree Level Order Traversal**
3. **Construct Tree from Preorder and Inorder**
4. **Kth Smallest Element in BST**
5. **Lowest Common Ancestor**

### Tree Problems (Hard)
1. **Binary Tree Maximum Path Sum**
2. **Serialize and Deserialize Binary Tree**
3. **Recover Binary Search Tree**

### Graph Problems (Easy)
1. **Find Center of Star Graph**
2. **Number of Connected Components**

### Graph Problems (Medium)
1. **Number of Islands**
2. **Clone Graph**
3. **Course Schedule** (Topological Sort)
4. **Pacific Atlantic Water Flow**

### Graph Problems (Hard)
1. **Word Ladder**
2. **Alien Dictionary**
3. **Critical Connections in a Network**

---

## Key Takeaways

1. **Trees are hierarchical** - one root, no cycles
2. **BSTs enable O(log n)** search when balanced
3. **DFS uses stack** (or recursion), BFS uses queue
4. **Graphs can be cyclic** - detect with recursion stack
5. **Adjacency list is better** for sparse graphs (space: O(V + E))
6. **Adjacency matrix is better** for dense graphs (constant-time edge lookup)

---

## Next Chapter

Continue to [Hash Tables](./05-Hash-Tables.md) to learn about constant-time lookup data structures.

[← Previous: Stacks and Queues](./03-Stacks-and-Queues.md) | [Next: Hash Tables →](./05-Hash-Tables.md)
