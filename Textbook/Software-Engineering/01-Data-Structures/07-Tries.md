# Chapter 7: Tries (Prefix Trees)

## Table of Contents
- [Introduction](#introduction)
- [Trie Structure](#trie-structure)
- [Implementation](#implementation)
- [Operations](#operations)
- [Applications](#applications)
- [Variations](#variations)
- [Practice Problems](#practice-problems)

---

## Introduction

### What is a Trie?

A trie (pronounced "try") is a tree-like data structure used to store and retrieve strings. It's particularly efficient for prefix-based operations.

**Key Properties:**
- Each node represents a character
- Root represents empty string
- Path from root to node represents a prefix
- Nodes can be marked as end of word

**Advantages:**
- **Fast prefix search**: O(m) where m is string length
- **Memory efficient** for storing many strings with common prefixes
- **Autocomplete** and spell-checking

**Disadvantages:**
- **Space intensive** for sparse datasets
- **More complex** than hash tables for simple lookups

---

## Trie Structure

```
Example Trie with words: "cat", "car", "card", "dog"

         (root)
        /      \
       c        d
       |        |
       a        o
      / \       |
     t   r      g*
     *   |
         d
         *

* = end of word
```

---

## Implementation

### Basic Trie

**JavaScript:**
```javascript
class TrieNode {
    constructor() {
        this.children = new Map();  // char -> TrieNode
        this.isEndOfWord = false;
    }
}

class Trie {
    constructor() {
        this.root = new TrieNode();
    }

    // Insert word - O(m) where m is word length
    insert(word) {
        let node = this.root;

        for (const char of word) {
            if (!node.children.has(char)) {
                node.children.set(char, new TrieNode());
            }
            node = node.children.get(char);
        }

        node.isEndOfWord = true;
    }

    // Search for exact word - O(m)
    search(word) {
        let node = this.root;

        for (const char of word) {
            if (!node.children.has(char)) {
                return false;
            }
            node = node.children.get(char);
        }

        return node.isEndOfWord;
    }

    // Check if any word starts with prefix - O(m)
    startsWith(prefix) {
        let node = this.root;

        for (const char of prefix) {
            if (!node.children.has(char)) {
                return false;
            }
            node = node.children.get(char);
        }

        return true;
    }

    // Delete word - O(m)
    delete(word) {
        const deleteRecursive = (node, word, index) => {
            if (index === word.length) {
                if (!node.isEndOfWord) {
                    return false;  // Word not found
                }

                node.isEndOfWord = false;

                // Return true if node has no children (can be deleted)
                return node.children.size === 0;
            }

            const char = word[index];
            const childNode = node.children.get(char);

            if (!childNode) {
                return false;  // Word not found
            }

            const shouldDeleteChild = deleteRecursive(childNode, word, index + 1);

            if (shouldDeleteChild) {
                node.children.delete(char);
                // Return true if node has no children and is not end of word
                return node.children.size === 0 && !node.isEndOfWord;
            }

            return false;
        };

        return deleteRecursive(this.root, word, 0);
    }

    // Get all words with given prefix
    getWordsWithPrefix(prefix) {
        const words = [];
        let node = this.root;

        // Navigate to prefix node
        for (const char of prefix) {
            if (!node.children.has(char)) {
                return words;
            }
            node = node.children.get(char);
        }

        // DFS to collect all words
        const dfs = (node, currentWord) => {
            if (node.isEndOfWord) {
                words.push(currentWord);
            }

            for (const [char, childNode] of node.children) {
                dfs(childNode, currentWord + char);
            }
        };

        dfs(node, prefix);
        return words;
    }

    // Get all words in trie
    getAllWords() {
        return this.getWordsWithPrefix('');
    }
}

// Example usage
const trie = new Trie();
['cat', 'car', 'card', 'dog', 'dodge'].forEach(word => trie.insert(word));

console.log(trie.search('car'));          // true
console.log(trie.search('cars'));         // false
console.log(trie.startsWith('ca'));       // true
console.log(trie.getWordsWithPrefix('ca')); // ['cat', 'car', 'card']
```

**Python:**
```python
class TrieNode:
    def __init__(self):
        self.children = {}  # char -> TrieNode
        self.is_end_of_word = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    # Insert word - O(m) where m is word length
    def insert(self, word):
        node = self.root

        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]

        node.is_end_of_word = True

    # Search for exact word - O(m)
    def search(self, word):
        node = self.root

        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]

        return node.is_end_of_word

    # Check if any word starts with prefix - O(m)
    def starts_with(self, prefix):
        node = self.root

        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]

        return True

    # Delete word - O(m)
    def delete(self, word):
        def delete_recursive(node, word, index):
            if index == len(word):
                if not node.is_end_of_word:
                    return False  # Word not found

                node.is_end_of_word = False

                # Return True if node has no children (can be deleted)
                return len(node.children) == 0

            char = word[index]
            child_node = node.children.get(char)

            if not child_node:
                return False  # Word not found

            should_delete_child = delete_recursive(child_node, word, index + 1)

            if should_delete_child:
                del node.children[char]
                # Return True if node has no children and is not end of word
                return len(node.children) == 0 and not node.is_end_of_word

            return False

        return delete_recursive(self.root, word, 0)

    # Get all words with given prefix
    def get_words_with_prefix(self, prefix):
        words = []
        node = self.root

        # Navigate to prefix node
        for char in prefix:
            if char not in node.children:
                return words
            node = node.children[char]

        # DFS to collect all words
        def dfs(node, current_word):
            if node.is_end_of_word:
                words.append(current_word)

            for char, child_node in node.children.items():
                dfs(child_node, current_word + char)

        dfs(node, prefix)
        return words

    # Get all words in trie
    def get_all_words(self):
        return self.get_words_with_prefix('')

# Example usage
trie = Trie()
for word in ['cat', 'car', 'card', 'dog', 'dodge']:
    trie.insert(word)

print(trie.search('car'))          # True
print(trie.search('cars'))         # False
print(trie.starts_with('ca'))      # True
print(trie.get_words_with_prefix('ca'))  # ['cat', 'car', 'card']
```

---

## Operations

### 1. Autocomplete

**JavaScript:**
```javascript
class AutocompleteSystem {
    constructor(sentences, times) {
        this.trie = new Trie();
        this.currentInput = '';

        // Build trie with frequencies
        for (let i = 0; i < sentences.length; i++) {
            this.insertWithFrequency(sentences[i], times[i]);
        }
    }

    insertWithFrequency(sentence, frequency) {
        let node = this.trie.root;

        for (const char of sentence) {
            if (!node.children.has(char)) {
                node.children.set(char, new TrieNode());
            }
            node = node.children.get(char);
        }

        node.isEndOfWord = true;
        node.frequency = (node.frequency || 0) + frequency;
    }

    input(c) {
        if (c === '#') {
            // Save current input
            this.insertWithFrequency(this.currentInput, 1);
            this.currentInput = '';
            return [];
        }

        this.currentInput += c;

        // Get all matching sentences
        const matches = this.getMatchesWithFrequency(this.currentInput);

        // Sort by frequency (desc), then lexicographically
        matches.sort((a, b) => {
            if (b.frequency !== a.frequency) {
                return b.frequency - a.frequency;
            }
            return a.sentence.localeCompare(b.sentence);
        });

        return matches.slice(0, 3).map(m => m.sentence);
    }

    getMatchesWithFrequency(prefix) {
        const matches = [];
        let node = this.trie.root;

        // Navigate to prefix
        for (const char of prefix) {
            if (!node.children.has(char)) {
                return matches;
            }
            node = node.children.get(char);
        }

        // DFS to collect matches
        const dfs = (node, current) => {
            if (node.isEndOfWord) {
                matches.push({
                    sentence: current,
                    frequency: node.frequency
                });
            }

            for (const [char, childNode] of node.children) {
                dfs(childNode, current + char);
            }
        };

        dfs(node, prefix);
        return matches;
    }
}

// Example
const system = new AutocompleteSystem(
    ["i love you", "island", "ironman", "i love leetcode"],
    [5, 3, 2, 2]
);
console.log(system.input('i'));    // ["i love you", "island", "i love leetcode"]
console.log(system.input(' '));    // ["i love you", "i love leetcode"]
console.log(system.input('a'));    // []
console.log(system.input('#'));    // []
```

**Python:**
```python
class AutocompleteSystem:
    def __init__(self, sentences, times):
        self.trie = Trie()
        self.current_input = ''

        # Build trie with frequencies
        for sentence, frequency in zip(sentences, times):
            self.insert_with_frequency(sentence, frequency)

    def insert_with_frequency(self, sentence, frequency):
        node = self.trie.root

        for char in sentence:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]

        node.is_end_of_word = True
        if not hasattr(node, 'frequency'):
            node.frequency = 0
        node.frequency += frequency

    def input(self, c):
        if c == '#':
            # Save current input
            self.insert_with_frequency(self.current_input, 1)
            self.current_input = ''
            return []

        self.current_input += c

        # Get all matching sentences
        matches = self.get_matches_with_frequency(self.current_input)

        # Sort by frequency (desc), then lexicographically
        matches.sort(key=lambda x: (-x['frequency'], x['sentence']))

        return [m['sentence'] for m in matches[:3]]

    def get_matches_with_frequency(self, prefix):
        matches = []
        node = self.trie.root

        # Navigate to prefix
        for char in prefix:
            if char not in node.children:
                return matches
            node = node.children[char]

        # DFS to collect matches
        def dfs(node, current):
            if node.is_end_of_word:
                matches.append({
                    'sentence': current,
                    'frequency': node.frequency
                })

            for char, child_node in node.children.items():
                dfs(child_node, current + char)

        dfs(node, prefix)
        return matches

# Example
system = AutocompleteSystem(
    ["i love you", "island", "ironman", "i love leetcode"],
    [5, 3, 2, 2]
)
print(system.input('i'))    # ["i love you", "island", "i love leetcode"]
print(system.input(' '))    # ["i love you", "i love leetcode"]
print(system.input('a'))    # []
print(system.input('#'))    # []
```

### 2. Word Search II

**JavaScript:**
```javascript
function findWords(board, words) {
    const trie = new Trie();
    const result = new Set();

    // Build trie from words
    for (const word of words) {
        trie.insert(word);
    }

    const rows = board.length;
    const cols = board[0].length;
    const visited = Array(rows).fill(null).map(() => Array(cols).fill(false));

    const dfs = (row, col, node, path) => {
        if (row < 0 || row >= rows || col < 0 || col >= cols ||
            visited[row][col]) {
            return;
        }

        const char = board[row][col];
        if (!node.children.has(char)) {
            return;
        }

        const nextNode = node.children.get(char);
        const newPath = path + char;

        if (nextNode.isEndOfWord) {
            result.add(newPath);
        }

        visited[row][col] = true;

        // Explore all 4 directions
        dfs(row + 1, col, nextNode, newPath);
        dfs(row - 1, col, nextNode, newPath);
        dfs(row, col + 1, nextNode, newPath);
        dfs(row, col - 1, nextNode, newPath);

        visited[row][col] = false;
    };

    // Start DFS from each cell
    for (let i = 0; i < rows; i++) {
        for (let j = 0; j < cols; j++) {
            dfs(i, j, trie.root, '');
        }
    }

    return Array.from(result);
}

const board = [
    ['o','a','a','n'],
    ['e','t','a','e'],
    ['i','h','k','r'],
    ['i','f','l','v']
];
const words = ["oath","pea","eat","rain"];
console.log(findWords(board, words));  // ["oath", "eat"]

// Time: O(M * N * 4^L) where L is max word length
// Space: O(W * L) for trie, where W is number of words
```

**Python:**
```python
def find_words(board, words):
    trie = Trie()
    result = set()

    # Build trie from words
    for word in words:
        trie.insert(word)

    rows, cols = len(board), len(board[0])
    visited = [[False] * cols for _ in range(rows)]

    def dfs(row, col, node, path):
        if (row < 0 or row >= rows or col < 0 or col >= cols or
            visited[row][col]):
            return

        char = board[row][col]
        if char not in node.children:
            return

        next_node = node.children[char]
        new_path = path + char

        if next_node.is_end_of_word:
            result.add(new_path)

        visited[row][col] = True

        # Explore all 4 directions
        for dr, dc in [(1,0), (-1,0), (0,1), (0,-1)]:
            dfs(row + dr, col + dc, next_node, new_path)

        visited[row][col] = False

    # Start DFS from each cell
    for i in range(rows):
        for j in range(cols):
            dfs(i, j, trie.root, '')

    return list(result)

board = [
    ['o','a','a','n'],
    ['e','t','a','e'],
    ['i','h','k','r'],
    ['i','f','l','v']
]
words = ["oath","pea","eat","rain"]
print(find_words(board, words))  # ["oath", "eat"]

# Time: O(M * N * 4^L), Space: O(W * L)
```

---

## Applications

### 1. Spell Checker

**JavaScript:**
```javascript
class SpellChecker {
    constructor(dictionary) {
        this.trie = new Trie();
        dictionary.forEach(word => this.trie.insert(word.toLowerCase()));
    }

    isCorrect(word) {
        return this.trie.search(word.toLowerCase());
    }

    getSuggestions(word, maxDistance = 2) {
        const suggestions = [];
        word = word.toLowerCase();

        // Levenshtein distance implementation
        const levenshteinDistance = (s1, s2) => {
            const dp = Array(s1.length + 1).fill(null)
                .map(() => Array(s2.length + 1).fill(0));

            for (let i = 0; i <= s1.length; i++) dp[i][0] = i;
            for (let j = 0; j <= s2.length; j++) dp[0][j] = j;

            for (let i = 1; i <= s1.length; i++) {
                for (let j = 1; j <= s2.length; j++) {
                    if (s1[i-1] === s2[j-1]) {
                        dp[i][j] = dp[i-1][j-1];
                    } else {
                        dp[i][j] = 1 + Math.min(
                            dp[i-1][j],    // deletion
                            dp[i][j-1],    // insertion
                            dp[i-1][j-1]   // substitution
                        );
                    }
                }
            }

            return dp[s1.length][s2.length];
        };

        // Get all words and filter by distance
        const allWords = this.trie.getAllWords();
        for (const dictWord of allWords) {
            if (levenshteinDistance(word, dictWord) <= maxDistance) {
                suggestions.push(dictWord);
            }
        }

        return suggestions;
    }
}

const checker = new SpellChecker(['cat', 'car', 'card', 'care', 'careful']);
console.log(checker.isCorrect('car'));           // true
console.log(checker.isCorrect('carr'));          // false
console.log(checker.getSuggestions('carr'));     // ['car', 'card', 'care']
```

**Python:**
```python
class SpellChecker:
    def __init__(self, dictionary):
        self.trie = Trie()
        for word in dictionary:
            self.trie.insert(word.lower())

    def is_correct(self, word):
        return self.trie.search(word.lower())

    def get_suggestions(self, word, max_distance=2):
        suggestions = []
        word = word.lower()

        # Levenshtein distance implementation
        def levenshtein_distance(s1, s2):
            dp = [[0] * (len(s2) + 1) for _ in range(len(s1) + 1)]

            for i in range(len(s1) + 1):
                dp[i][0] = i
            for j in range(len(s2) + 1):
                dp[0][j] = j

            for i in range(1, len(s1) + 1):
                for j in range(1, len(s2) + 1):
                    if s1[i-1] == s2[j-1]:
                        dp[i][j] = dp[i-1][j-1]
                    else:
                        dp[i][j] = 1 + min(
                            dp[i-1][j],    # deletion
                            dp[i][j-1],    # insertion
                            dp[i-1][j-1]   # substitution
                        )

            return dp[len(s1)][len(s2)]

        # Get all words and filter by distance
        all_words = self.trie.get_all_words()
        for dict_word in all_words:
            if levenshtein_distance(word, dict_word) <= max_distance:
                suggestions.append(dict_word)

        return suggestions

checker = SpellChecker(['cat', 'car', 'card', 'care', 'careful'])
print(checker.is_correct('car'))           # True
print(checker.is_correct('carr'))          # False
print(checker.get_suggestions('carr'))     # ['car', 'card', 'care']
```

---

## Variations

### 1. Compressed Trie (Radix Tree)

Nodes with single child are merged to save space.

**JavaScript:**
```javascript
class RadixNode {
    constructor() {
        this.children = new Map();
        this.isEndOfWord = false;
        this.edge = '';  // Edge label (string)
    }
}

// Compact representation
// Instead of: c->a->t
// We have: "cat" as single edge
```

### 2. Ternary Search Tree

Space-efficient alternative to trie.

**JavaScript:**
```javascript
class TSTNode {
    constructor(char) {
        this.char = char;
        this.left = null;   // Less than
        this.mid = null;    // Equal (next char)
        this.right = null;  // Greater than
        this.isEndOfWord = false;
    }
}
```

---

## Practice Problems

### Easy
1. **Implement Trie** - Basic trie implementation
2. **Longest Common Prefix** - Find longest common prefix
3. **Prefix and Suffix Search** - Combined prefix/suffix search

### Medium
1. **Word Search II** - Find words from board
2. **Design Add and Search Words Data Structure** - With wildcards
3. **Replace Words** - Replace with shortest root
4. **Map Sum Pairs** - Sum values with prefix

### Hard
1. **Word Squares** - Find all word squares
2. **Palindrome Pairs** - Find palindrome pairs
3. **Stream of Characters** - Check if suffix matches

---

## Key Takeaways

1. **Tries excel at prefix operations** - O(m) time complexity
2. **Space trade-off**: Fast but memory-intensive
3. **Perfect for autocomplete**, spell-checking, IP routing
4. **DFS traversal** collects all words with prefix
5. **Deletion** requires careful handling of shared prefixes
6. **Variations** like radix trees save space

---

## Summary

Congratulations! You've completed the Data Structures section. You now understand:

- Arrays, Strings, and their manipulations
- Linked Lists (singly, doubly, circular)
- Stacks and Queues (LIFO and FIFO)
- Trees and Graphs (hierarchical and network structures)
- Hash Tables (constant-time lookups)
- Heaps (priority-based operations)
- Tries (prefix-based operations)

Next, you'll learn algorithms that operate on these data structures!

---

## Next Section

Continue to [Algorithms](../../02-Algorithms/) to learn problem-solving techniques.

[← Previous: Heaps](./06-Heaps.md) | [Next: Algorithms →](../../02-Algorithms/01-Sorting-Algorithms.md)
