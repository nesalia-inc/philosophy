# Tries (Prefix Trees)

A trie is a tree data structure for efficient prefix matching and autocomplete.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Autocomplete suggestions
* Prefix matching
* IP routing
* Spell checking
* Dictionary lookups

Complexities:
* Insert: O(m) where m = word length
* Search: O(m)
* Space: O(alphabet × m × n)
```

---

## Implementation

```typescript
class TrieNode {
  children: Map<string, TrieNode> = new Map();
  isEndOfWord: boolean = false;
}

class Trie {
  private root: TrieNode;

  constructor() {
    this.root = new TrieNode();
  }

  insert(word: string): void {
    let node = this.root;
    for (const char of word) {
      if (!node.children.has(char)) {
        node.children.set(char, new TrieNode());
      }
      node = node.children.get(char)!;
    }
    node.isEndOfWord = true;
  }

  search(word: string): boolean {
    const node = this.traverse(word);
    return node !== null && node.isEndOfWord;
  }

  startsWith(prefix: string): boolean {
    return this.traverse(prefix) !== null;
  }

  autocomplete(prefix: string): string[] {
    const results: string[] = [];
    const node = this.traverse(prefix);

    if (!node) return results;

    this.collectAllWords(node, prefix, results);
    return results;
  }

  private traverse(prefix: string): TrieNode | null {
    let node = this.root;
    for (const char of prefix) {
      if (!node.children.has(char)) {
        return null;
      }
      node = node.children.get(char)!;
    }
    return node;
  }

  private collectAllWords(node: TrieNode, prefix: string, results: string[]) {
    if (node.isEndOfWord) {
      results.push(prefix);
    }

    for (const [char, child] of node.children) {
      this.collectAllWords(child, prefix + char, results);
    }
  }
}
```

---

## Usage

```typescript
const trie = new Trie();

trie.insert('apple');
trie.insert('app');
trie.insert('application');
trie.insert('apply');
trie.insert('banana');

console.log(trie.search('apple'));      // true
console.log(trie.search('app'));         // true
console.log(trie.search('appl'));       // false

console.log(trie.startsWith('app'));    // true
console.log(trie.startsWith('ban'));     // true

console.log(trie.autocomplete('app'));
// ['app', 'apple', 'application', 'apply']
```

---

## Space Optimization

```typescript
// For large alphabets, use array instead of Map
class OptimizedTrieNode {
  children: TrieNode[] = new Array(26); // For lowercase letters
  isEndOfWord: boolean = false;
}
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
