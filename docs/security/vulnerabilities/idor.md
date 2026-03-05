# IDOR (Insecure Direct Object Reference)

IDOR occurs when an application exposes direct references to internal objects (database IDs, filenames) without proper authorization checks.

---

## Severity

**HIGH** - Allows attackers to access unauthorized resources by manipulating object references.

---

## Types of IDOR

### 1. URL-Based IDOR

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

User accesses other users' invoices:

GET /api/invoices/1234    → Own invoice
GET /api/invoices/1235    → Another user's invoice!

 attacker just changes the ID in the URL
```

### 2. Parameter-Based IDOR

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

GET /api/download?file=report.pdf
GET /api/download?file=../../etc/passwd

 attacker manipulates file parameters
```

### 3. HTTP Header IDOR

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

GET /api/orders
X-User-ID: 12345

 attacker changes header to access other user
```

### 4. Cookie-Based IDOR

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Cookie: account_id=12345
GET /api/account/12345

 attacker changes cookie value
```

---

## Detection

### AST Patterns

```typescript
const idorPatterns = {
  issues: [
    // Database query with user-controlled ID
    {
      pattern: 'db.findUnique',
      check: (node) => hasUserInput(node.where) && !hasOwnershipCheck(node),
      issue: 'Query without ownership verification',
    },
    // File access with user input
    {
      pattern: 'fs.readFile',
      check: (node) => hasUserInput(node.path),
      issue: 'File path from user input',
    },
    // API endpoint with object ID
    {
      pattern: 'router.get',
      check: (node) => hasIdParam(node.path) && !hasAuthCheck(node),
      issue: 'Endpoint accessible without authorization',
    },
  ],
};
```

---

## Prevention

### 1. Verify Object Ownership

```typescript
// GOOD - Check ownership
app.get('/api/document/:id', async (req, res) => {
  const doc = await db.document.findUnique({
    where: { id: req.params.id },
  });

  // Verify ownership
  if (doc.ownerId !== req.user.id) {
    return res.status(403).json({ error: 'Forbidden' });
  }

  res.json(doc);
});
```

### 2. Use Indirect References

```typescript
// GOOD - Indirect object references
const documentRefs = new Map();
// Map internal IDs to random references

function getInternalId(ref: string): number {
  return documentRefs.get(ref);
}

// Endpoint uses references, not IDs
app.get('/api/doc/:ref', async (req, res) => {
  const id = getInternalId(req.params.ref);
  const doc = await db.document.findUnique({ where: { id } });
  // ...
});
```

### 3. Authorization Middleware

```typescript
// GOOD - Generic authorization
const authorizeObject = (resource: string) => {
  return async (req: Request, res: Response, next: NextFunction) => {
    const resourceId = req.params.id;

    const hasAccess = await checkAccess({
      user: req.user,
      resource,
      resourceId,
    });

    if (!hasAccess) {
      return res.status(403).json({ error: 'Forbidden' });
    }

    next();
  };
};

app.get('/api/document/:id', authorizeObject('document'), handler);
```

---

## Fix Example

```
BEFORE (Vulnerable):
━━━━━━━━━━━━━━━━━━━━

app.get('/api/invoice/:id', (req, res) => {
  const invoice = db.getInvoice(req.params.id);
  res.json(invoice);
});

AFTER (Fixed):
━━━━━━━━━━━━━━━━

app.get('/api/invoice/:id',
  authorize(),
  async (req, res) => {
    const invoice = await db.invoice.findUnique({
      where: { id: req.params.id },
    });

    if (!invoice) {
      return res.status(404).json({ error: 'Not found' });
    }

    // Ownership check
    if (invoice.userId !== req.user.id && req.user.role !== 'admin') {
      return res.status(403).json({ error: 'Forbidden' });
    }

    res.json(invoice);
  }
);
```

---

## Related Vulnerabilities

- [Broken Access Control](./broken-access-control.md)
- [Privilege Escalation](./privilege-escalation.md)

---

*Document version: 1.0*
*Last updated: 2026-03-04*
