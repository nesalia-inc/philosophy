# Memory Leaks

Memory leaks occur when applications allocate memory but fail to release it, leading to gradually increasing memory usage.

---

## Severity

**HIGH** - Can cause application crashes, slowdowns, and require restarts.

---

## Common Causes

### 1. Unclosed Event Listeners

```typescript
// VULNERABLE
function subscribeToEvents() {
  emitter.on('event', handler); // Never removed
}

// Every call adds a new listener
subscribeToEvents();
subscribeToEvents();
subscribeToEvents(); // 3 listeners!
```

### 2. Global Variables

```typescript
// VULNERABLE - Accidental global
function processData(data) {
  global.cache = data; // Never cleared!
  return transform(data);
}
```

### 3. Closures Holding References

```typescript
// VULNERABLE
const cache = new Map();

function getData(id) {
  if (cache.has(id)) return cache.get(id);

  const data = fetchData(id);

  // Closure holds reference to all data ever fetched
  cache.set(id, data);

  return data;
}

// Map grows infinitely!
```

### 4. Timers Not Cleared

```typescript
// VULNERABLE
function startPolling() {
  setInterval(() => {
    fetchData(); // Never cleared!
  }, 1000);
}
```

### 5. Detached DOM Elements

```typescript
// VULNERABLE
const elements = [];

function createElement() {
  const div = document.createElement('div');
  const handler = () => console.log('click');

  div.addEventListener('click', handler);

  elements.push({ div, handler }); // Elements kept in memory
}
```

---

## Prevention

### 1. Clean Up Event Listeners

```typescript
// GOOD
function subscribeToEvents() {
  const handler = () => console.log('event');
  emitter.on('event', handler);

  // Return cleanup function
  return () => emitter.off('event', handler);
}

// Use and cleanup
const unsubscribe = subscribeToEvents();
unsubscribe(); // Clean up when done
```

### 2. Use WeakRef

```typescript
// GOOD - Use WeakRef for cache
const cache = new WeakMap<object, Data>();

function getData(obj: object): Data | undefined {
  if (cache.has(obj)) {
    return cache.get(obj)!;
  }

  const data = fetchData(obj);
  cache.set(obj, data);
  return data;
}
```

### 3. Clear Timers

```typescript
// GOOD
let intervalId: NodeJS.Timeout | null = null;

function startPolling() {
  if (intervalId) return;

  intervalId = setInterval(fetchData, 1000);
}

function stopPolling() {
  if (intervalId) {
    clearInterval(intervalId);
    intervalId = null;
  }
}
```

### 4. Bounded Cache

```typescript
// GOOD - LRU cache with max size
import { LRUCache } from 'lru-cache';

const cache = new LRUCache<string, Data>({
  max: 1000,
  ttl: 1000 * 60 * 10, // 10 minutes
});

function getData(id: string): Data {
  return cache.getOrSet(id, () => fetchData(id));
}
```

---

## Detection

### Node.js

```bash
# Use --inspect flag
node --inspect app.js

# Chrome DevTools
# Memory tab > Take heap snapshot
# Compare snapshots to find leaks
```

### React

```typescript
// Use React DevTools
# Profiler > Highlight updates
# Shows unnecessary re-renders
```

---

## Fix Example

```
BEFORE (Leaking):
━━━━━━━━━━━━━━━━

function useData(id) {
  const [data, setData] = useState(null);

  useEffect(() => {
    const subscription = api.subscribe(id, (newData) => {
      setData(newData);
    });
    // Missing cleanup!
  }, [id]);

  return data;
}

AFTER (Fixed):
━━━━━━━━━━━━━━━━

function useData(id) {
  const [data, setData] = useState(null);

  useEffect(() => {
    const subscription = api.subscribe(id, (newData) => {
      setData(newData);
    });

    // Clean up on unmount or id change
    return () => subscription.unsubscribe();
  }, [id]);

  return data;
}
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
