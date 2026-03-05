# Bundle Size

Large JavaScript bundles slow down initial page load and harm user experience.

---

## Severity

**HIGH** - Directly impacts LCP and Time to Interactive.

---

## Common Causes

### 1. Large Dependencies

```typescript
// VULNERABLE - Importing entire library
import _ from 'lodash';

const x = _.cloneDeep(obj);
```

### 2. Multiple Similar Libraries

```typescript
// VULNERABLE - Multiple utility libraries
import moment from 'moment';
import lodash from 'lodash';
import axios from 'axios';
```

### 3. Not Using Tree Shaking

```typescript
// VULNERABLE - Side effects
// package.json
{
  "sideEffects": false
}

// BUT code has side effects
export function process() {
  console.log('processing'); // Side effect!
}
```

---

## Prevention

### 1. Use Named Imports

```typescript
// GOOD - Tree shakeable
import { cloneDeep } from 'lodash';
import { format } from 'date-fns';
```

### 2. Use Smaller Alternatives

```
ALTERNATIVES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

lodash → lodash-es (tree shakeable)
moment → date-fns or dayjs
axios → fetch or ky
prismjs → Custom minimal highlighter
```

### 3. Code Splitting

```typescript
// GOOD - Dynamic imports
const HeavyComponent = React.lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <HeavyComponent />
    </Suspense>
  );
}
```

### 4. Analyze Bundle

```bash
# Webpack bundle analyzer
npx webpack-bundle-analyzer

# Next.js
# next build shows bundle sizes
```

---

## Fix Example

```
BEFORE (500KB bundle):
━━━━━━━━━━━━━━━━━━━━━━━

// utils.ts
import moment from 'moment';
import _ from 'lodash';
import axios from 'axios';

export const formatDate = (date) => moment(date).format();
export const clone = _.cloneDeep;
export const fetchData = (url) => axios.get(url);

AFTER (50KB bundle):
━━━━━━━━━━━━━━━━━━━━

// utils.ts
import { format } from 'date-fns';
import { cloneDeep } from 'lodash-es';
import fetch from 'ky';

export const formatDate = (date) => format(new Date(date), 'yyyy-MM-dd');
export const clone = cloneDeep;
export const fetchData = (url) => fetch(url).json();
```

---

## Target Sizes

```
BUNDLE TARGETS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

JavaScript:
* Initial load: < 170KB (compressed)
* Total: < 500KB (compressed)

LCP:
* < 2.5 seconds

Time to Interactive:
* < 3.8 seconds
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
