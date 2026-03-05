# Event Loop Blocking

Long-running synchronous operations block the JavaScript event loop, making the application unresponsive.

---

## Severity

**HIGH** - Causes UI freezing and poor user experience.

---

## Common Causes

### 1. Heavy Computation on Main Thread

```typescript
// VULNERABLE - Blocks event loop
function processLargeData(data) {
  let result = 0;
  for (let i = 0; i < data.length; i++) {
    result += heavyComputation(data[i]);
  }
  return result;
}

// UI freezes while this runs!
```

### 2. Synchronous File Operations

```typescript
// VULNERABLE - Blocks event loop
const fs = require('fs');

const content = fs.readFileSync('large-file.txt', 'utf8');
// File system blocked!
```

### 3. JSON Parsing Large Data

```typescript
// VULNERABLE - Blocks on large JSON
const data = JSON.parse(largeJsonString);
// UI freezes during parse!
```

---

## Prevention

### 1. Use Web Workers

```typescript
// worker.ts
self.onmessage = (e) => {
  const result = heavyComputation(e.data);
  self.postMessage(result);
};

// Main thread
const worker = new Worker(new URL('./worker.ts', import.meta.url));
worker.postMessage(largeData);
worker.onmessage = (e) => console.log(e.data);
```

### 2. Chunk Processing

```typescript
// GOOD - Process in chunks
async function processLargeData(data) {
  const CHUNK_SIZE = 1000;
  let result = 0;

  for (let i = 0; i < data.length; i += CHUNK_SIZE) {
    const chunk = data.slice(i, i + CHUNK_SIZE);

    // Process chunk
    for (const item of chunk) {
      result += compute(item);
    }

    // Yield to event loop
    await new Promise(resolve => setTimeout(resolve, 0));
  }

  return result;
}
```

### 3. Use Streams

```typescript
// GOOD - Stream processing
import fs from 'fs/promises';

const readable = fs.createReadStream('large-file.txt');
const writable = fs.createWriteStream('output.txt');

readable.pipe(writable);
// Doesn't block event loop!
```

### 4. Use Async/Await

```typescript
// GOOD - Async file operations
import { readFile } from 'fs/promises';

const content = await readFile('large-file.txt', 'utf8');
// Non-blocking!
```

---

## Detection

### Chrome DevTools

```
1. Open DevTools > Performance tab
2. Record interaction
3. Look for "Main" thread blocking
4. Long yellow bars = blocked event loop
```

---

## Fix Example

```
BEFORE (UI Freezes):
━━━━━━━━━━━━━━━━━━━━

function processAll() {
  const data = fetchData();
  const result = heavyComputation(data);
  updateUI(result);
}

AFTER (Responsive):
━━━━━━━━━━━━━━━━━━━

async function processAll() {
  const data = fetchData();

  const result = await new Promise((resolve) => {
    const worker = new Worker(new URL('./worker.js', import.meta.url));
    worker.onmessage = (e) => resolve(e.data);
    worker.postMessage(data);
  });

  updateUI(result);
}
```

---

## Targets

```
FID (First Input Delay) TARGETS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Good: < 100ms
Needs Improvement: 100-300ms
Poor: > 300ms
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
