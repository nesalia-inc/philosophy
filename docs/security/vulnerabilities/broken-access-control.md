# Broken Access Control

Broken Access Control occurs when users can act outside their intended permissions, accessing unauthorized resources or functionality.

---

## Severity

**CRITICAL** - OWASP #1 vulnerability. Can lead to unauthorized access to sensitive data and administrative functions.

---

## Types of Access Control Issues

### 1. Vertical Privilege Escalation

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Normal user accesses admin functions:

GET /api/user/profile      → 200 OK (regular user)
GET /api/admin/users       → 200 OK (should be 403!)

User can access:
- Admin panels
- User management
- System configuration
```

### 2. Horizontal Privilege Escalation

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

User accesses another user's data:

GET /api/user/123/profile  → 200 OK (my profile)
GET /api/user/456/profile  → 200 OK (another user's profile!)

User can view other users' data
```

### 3. Insecure Direct Object Reference (IDOR)

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

URL-based IDOR:
/api/invoices/1234
/api/documents/secret.pdf

Parameter-based IDOR:
/api/delete?id=123
/api/transfer?to=attacker&amount=10000
```

### 4. Missing Function Level Access Control

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Admin endpoint not protected:

POST /api/admin/create-user
GET /api/debug/config

These should return 403 for non-admin users
```

---

## Detection

### AST Patterns to Detect

```typescript
const accessControlPatterns = {
  issues: [
    // No authorization check
    {
      pattern: 'router.post',
      check: (node) => !hasAuthMiddleware(node),
      issue: 'Route without authentication',
    },
    // Role check but not enforced
    {
      pattern: 'if (user.role',
      check: (node) => !hasEnforcement(node),
      issue: 'Role check but not enforced',
    },
    // Direct database access without check
    {
      pattern: 'db.query',
      check: (node) => !hasOwnershipCheck(node),
      issue: 'No ownership verification',
    },
    // Object reference without validation
    {
      pattern: 'req.params.id',
      check: (node) => !hasAccessCheck(node),
      issue: 'IDOR vulnerability',
    },
  ],
};
```

### Common Vulnerable Patterns

```typescript
// VULNERABLE - No access control
app.get('/api/user/:id', (req, res) => {
  const user = db.getUser(req.params.id);
  res.json(user);
});

// VULNERABLE - Only client-side check
const deleteDocument = (id) => {
  if (isAdmin()) { // Client-side only!
    api.delete(`/documents/${id}`);
  }
};

// VULNERABLE - Insufficient check
app.get('/api/user/:id', (req, res) => {
  const user = db.getUser(req.params.id);
  if (req.user) { // Just checking if logged in!
    res.json(user);
  }
});
```

---

## Prevention

### 1. Centralized Authorization

```typescript
// GOOD - Centralized middleware
const authorize = (requiredRole: Role) => {
  return (req: Request, res: Response, next: NextFunction) => {
    if (!req.user) {
      return res.status(401).json({ error: 'Unauthorized' });
    }

    if (requiredRole && req.user.role !== requiredRole) {
      return res.status(403).json({ error: 'Forbidden' });
    }

    next();
  };
};

// GOOD - Apply to routes
app.get('/api/admin/users', authorize('admin'), adminController.list);
app.get('/api/user/:id', authorize(), userController.get);
```

### 2. Verify Object Ownership

```typescript
// GOOD - Verify ownership
app.get('/api/document/:id', async (req, res) => {
  const doc = await db.document.findUnique({
    where: { id: req.params.id },
  });

  // Verify ownership
  if (doc.ownerId !== req.user.id && req.user.role !== 'admin') {
    return res.status(403).json({ error: 'Forbidden' });
  }

  res.json(doc);
});
```

### 3. Deny by Default

```typescript
// GOOD - Default deny
const permissions = {
  admin: ['*'],
  user: ['read:own', 'write:own'],
  guest: ['read:public'],
};

function checkPermission(action: string, user: User): boolean {
  const userPermissions = permissions[user.role] || [];
  return userPermissions.includes('*') ||
         userPermissions.includes(action);
}
```

### 4. Use Secure Patterns

```typescript
// GOOD - Policy-based access
import { AbilityBuilder, createMongoAbility } from '@casl/ability';

const { can, cannot, build } = new AbilityBuilder(createMongoAbility);

if (user.role === 'admin') {
  can('manage', 'all');
} else {
  can('read', 'Post');
  can('create', 'Post');
  cannot('delete', 'Post');
}

// Check in route
app.delete('/api/post/:id', (req, res) => {
  if (ability.cannot('delete', 'Post', { authorId: req.user.id })) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  // Delete post
});
```

---

## Testing

### Manual Testing

```
ACCESS CONTROL TEST CASES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Vertical Escalation
   * Login as regular user
   * Try to access /api/admin/*
   * Should get 403

2. Horizontal Escalation
   * Login as user A
   * Try to access user B's data
   * Should get 403

3. IDOR
   * Change ID parameter in URLs
   * Try to access other resources
   * Should get 403

4. Missing Function Access
   * Find admin endpoints
   * Try without admin privileges
   * Should get 403
```

---

## Fix Example

```
BEFORE (Vulnerable):
━━━━━━━━━━━━━━━━━━━━

app.get('/api/document/:id', (req, res) => {
  const doc = db.getDocument(req.params.id);
  res.json(doc);
});

AFTER (Fixed):
━━━━━━━━━━━━━━━━

import { authorize, checkOwnership } from './auth';

app.get('/api/document/:id',
  authorize(),
  checkOwnership('document'),
  async (req, res) => {
    const doc = await db.getDocument(req.params.id);
    res.json(doc);
  }
);

// Middleware
function checkOwnership(resource: string) {
  return async (req: Request, res: Response, next: NextFunction) => {
    const resource = await db[resource].findUnique({
      where: { id: req.params.id },
    });

    if (resource.ownerId !== req.user.id && req.user.role !== 'admin') {
      return res.status(403).json({ error: 'Forbidden' });
    }

    next();
  };
}
```

---

## Related Vulnerabilities

- [IDOR](./idor.md)
- [Privilege Escalation](./privilege-escalation.md)

---

*Document version: 1.0*
*Last updated: 2026-03-04*
