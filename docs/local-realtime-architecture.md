# Local Realtime Architecture

Deterministic local caching with server-dictated cache keys.

---

## The Problem: Client-Side Cache Chaos

```
CURRENT APPROACHES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. CLIENT-DRIVEN CACHING (TanStack Query default):
   * Client decides what to cache
   * Client decides when to invalidate
   * Client guesses dependencies
   * Problem: Cache invalidation is HARD
   * Problem: Easy to have stale data

2. SERVER-SENT EVENTS / WEBSOCKETS:
   * Server pushes updates
   * Complex infrastructure
   * Bidirectional
   * Problem: Overhead for simple needs

3. POLLING:
   * Simple but inefficient
   * Wastes resources
   * Not truly realtime
```

---

## The Solution: Server-Dictated Keys

```
CORE PRINCIPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   THE SERVER KNOWS THE DATA GRAPH                           │
│   LET THE SERVER DICTATE WHAT TO CACHE                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘

QUERY:
  Client asks for data
  Server returns: data + cacheKeys to store

MUTATION:
  Client modifies data
  Server returns: result + revalidateKeys to refresh

That's it. No guessing. No complexity. Deterministic.
```

---

## How It Works

### Query Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                         QUERY FLOW                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   CLIENT                                                         │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │  const query = api.query({                              │   │
│   │    key: ['posts'],                                     │   │
│   │    fetch: () => server.getPosts()                      │   │
│   │  })                                                     │   │
│   └─────────────────────────────────────────────────────────┘   │
│        │                                                       │
│        │ "GET /posts"                                         │
│        ▼                                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │                        SERVER                            │   │
│   │                                                          │   │
│   │  Query: SELECT * FROM posts                             │   │
│   │                                                          │   │
│   │  Response: {                                             │   │
│   │    data: [...],  // posts                               │   │
│   │    cacheKeys: [                                          │   │
│   │      'posts',              // this query                │   │
│   │      'posts:user:123',     // posts by user 123        │   │
│   │      'feed',               // main feed                 │   │
│   │      'stats'               // global stats              │   │
│   │    ]                                                   │   │
│   │  }                                                      │   │
│   │                                                          │   │
│   └─────────────────────────────────────────────────────────┘   │
│        │                                                       │
│        │ Response + cacheKeys                                 │
│        ▼                                                       │
│   CLIENT                                                         │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │  Cache.set('posts', data)                              │   │
│   │  Cache.set('posts:user:123', data)                     │   │
│   │  Cache.set('feed', data)                               │   │
│   │  Cache.set('stats', data)                              │   │
│   │                                                          │   │
│   │  All keys stored together                              │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Mutation Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                       MUTATION FLOW                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   CLIENT                                                         │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │  const mutation = api.mutate({                         │   │
│   │    mutation: 'createPost',                              │   │
│   │    args: { title: 'Hello' }                            │   │
│   │  })                                                     │   │
│   └─────────────────────────────────────────────────────────┘   │
│        │                                                       │
│        │ "POST /posts"                                        │
│        ▼                                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │                        SERVER                            │   │
│   │                                                          │   │
│   │  Mutation: INSERT INTO posts ...                         │   │
│   │  Hooks: afterCreate → notify followers                 │   │
│   │                                                          │   │
│   │  Response: {                                             │   │
│   │    result: { id: 1, title: 'Hello' },                 │   │
│   │    revalidateKeys: [                                    │   │
│   │      'posts',              // list refreshed            │   │
│   │      'posts:user:123',     // user's posts              │   │
│   │      'feed',               // main feed                  │   │
│   │      'stats',              // global stats              │   │
│   │      'notifications:123'   // user notifications       │   │
│   │    ]                                                   │   │
│   │  }                                                      │   │
│   │                                                          │   │
│   └─────────────────────────────────────────────────────────┘   │
│        │                                                       │
│        │ Response + revalidateKeys                            │
│        ▼                                                       │
│   CLIENT                                                         │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │  for (key of revalidateKeys) {                         │   │
│   │    Cache.invalidate(key)                               │   │
│   │    QueryClient.refetch({ queryKey: [key] })           │   │
│   │  }                                                      │   │
│   │                                                          │   │
│   │  Automatic refetch of all affected queries            │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Why It's Deterministic

