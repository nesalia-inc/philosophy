# N+1 Queries

N+1 queries occur when an application makes one initial query followed by N additional queries to fetch related data.

---

## Severity

**HIGH** - Can slow down applications with large datasets.

 significantly, especially---

## How It Works

```
PROBLEM:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Code:
───────
const users = await db.users.findMany();

for (const user of users) {
  const posts = await db.posts.findMany({
    where: { userId: user.id }
  });
  user.posts = posts;
}

Queries:
───────
1. SELECT * FROM users
2. SELECT * FROM posts WHERE userId = 1
3. SELECT * FROM posts WHERE userId = 2
4. SELECT * FROM posts WHERE userId = 3
...
N+1 queries!
```

---

## Examples

### TypeORM / Drizzle

```typescript
// VULNERABLE - N+1
const users = await db.user.findMany();

for (const user of users) {
  const posts = await db.post.findMany({
    where: { authorId: user.id }
  });
}

// 1 + N queries
```

### Sequelize

```typescript
// VULNERABLE - N+1
const users = await User.findAll();

for (const user of users) {
  const orders = await Order.findAll({
    where: { userId: user.id }
  });
}
```

---

## Prevention

### 1. Eager Loading

```typescript
// GOOD - Single query with JOIN
const users = await db.user.findMany({
  include: {
    posts: true,  // Automatically fetches posts
  },
});

// Single query: SELECT * FROM users JOIN posts ...
```

### 2. Batch Loading

```typescript
// GOOD - Batch fetch all related records
const users = await db.user.findMany();
const userIds = users.map(u => u.id);

const posts = await db.post.findMany({
  where: {
    authorId: { in: userIds }
  }
});

// Group posts by user
const postsByUser = posts.reduce((acc, post) => {
  (acc[post.authorId] ||= []).push(post);
  return acc;
}, {});

// Attach to users
for (const user of users) {
  user.posts = postsByUser[user.id] || [];
}

// 2 queries total
```

### 3. Drizzle Specific

```typescript
// GOOD - Using Drizzle relations
import { users, posts } from './schema';

const userList = await db.select({
  user: users,
  posts: posts,
})
.from(users)
.leftJoin(posts, eq(users.id, posts.authorId));

// Or with relations
const userList = await db.query.users.findMany({
  with: {
    posts: true,
  },
});
```

---

## Detection

### AST Patterns

```typescript
// Pattern: Loop with database query inside
const n1Patterns = {
  check: (node) => {
    // Find for/foreach/while loops
    if (isLoop(node)) {
      // Check if there's a DB query inside
      for (const child of node.body) {
        if (isDatabaseQuery(child)) {
          return { vulnerable: true, location: node.loc };
        }
      }
    }
  },
};
```

### Tools

```prisma
// Prisma Studio or prisma-fmt
// Shows N+1 in query console
```

---

## Fix Example

```
BEFORE (N+1):
━━━━━━━━━━━━━━━━

const users = await db.user.findMany();

for (const user of users) {
  const posts = await db.post.findMany({
    where: { authorId: user.id }
  });
  user.posts = posts;
}

AFTER (Fixed):
━━━━━━━━━━━━━━━━

const users = await db.user.findMany({
  include: { posts: true }
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
