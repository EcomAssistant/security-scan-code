# OWASP Top 10 (2021) Vulnerability Patterns

Quick reference for identifying common vulnerability patterns during code review.

---

## A01:2021 - Broken Access Control

### Patterns to look for

**IDOR (Insecure Direct Object Reference):**
```php
// VULNERABLE: User can change ID to access other users' data
$user = User::find($_GET['id']);
```
```python
# VULNERABLE: No ownership check
order = Order.query.get(request.args.get('order_id'))
```

**Missing Authorization Check:**
```python
# VULNERABLE: No role check
@app.route('/admin/users')
def list_users():
    return User.query.all()
```

**Privilege Escalation:**
```javascript
// VULNERABLE: Role from client-side
const role = req.body.role; // attacker sends role=admin
await User.update({ role });
```

### Fix Pattern
```python
# Always verify ownership + role
@app.route('/api/orders/<order_id>')
@require_auth
def get_order(order_id):
    order = Order.query.get(order_id)
    if not order or (order.user_id != current_user.id and not current_user.is_admin):
        abort(403)
    return order
```

---

## A02:2021 - Cryptographic Failures

### Patterns to look for

**Weak Hashing:**
```php
// VULNERABLE
$hash = md5($password);
$hash = sha1($password);
```

**Hardcoded Keys:**
```python
# VULNERABLE
SECRET_KEY = "my-secret-key-123"
API_KEY = "sk-abc123def456"
```

**Insecure Random:**
```python
# VULNERABLE for security purposes
import random
token = random.randint(100000, 999999)
```

**Unencrypted Sensitive Data:**
```python
# VULNERABLE: Storing PII in plain text
user.ssns.append(raw_ssn)
```

### Fix Pattern
```python
# Password hashing
from argon2 import PasswordHasher
ph = PasswordHasher()
hash = ph.hash(password)

# Secure random
import secrets
token = secrets.token_urlsafe(32)

# Environment-based secrets
SECRET_KEY = os.environ['SECRET_KEY']
```

---

## A03:2021 - Injection

### SQL Injection
```php
// VULNERABLE
$sql = "SELECT * FROM users WHERE id = " . $_GET['id'];
$db->query($sql);
```
```python
# VULNERABLE
cursor.execute(f"SELECT * FROM users WHERE name = '{name}'")
```

### Command Injection
```python
# VULNERABLE
os.system(f"ping {user_input}")
subprocess.call(f"nslookup {domain}", shell=True)
```

### Fix Pattern
```python
# SQL: Parameterized queries
cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))

# ORM
User.objects.filter(id=user_id)

# Command: Array form
subprocess.run(['ping', '-c', '4', user_input])
```

---

## A04:2021 - Insecure Design

### Patterns to look for

- Security questions as sole recovery mechanism
- Rate limiting only on login, not on other sensitive endpoints
- Security through obscurity (hidden admin URLs)
- No security architecture documentation
- Trust boundaries not defined

---

## A05:2021 - Security Misconfiguration

### Patterns to look for

```python
# VULNERABLE
app.run(debug=True)  # Flask debug mode in production
```
```php
// VULNERABLE
ini_set('display_errors', 1);  // in production
```
```yaml
# VULNERABLE CORS
Access-Control-Allow-Origin: *
```

### Fix Pattern
```python
# Environment-based configuration
DEBUG = os.environ.get('FLASK_ENV') == 'development'

# Restrictive CORS
CORS(app, origins=['https://app.example.com'],
     supports_credentials=True)
```

---

## A06:2021 - Vulnerable and Outdated Components

### Patterns to look for

- Unpinned dependency versions
- Known CVEs in `package-lock.json`, `requirements.txt`, `composer.lock`
- Deprecated packages still in use
- No dependency scanning in CI/CD
- Docker images based on full OS instead of minimal (alpine/distroless)

---

## A07:2021 - Identification and Authentication Failures

### Patterns to look for