```
DETERMINISTIC = NO GUESSING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CLIENT CAN'T KNOW:
* What other queries depend on this data
* What should be invalidated when data changes
* What the data graph looks like

SERVER KNOWS:
* All relationships between queries
* What mutations affect what data
* The complete data graph

EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

When a user creates a post, what should refresh?

Client doesn't know:
* Maybe the user's post list?
* Maybe the global feed?
* Maybe notifications?
* Maybe stats?

Server knows:
* The server DEFINED the cacheKeys for each query
* The server returns EXACTLY what needs revalidation
* No guessing, just follow the keys
```

---

## The API Local Integration

### Query Definition

```typescript
// Server: Define query with cacheKeys
const getPosts = t.query({
  args: z.object({
    limit: z.number().default(10),
  }),

  handler: async (ctx, args) => {
    const posts = await ctx.db.posts.findMany({
      take: args.limit,
      orderBy: { createdAt: 'desc' },
    });

    // Server calculates cacheKeys
    const cacheKeys = [
      'posts',                      // All posts
      `posts:limit:${args.limit}`,  // Posts with this limit
      'feed',                       // Main feed
      'stats',                      // Global stats
    ];

    return {
      data: posts,
      cacheKeys,
    };
  },
});
```

### Mutation Definition

```typescript
// Server: Define mutation with revalidateKeys
const createPost = t.mutation({
  args: z.object({
    title: z.string().min(1),
    content: z.string(),
  }),

  handler: async (ctx, args) => {
    // Validate
    const data = await validatePost(args);
    if (data.isErr()) {
      return error('VALIDATION_FAILED', data.error);
    }

    // Create
    const post = await ctx.db.posts.create({
      data: data.value,
    });

    // Server calculates revalidateKeys
    const revalidateKeys = [
      'posts',                      // Post list
      `posts:user:${ctx.userId}`,   // User's posts
      'feed',                       // Main feed
      'stats',                      // Global stats
      `notifications:${ctx.userId}`,// User notifications
    ];

    return {
      result: post,
      revalidateKeys,
    };
  },
});
```

### Client Implementation

```typescript
// Client: Wrapper around TanStack Query
class LocalRealtimeClient {
  private queryClient: QueryClient;

  async query<T>(key: string[], fetchFn: () => Promise<QueryResponse<T>>) {
    const response = await fetchFn();

    // Store all cacheKeys
    for (const cacheKey of response.cacheKeys) {
      this.queryClient.setQueryData(cacheKey, response.data);
    }

    return response.data;
  }

  async mutate<T>(mutationFn: () => Promise<MutationResponse<T>>) {
    const response = await mutationFn();

    // Revalidate all revalidateKeys
    for (const key of response.revalidateKeys) {
      await this.queryClient.invalidateQueries({ queryKey: [key] });
    }

    return response.result;
  }
}
```

---

## Cache Strategies

### Strategy 1: Global Cache (Server/Redis)

```
┌─────────────────────────────────────────────────────────────────┐
│                     GLOBAL CACHE (REDIS)                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Use for:                                                     │
│   * Shared data across users                                   │
│   * Expensive computations                                      │
│   * Cross-session data                                         │
│                                                                 │
│   Flow:                                                        │
│   ┌──────────┐    ┌──────────┐    ┌──────────┐              │
│   │ Client   │───►│  Server  │───►│  Redis   │              │
│   │ Query    │    │ Process  │    │  Cache   │              │
│   └──────────┘    └──────────┘    └──────────┘              │
│        │               │                                        │
│        ▼               ▼                                        │
│   Response +      Set TTL +                                    │
│   cacheKeys      return keys                                   │
│                                                                 │
│   Example:                                                     │
│   * User profiles (same for all)                              │
│   * Global config                                              │
│   * Public content                                             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Strategy 2: Local Cache (Client)

```
┌─────────────────────────────────────────────────────────────────┐
│                     LOCAL CACHE (CLIENT)                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Use for:                                                     │
│   * User-specific data                                         │
│   * Session data                                               │
│   * Real-time updates                                          │
│                                                                 │
│   Flow:                                                        │
│   ┌──────────┐    ┌──────────┐                               │
│   │ Client   │───►│  Server  │                               │
│   │ Query    │    │ Process  │                               │
│   └──────────┘    └──────────┘                               │
│        │               │                                        │
│        ▼               ▼                                        │
│   Client-local     Return keys                                │
│   TanStack Query  (no Redis)                                 │
│                                                                 │
│   Example:                                                     │
│   * User's draft posts                                         │
│   * UI state                                                   │
│   * Optimistic updates                                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Strategy 3: Hybrid (Both)

