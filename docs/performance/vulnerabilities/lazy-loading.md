# Lazy Loading Issues

Incorrect code splitting and lazy loading can lead to larger initial bundles or unnecessary network requests.

---

## Severity

**MEDIUM** - Affects Time to Interactive and bundle size.

---

## Common Issues

### 1. Not Lazy Loading Large Components

```typescript
// VULNERABLE - All code in initial bundle
import { HeavyChart } from './charts/HeavyChart';
import { DataTable } from './tables/DataTable';
import { Editor } from './editor/Editor';

function App() {
  return (
    <div>
      <HeavyChart />
      <DataTable />
    </div>
  );
}
```

### 2. Eager Loading Dynamic Imports

```typescript
// VULNERABLE - Imports immediately anyway
const HeavyComponent = await import('./HeavyComponent');
// Still loads immediately!
```

### 3. No Route-Based Splitting

```typescript
// VULNERABLE - All routes in one bundle
import { Route } from 'react-router';

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/dashboard" element={<Dashboard />} />
      <Route path="/admin" element={<Admin />} />
    </Routes>
  );
}
```

---

## Prevention

### 1. React.lazy

```typescript
// GOOD - Lazy load
const HeavyChart = React.lazy(() => import('./charts/HeavyChart'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <HeavyChart />
    </Suspense>
  );
}
```

### 2. Route-Based Splitting

```typescript
// GOOD - Split by route
import { lazy, Suspense } from 'react';
import { Routes, Route } from 'react-router-dom';

const Home = lazy(() => import('./pages/Home'));
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Admin = lazy(() => import('./pages/Admin'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/admin" element={<Admin />} />
      </Routes>
    </Suspense>
  );
}
```

### 3. Prefetch Important Routes

```tsx
// Next.js automatic prefetching
import Link from 'next/link';

function Nav() {
  return (
    <nav>
      <Link href="/dashboard" prefetch={true}>Dashboard</Link>
    </nav>
  );
}
```

---

## Fix Example

```
BEFORE (1MB bundle):
━━━━━━━━━━━━━━━━━━━━

// app.ts
import Home from './pages/Home';
import Dashboard from './pages/Dashboard';
import Settings from './pages/Settings';
import Profile from './pages/Profile';
import Admin from './pages/Admin';

// All in one bundle!

AFTER (100KB initial):
━━━━━━━━━━━━━━━━━━━━━━

// app.ts
const Home = lazy(() => import('./pages/Home'));
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));
const Profile = lazy(() => import('./pages/Profile'));
const Admin = lazy(() => import('./pages/Admin'));

// Split into separate chunks:
// - home.js (50KB)
// - dashboard.js (80KB)
// - settings.js (30KB)
// - profile.js (40KB)
// - admin.js (100KB)
```

---

## Targets

```
BUNDLE SIZE TARGETS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Initial bundle: < 170KB (compressed)
Time to Interactive: < 3.8 seconds
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