**Weak Password Policy:**
```python
# VULNERABLE: No password complexity
user = User(username=username, password=password)
user.save()
```

**Credential Exposure:**
```python
# VULNERABLE: Password in URL
GET /api/login?username=admin&password=secret123
```

**No Brute Force Protection:**
```python
# VULNERABLE: No rate limiting on login
@app.route('/login', methods=['POST'])
def login():
    user = authenticate(request.form['username'], request.form['password'])
    # No attempt tracking, no lockout
```

### Fix Pattern
```python
from flask_limiter import Limiter

limiter = Limiter(app, key_func=get_remote_address)

@app.route('/login', methods=['POST'])
@limiter.limit("5/minute")
def login():
    # Track failed attempts
    # Lock account after N failures
    # Return generic error message
```

---

## A08:2021 - Software and Data Integrity Failures

### Patterns to look for

**Insecure Deserialization:**
```python
# VULNERABLE
import pickle
data = pickle.loads(user_input)

import yaml
config = yaml.load(user_input)  # uses unsafe Loader by default
```

**Unverified Updates:**
```python
# VULNERABLE: No integrity check on downloaded update
requests.get('http://update-server.com/latest')
```

### Fix Pattern
```python
# Use safe alternatives
import json
data = json.loads(user_input)

import yaml
config = yaml.safe_load(user_input)  # safe_load restricts to basic types
```

---

## A09:2021 - Security Logging and Monitoring Failures

### Patterns to look for

```python
# VULNERABLE: No logging of security events
@app.route('/login', methods=['POST'])
def login():
    if not authenticate(user, password):
        return "Login failed", 401  # No logging!
```

```python
# VULNERABLE: Logging sensitive data
app.logger.info(f"User login: {username}, password: {password}")
```

### Fix Pattern
```python
import structlog
logger = structlog.get_logger()

@app.route('/login', methods=['POST'])
def login():
    if not authenticate(user, password):
        logger.warn("login_failed", username=username, ip=request.remote_addr)
        return "Invalid credentials", 401
    logger.info("login_success", user_id=user.id)
```

---

## A10:2021 - Server-Side Request Forgery (SSRF)

### Patterns to look for

```python
# VULNERABLE: User controls the URL
url = request.args.get('url')
response = requests.get(url)
```

```python
# VULNERABLE: Webhook URL not validated
@app.route('/webhook', methods=['POST'])
def set_webhook():
    webhook_url = request.json['url']
    save_webhook(webhook_url)  # Could point to internal services
```

### Fix Pattern
```python
from urllib.parse import urlparse
import ipaddress

def is_safe_url(url):
    parsed = urlparse(url)
    if parsed.scheme not in ('http', 'https'):
        return False
    # Resolve and check against private IP ranges
    try:
        ip = ipaddress.ip_address(socket.gethostbyname(parsed.hostname))
        if ip.is_private or ip.is_loopback:
            return False
    except ValueError:
        return False
    return True
```

---

## CWE Quick Reference

| CWE | Name | Common Pattern |
|-----|------|---------------|
| CWE-22 | Path Traversal | `open(user_input)` |
| CWE-78 | OS Command Injection | `os.system(user_input)` |
| CWE-79 | XSS | `element.innerHTML = user_input` |
| CWE-89 | SQL Injection | `f"SELECT ... {user_input}"` |
| CWE-200 | Info Exposure | Stack traces in production |
| CWE-287 | Auth Bypass | Missing auth check on endpoint |
| CWE-306 | Missing Auth | Resource accessible without login |
| CWE-327 | Weak Crypto | MD5, DES, ECB mode |
| CWE-502 | Deserialization | `pickle.loads(user_input)` |
| CWE-639 | IDOR | `User.find(params[:id])` without ownership check |
| CWE-798 | Hardcoded Creds | `password = "admin123"` |
| CWE-918 | SSRF | `requests.get(user_url)` |
| CWE-922 | Insecure Storage | PII stored in plain text |
| CWE-1021 | Rendered UI | Unescaped user input in HTML |
