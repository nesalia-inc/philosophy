# React Re-renders

Unnecessary React component re-renders occur when components update even when their props haven't changed.

---

## Severity

**MEDIUM** - Can cause performance issues, especially in large applications.

---

## Common Causes

### 1. Inline Functions

```tsx
// VULNERABLE - New function each render
function Parent() {
  return (
    <Child
      onClick={() => console.log('click')} // New function every render!
    />
  );
}
```

### 2. Props Changing on Every Render

```tsx
// VULNERABLE - Object recreated each render
function Parent() {
  const config = { // New object each render!
    theme: 'dark',
    fontSize: 14,
  };

  return <Child config={config} />;
}
```

### 3. Missing React.memo

```tsx
// VULNERABLE - Re-renders even when props unchanged
function HeavyComponent({ data }) {
  // Expensive computation every render!
  return <div>{data.map(d => <Item key={d.id} {...d} />)}</div>;
}
```

---

## Prevention

### 1. Use useCallback / useMemo

```tsx
// GOOD - Stable function reference
function Parent() {
  const handleClick = useCallback(() => {
    console.log('click');
  }, []); // Empty deps = same function

  return <Child onClick={handleClick} />;
}

// GOOD - Stable object reference
function Parent() {
  const config = useMemo(() => ({
    theme: 'dark',
    fontSize: 14,
  }), []); // Empty deps = same object

  return <Child config={config} />;
}
```

### 2. Use React.memo

```tsx
// GOOD - Only re-renders when props change
const HeavyComponent = React.memo(function HeavyComponent({ data }) {
  return <div>{data.map(d => <Item key={d.id} {...d} />)}</div>;
});

// With custom comparison
const HeavyComponent = React.memo(function HeavyComponent({ data }) {
  return <div>{data.map(d => <Item key={d.id} {...d} />)}</div>;
}, (prevProps, nextProps) => {
  return prevProps.data.length === nextProps.data.length;
});
```

### 3. Use Proper State Structure

```tsx
// GOOD - Separate unrelated state
function Form() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');

  return (
    <>
      <NameInput value={name} onChange={setName} />
      <EmailInput value={email} onChange={setEmail} />
    </>
  );
}
```

---

## Detection

### React DevTools

```bash
# In React DevTools Profiler
# Enable "Highlight updates when components render"
# Shows which components re-render unnecessarily
```

### why-did-you-render

```typescript
// Add to app
import whyDidYouRender from '@welldone-software/why-did-you-render';

whyDidYouRender(React, {
  trackAllPureComponents: true,
});
```

---

## Fix Example

```
BEFORE (Re-renders on every parent render):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

function Parent({ userId }) {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>
        Count: {count}
      </button>
      <Child userId={userId} />
    </div>
  );
}

function Child({ userId }) {
  console.log('Child rendered!'); // Logs every click!
  return <div>User: {userId}</div>;
}

AFTER (Optimized):
━━━━━━━━━━━━━━━━━━━

function Parent({ userId }) {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>
        Count: {count}
      </button>
      <Child key={userId} userId={userId} />
    </div>
  );
}

const Child = React.memo(function Child({ userId }) {
  console.log('Child rendered!');
  return <div>User: {userId}</div>;
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
