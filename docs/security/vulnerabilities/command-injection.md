# Command Injection

Command Injection occurs when an application passes unsafe user input to a system shell, allowing attackers to execute arbitrary commands on the host system.

---

## Severity

**CRITICAL** - Can lead to complete system compromise, data exfiltration, or pivot to other systems.

---

## How It Works

```
ATTACK FLOW:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Vulnerable Code:
────────────────
const { exec } = require('child_process');
const filename = req.body.filename;

exec(`convert ${filename} output.png`);

User Input:
────────────────
test.png; cat /etc/passwd

Executed Command:
────────────────
convert test.png; cat /etc/passwd output.png

Result:
────────────────
✓ Image converted
✓ Passwords dumped
```

---

## Types of Command Injection

### 1. Direct Command Injection

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

User Input: ; cat /etc/passwd

Full Command:
ls -la ; cat /etc/passwd

Executes both commands
```

### 2. Argument Injection

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Command: ./app --user admin

User Input: admin --admin

Full Command:
./app --user admin --admin

Extra flag passed
```

### 3. Pipe Injection

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

User Input: test | cat /etc/passwd

Full Command:
echo test | cat /etc/passwd

Output piped to malicious command
```

### 4. Environment Injection

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

User Input: test$(whoami)

Full Command:
echo test$(whoami)
echo testroot

Command substitution executed
```

---

## Detection

### AST Patterns to Detect

```typescript
const commandInjectionPatterns = {
  dangerousFunctions: [
    'exec',
    'execSync',
    'spawn',
    'spawnSync',
    'execFile',
    'execFileSync',
    'system',
    'popen',
    'child_process.exec',
    'child_process.spawn',
    'os.system',
    'subprocess.call',
    'subprocess.run',
    'Runtime.exec',
  ],

  check: (node: Node) => {
    // exec(userInput)
    if (isCall(node, 'exec') && hasUserInput(node.arguments[0])) {
      return { vulnerable: true, function: 'exec' };
    }

    // spawn('cmd', [userInput])
    if (isCall(node, 'spawn') && hasUserInputInArray(node.arguments[1])) {
      return { vulnerable: true, function: 'spawn' };
    }

    // Backticks
    if (node.type === 'TemplateLiteral' && hasUserInput(node.expressions)) {
      return { vulnerable: true, function: 'template' };
    }
  },
};
```

### Common Vulnerable Patterns

```typescript
// VULNERABLE - AVOID:

// Node.js
exec(`convert ${filename} output.png`);
spawn('ffmpeg', [userInput, '-o', 'output']);
exec(`ls ${directory}`);

// Python
os.system(f'ffmpeg {user_input}')
subprocess.call(user_input, shell=True)
os.popen(user_input).read()

// Java
Runtime.getRuntime().exec("ffmpeg " + userInput);

// Shell in PHP
shell_exec("convert " . $_POST['filename']);
```

---

## Prevention

### 1. Avoid Shell Execution

```typescript
// BAD - Uses shell
exec(`convert ${filename} output.png`);

// GOOD - Array form, no shell
execFile('convert', [filename, 'output.png']);

// GOOD - Use library instead
import { imageProcessor } from 'my-lib';
await imageProcessor.convert(filename, 'output.png');
```

### 2. Input Validation

```typescript
// Validate against allowlist
const ALLOWED_FILENAMES = /^[a-zA-Z0-9_-]+\.png$/;

function validateFilename(filename: string): string {
  if (!ALLOWED_FILENAMES.test(filename)) {
    throw new Error('Invalid filename');
  }
  return filename;
}

// Validate format
function validateCommandArg(arg: string): string {
  // Only allow alphanumeric and safe chars
  if (!/^[a-zA-Z0-9_-]+$/.test(arg)) {
    throw new Error('Invalid characters in argument');
  }
  return arg;
}
```

### 3. Use Safe Libraries

```typescript
// GOOD - Don't use shell commands

// Instead of: exec('ffmpeg -i input output')
import ffmpeg from 'fluent-ffmpeg';
ffmpeg(input).output(output).run();

// Instead of: exec('convert img -resize 100x100 out')
import sharp from 'sharp';
await sharp(input).resize(100, 100).toFile(output);

// Instead of: exec('tar -xvf ' + archive)
import tar from 'tar';
await tar.extract({ file: archive });
```

### 4. Sandboxing

```typescript
import { spawn } from 'child_process';

// Use isolated environment
const child = spawn('command', args, {
  env: {
    // Minimal environment
    PATH: '/usr/bin:/bin',
    HOME: '/tmp/sandbox',
  },
  cwd: '/tmp/sandbox',
  // Drop privileges
  uid: 1000,
  gid: 1000,
});
```

---

## Testing

### Manual Testing

```
COMMAND INJECTION TEST PAYLOADS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Command Separation
   ; cat /etc/passwd
   | cat /etc/passwd
   & cat /etc/passwd
   \n cat /etc/passwd

2. Command Substitution
   $(cat /etc/passwd)
   `cat /etc/passwd`

3. Argument Injection
   --help
   -L
   "

4. Environment Variables
   $(whoami)
   ${IFS}

5. Time-Based Detection
   ; sleep 5
   && sleep 5
```

### Automated Testing

```typescript
test('command injection prevention', async () => {
  const payloads = [
    '; cat /etc/passwd',
    '| ls',
    '$(whoami)',
    '`id`',
    '--help',
  ];

  for (const payload of payloads) {
    const response = await fetch('/process', {
      method: 'POST',
      body: { filename: payload },
    });

    // Should not execute commands
    expect(response.status).not.toContain(payload);
  }
});
```

---

## Fix Example

```
BEFORE (Vulnerable):
━━━━━━━━━━━━━━━━━━━━

app.post('/upload', (req, res) => {
  const filename = req.body.filename;
  exec(`convert ${filename} thumbnail.jpg`);
  res.send('Done');
});

AFTER (Fixed):
━━━━━━━━━━━━━━━━

import { validateFilename } from './validation';
import { imageProcessor } from './image-lib';

app.post('/upload', async (req, res) => {
  const filename = validateFilename(req.body.filename);

  // Use library instead of shell
  await imageProcessor.createThumbnail(filename, 'thumbnail.jpg');

  res.send('Done');
});
```

---

## Related Vulnerabilities

- [SQL Injection](./sql-injection.md) - Similar pattern
- [Path Traversal](./path-traversal.md) - Related file attacks

---

*Document version: 1.0*
*Last updated: 2026-03-04*