```
┌─────────────────────────────────────────────────────────────────┐
│                      HYBRID CACHE                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Server decides which strategy for each key:                  │
│                                                                 │
│   {                                                            │
│     data: posts,                                              │
│     cache: {                                                  │
│       'posts': 'global',    // Redis                           │
│       'drafts': 'local',    // Client only                    │
│       'feed': 'both'       // Redis + local                   │
│     }                                                          │
│   }                                                            │
│                                                                 │
│   Benefits:                                                   │
│   * Best of both worlds                                        │
│   * Server optimizes per-use-case                             │
│   * Deterministic                                              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Real-Time Patterns

### Pattern 1: Polling (Simple)

```
POLLING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Use: Low-frequency updates
Cost: Low
Latency: Seconds

Implementation:
* Set staleTime: 5000 (5 seconds)
* Background refetch every 5s
* Works with server-dictated keys
```

### Pattern 2: On-Focus Revalidation

```
ON-FOCUS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Use: When user returns to app
Cost: Very low
Latency: Immediate on focus

Implementation:
* QueryClient: focusManager.setEventListener
* On window focus → refetch revalidateKeys
* Works with server-dictated keys
```

### Pattern 3: Manual Refresh

```
MANUAL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Use: User-initiated refresh
Cost: On-demand
Latency: User-controlled

Implementation:
* Provide refresh button
* On click → refetch revalidateKeys
* Works with server-dictated keys
```

### Pattern 4: Event-Based (Advanced)

```
EVENT-BASED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Use: High-frequency updates
Cost: Medium
Latency: Near real-time

Implementation:
* Server emits events on mutations
* Server-Sent Events (SSE) stream
* Client listens and triggers revalidation
* Works with server-dictated keys

Example:
* User is typing in collaborative editor
* Each keystroke → server event
* Client receives → revalidates only affected keys
```

---

## Benefits

```
BENEFITS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────┐  ┌─────────────────────┐
│   DETERMINISTIC     │  │   SIMPLE            │
├─────────────────────┤  ├─────────────────────┤
│ • Server controls  │  │ • No guessing      │
│   the graph        │  │ • No complex       │
│ • No race          │  │   invalidation     │
│   conditions       │  │   logic           │
│ • Predictable      │  │ • Just follow     │
│                    │  │   the keys        │
└─────────────────────┘  └─────────────────────┘

┌─────────────────────┐  ┌─────────────────────┐
│   OPTIMIZED        │  │   REAL-TIME        │
├─────────────────────┤  ├─────────────────────┤
│ • Server knows     │  │ • Instant         │
│   what's related  │  │   updates         │
│ • Can batch        │  │ • No WebSocket   │
│   invalidation     │  │   overhead       │
│ • Can prioritize   │  │ • Works with     │
│                    │  │   any transport  │
└─────────────────────┘  └─────────────────────┘
```

---

## Comparison

```
COMPARISON TABLE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────┬──────────────┬──────────────┬──────────────┐
│                     │  TanStack    │  WebSocket   │  Our System  │
│                     │   Query      │    (SSE)     │              │
├─────────────────────┼──────────────┼──────────────┼──────────────┤
│ Complexity          │    Low       │    High      │    Low       │
│ Infrastructure      │    None      │    Server    │    None      │
│ Real-time           │   Polling    │   Push       │   Deterministic│
│ Server control      │    No        │    Yes       │    Yes       │
│ Stale data risk     │    High      │    Low       │    Low       │
│ Offline support     │    Yes       │     No       │    Yes       │
│ Implementation      │   Native     │  Custom      │  Wrapper     │
│                     │   (built-in) │  needed      │  around TQ   │
└─────────────────────┴──────────────┴──────────────┴──────────────┘
```

---

## Implementation

```
IMPLEMENTATION STEPS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Wrap TanStack Query
   * Extend QueryClient
   * Add cacheKeys handling
   * Add revalidateKeys handling

2. Server: Add cacheKeys to all queries
   * Calculate related keys
   * Return in response

3. Server: Add revalidateKeys to all mutations
   * Calculate affected keys
   * Return in response

4. Client: Connect the dots
   * Parse response
   * Update cache
   * Trigger refetch

5. Optional: Add SSE for true real-time
   * Stream of events
   * Each event → revalidateKeys
```

---

## Related Documentation

- [API Local Architecture](./api-local-architecture.md) - High-level ORM
- [Three Pillars](./three-pillars.md) - Result, Option, Unit
- [Meta-Framework Philosophy](./meta-framework-philosophy.md) - Overall vision
- [DeesseJS Functions](./deessejs-functions.md) - Local procedures

---

*Document version: 1.0*
*Last updated: 2026-03-04*
