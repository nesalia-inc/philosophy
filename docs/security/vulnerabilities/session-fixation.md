# Session Fixation

Session Fixation allows attackers to hijack valid user sessions.

---

## Severity

**MEDIUM** - Can lead to account takeover.

---

## How It Works

```
ATTACK FLOW:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Attacker obtains a session ID

2. Attacker tricks user into using that ID

3. User logs in (session is now authenticated)

4. Attacker uses the same session ID

5. Attacker has access to user's account!
```

---

## Prevention

### 1. Regenerate Session ID

```typescript
// GOOD - Regenerate after login
app.post('/login', (req, res) => {
  // Authenticate user
  req.session.regenerate((err) => {
    req.session.userId = user.id;
    res.redirect('/dashboard');
  });
});
```

### 2. Set Secure Cookie

```typescript
// GOOD - Secure session cookies
app.use(session({
  name: 'sessionId',
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  cookie: {
    secure: true,      // HTTPS only
    httpOnly: true,   // No JS access
    sameSite: 'strict',
    maxAge: 3600000   // 1 hour
  }
}));
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
