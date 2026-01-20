# Day 28: Web Security & Best Practices 🔒

## 🎯 Learning Objectives

By the end of Day 28, you will:
- Understand OWASP Top 10 web vulnerabilities
- Implement secure authentication and authorization
- Protect against XSS, CSRF, and injection attacks
- Configure security headers and Content Security Policy
- Handle sensitive data securely
- Implement input validation and sanitization
- Understand API security best practices
- Apply accessibility (a11y) standards (WCAG)
- Optimize for SEO and social media sharing
- Write maintainable, production-ready code

---

## 📚 Topics to Study

### 1. **OWASP Top 10 Security Risks**

```javascript
// 1. Broken Access Control
// Bad: No authorization check
function deleteUser(userId) {
  return fetch(`/api/users/${userId}`, { method: 'DELETE' });
}

// Good: Verify permissions
function deleteUser(userId) {
  if (!currentUser.isAdmin) {
    throw new Error('Unauthorized');
  }
  return fetch(`/api/users/${userId}`, {
    method: 'DELETE',
    headers: {
      'Authorization': `Bearer ${getAuthToken()}`
    }
  });
}

// 2. Cryptographic Failures
// Bad: Storing passwords in plain text
const user = {
  password: 'mypassword123'  // NEVER DO THIS
};

// Good: Hash passwords (backend)
/*
const bcrypt = require('bcrypt');
const hashedPassword = await bcrypt.hash(password, 10);
*/

// Good: Use HTTPS for all communications
// Good: Encrypt sensitive data at rest
// Good: Use secure random tokens

// 3. Injection Attacks
// Bad: SQL Injection vulnerability
const query = `SELECT * FROM users WHERE username = '${username}'`;
// If username is: ' OR '1'='1
// Query becomes: SELECT * FROM users WHERE username = '' OR '1'='1'

// Good: Use parameterized queries (backend)
/*
const query = 'SELECT * FROM users WHERE username = ?';
db.query(query, [username]);
*/

// Frontend: Validate and sanitize all inputs
function sanitizeInput(input) {
  return input
    .replace(/[<>]/g, '') // Remove angle brackets
    .trim()
    .slice(0, 255); // Limit length
}

// 4. Insecure Design
// Implement security from the start
// Use threat modeling
// Apply principle of least privilege
// Implement proper error handling
// Don't expose sensitive information in errors

// 5. Security Misconfiguration
// - Keep dependencies updated
// - Remove default accounts and passwords
// - Disable directory listing
// - Remove unnecessary features
// - Configure proper CORS
// - Set secure headers

// 6. Vulnerable and Outdated Components
// Regularly update dependencies
// npm audit
// npm audit fix
// Use tools like Snyk or Dependabot

// 7. Identification and Authentication Failures
// Covered in detail below

// 8. Software and Data Integrity Failures
// - Use Subresource Integrity (SRI) for CDN
// - Verify package integrity
// - Implement secure CI/CD pipelines

// 9. Security Logging and Monitoring Failures
// - Log security events
// - Monitor for suspicious activity
// - Set up alerts
// - Don't log sensitive data

// 10. Server-Side Request Forgery (SSRF)
// - Validate and sanitize URLs
// - Use allowlists for external requests
// - Implement network segmentation
```

### 2. **Cross-Site Scripting (XSS) Prevention**

```javascript
// Types of XSS:
// 1. Stored XSS: Malicious script stored in database
// 2. Reflected XSS: Script in URL parameters
// 3. DOM-based XSS: Client-side script manipulation

// Bad: Directly inserting user content
function renderComment(comment) {
  return <div dangerouslySetInnerHTML={{ __html: comment }} />;
  // If comment contains: <script>alert('XSS')</script>
}

// Good: React automatically escapes content
function renderComment(comment) {
  return <div>{comment}</div>; // Safe by default
}

// Sanitize HTML if you must render it
import DOMPurify from 'dompurify';

function renderComment(comment) {
  const clean = DOMPurify.sanitize(comment, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'a'],
    ALLOWED_ATTR: ['href']
  });
  return <div dangerouslySetInnerHTML={{ __html: clean }} />;
}

// Escape special characters
function escapeHtml(text) {
  const map = {
    '&': '&amp;',
    '<': '&lt;',
    '>': '&gt;',
    '"': '&quot;',
    "'": '&#039;'
  };
  return text.replace(/[&<>"']/g, (m) => map[m]);
}

// Content Security Policy (CSP)
/*
HTTP Header:
Content-Security-Policy: 
  default-src 'self';
  script-src 'self' 'unsafe-inline' https://trusted-cdn.com;
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https:;
  font-src 'self' https://fonts.gstatic.com;
  connect-src 'self' https://api.example.com;
  frame-ancestors 'none';
  base-uri 'self';
  form-action 'self';

This prevents:
- Inline scripts (except explicitly allowed)
- Loading resources from unauthorized domains
- Clickjacking
- Form submission to external sites
*/

// Meta tag CSP (fallback)
// <meta 
//   http-equiv="Content-Security-Policy" 
//   content="default-src 'self'; script-src 'self' 'unsafe-inline'"
// />
```

### 3. **Cross-Site Request Forgery (CSRF) Prevention**

```javascript
// CSRF Attack Example:
// User is logged into bank.com
// Attacker tricks user to visit evil.com
// evil.com contains: <img src="https://bank.com/transfer?to=attacker&amount=1000">
// Request is sent with user's cookies

// Prevention Methods:

// 1. CSRF Tokens
// Server sends token with form
// Client includes token in requests
function sendRequest(data) {
  const csrfToken = document.querySelector('meta[name="csrf-token"]').content;
  
  return fetch('/api/data', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-CSRF-Token': csrfToken
    },
    body: JSON.stringify(data)
  });
}

// 2. SameSite Cookies
/*
Set-Cookie: sessionId=abc123; SameSite=Strict; Secure; HttpOnly

SameSite=Strict: Cookie only sent to same site
SameSite=Lax: Cookie sent with top-level navigation
SameSite=None: Cookie sent with all requests (requires Secure)
*/

// 3. Custom Headers
// Browsers don't allow custom headers in simple CORS requests
// from different origins without preflight
fetch('/api/data', {
  method: 'POST',
  headers: {
    'X-Requested-With': 'XMLHttpRequest',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(data)
});

// 4. Verify Origin Header
/*
Backend checks:
if (req.headers.origin !== 'https://trusted-site.com') {
  return res.status(403).send('Forbidden');
}
*/
```

### 4. **Authentication & Authorization**

```javascript
// Secure Authentication Flow

// 1. Password Requirements
const passwordRules = {
  minLength: 12,
  requireUppercase: true,
  requireLowercase: true,
  requireNumbers: true,
  requireSpecialChars: true
};

function validatePassword(password) {
  if (password.length < passwordRules.minLength) {
    return 'Password too short';
  }
  if (passwordRules.requireUppercase && !/[A-Z]/.test(password)) {
    return 'Password must contain uppercase letter';
  }
  if (passwordRules.requireLowercase && !/[a-z]/.test(password)) {
    return 'Password must contain lowercase letter';
  }
  if (passwordRules.requireNumbers && !/\d/.test(password)) {
    return 'Password must contain number';
  }
  if (passwordRules.requireSpecialChars && !/[!@#$%^&*]/.test(password)) {
    return 'Password must contain special character';
  }
  return null;
}

// 2. JWT Token Management
// Store in httpOnly cookie (most secure)
// Or store in memory (doesn't persist on refresh)
// NEVER store in localStorage (XSS vulnerable)

class AuthService {
  constructor() {
    this.token = null;
    this.refreshToken = null;
  }
  
  async login(email, password) {
    const response = await fetch('/api/auth/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password }),
      credentials: 'include' // Include cookies
    });
    
    const data = await response.json();
    this.token = data.accessToken;
    
    // Set token expiration timeout
    this.scheduleTokenRefresh(data.expiresIn);
    
    return data;
  }
  
  scheduleTokenRefresh(expiresIn) {
    // Refresh token 5 minutes before expiration
    const timeout = (expiresIn - 300) * 1000;
    
    setTimeout(() => {
      this.refreshAccessToken();
    }, timeout);
  }
  
  async refreshAccessToken() {
    try {
      const response = await fetch('/api/auth/refresh', {
        method: 'POST',
        credentials: 'include'
      });
      
      const data = await response.json();
      this.token = data.accessToken;
      this.scheduleTokenRefresh(data.expiresIn);
    } catch (error) {
      // Refresh failed, redirect to login
      this.logout();
    }
  }
  
  logout() {
    this.token = null;
    fetch('/api/auth/logout', {
      method: 'POST',
      credentials: 'include'
    });
    window.location.href = '/login';
  }
  
  getAuthHeader() {
    return this.token ? `Bearer ${this.token}` : '';
  }
}

// 3. Protected Routes
function ProtectedRoute({ children, requiredRole }) {
  const { user, isAuthenticated } = useAuth();
  
  if (!isAuthenticated) {
    return <Navigate to="/login" />;
  }
  
  if (requiredRole && user.role !== requiredRole) {
    return <Navigate to="/unauthorized" />;
  }
  
  return children;
}

// Usage
// <Route 
//   path="/admin" 
//   element={
//     <ProtectedRoute requiredRole="admin">
//       <AdminDashboard />
//     </ProtectedRoute>
//   } 
// />

// 4. Multi-Factor Authentication (MFA)
async function verifyMFA(code) {
  const response = await fetch('/api/auth/verify-mfa', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${tempToken}`
    },
    body: JSON.stringify({ code })
  });
  
  return response.json();
}
```

### 5. **Input Validation & Sanitization**

```javascript
// Client-side validation (convenience)
// Server-side validation (security) - ALWAYS required

// 1. Form Validation
import { z } from 'zod';

const registrationSchema = z.object({
  email: z.string().email('Invalid email'),
  password: z.string()
    .min(12, 'Password must be at least 12 characters')
    .regex(/[A-Z]/, 'Must contain uppercase')
    .regex(/[a-z]/, 'Must contain lowercase')
    .regex(/[0-9]/, 'Must contain number')
    .regex(/[^A-Za-z0-9]/, 'Must contain special character'),
  username: z.string()
    .min(3, 'Username too short')
    .max(20, 'Username too long')
    .regex(/^[a-zA-Z0-9_]+$/, 'Invalid characters'),
  age: z.number()
    .int('Must be an integer')
    .min(18, 'Must be 18 or older')
    .max(120, 'Invalid age')
});

function validateForm(data) {
  try {
    registrationSchema.parse(data);
    return { valid: true, errors: null };
  } catch (error) {
    return { valid: false, errors: error.errors };
  }
}

// 2. Sanitize User Input
import DOMPurify from 'dompurify';
import validator from 'validator';

function sanitizeUserInput(input) {
  return {
    // Remove HTML tags
    text: DOMPurify.sanitize(input.text, { ALLOWED_TAGS: [] }),
    
    // Validate and normalize email
    email: validator.isEmail(input.email) 
      ? validator.normalizeEmail(input.email)
      : '',
    
    // Validate URL
    url: validator.isURL(input.url) ? input.url : '',
    
    // Escape special characters
    escaped: validator.escape(input.raw)
  };
}

// 3. Rate Limiting (Frontend)
class RateLimiter {
  constructor(maxRequests, windowMs) {
    this.maxRequests = maxRequests;
    this.windowMs = windowMs;
    this.requests = [];
  }
  
  canMakeRequest() {
    const now = Date.now();
    this.requests = this.requests.filter(
      time => now - time < this.windowMs
    );
    
    if (this.requests.length >= this.maxRequests) {
      return false;
    }
    
    this.requests.push(now);
    return true;
  }
}

// Usage
const apiLimiter = new RateLimiter(10, 60000); // 10 requests per minute

async function callApi() {
  if (!apiLimiter.canMakeRequest()) {
    throw new Error('Rate limit exceeded');
  }
  
  return fetch('/api/data');
}

// 4. File Upload Validation
function validateFile(file) {
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif'];
  const maxSize = 5 * 1024 * 1024; // 5MB
  
  if (!allowedTypes.includes(file.type)) {
    throw new Error('Invalid file type');
  }
  
  if (file.size > maxSize) {
    throw new Error('File too large');
  }
  
  // Validate file extension
  const allowedExtensions = ['.jpg', '.jpeg', '.png', '.gif'];
  const extension = file.name.slice(file.name.lastIndexOf('.')).toLowerCase();
  
  if (!allowedExtensions.includes(extension)) {
    throw new Error('Invalid file extension');
  }
  
  return true;
}
```

### 6. **Security Headers**

```javascript
// Configure security headers (usually on server/CDN)
/*
// 1. Strict-Transport-Security (HSTS)
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
// Forces HTTPS for 1 year

// 2. X-Frame-Options
X-Frame-Options: DENY
// Prevents clickjacking by disabling iframe embedding

// 3. X-Content-Type-Options
X-Content-Type-Options: nosniff
// Prevents MIME type sniffing

// 4. X-XSS-Protection
X-XSS-Protection: 1; mode=block
// Enables browser XSS filter

// 5. Referrer-Policy
Referrer-Policy: strict-origin-when-cross-origin
// Controls referrer information

// 6. Permissions-Policy
Permissions-Policy: geolocation=(), microphone=(), camera=()
// Controls browser features

// 7. Content-Security-Policy (see XSS section)

// Netlify configuration (_headers file)
/*
  X-Frame-Options: DENY
  X-Content-Type-Options: nosniff
  X-XSS-Protection: 1; mode=block
  Referrer-Policy: strict-origin-when-cross-origin
  Permissions-Policy: geolocation=(), microphone=(), camera=()
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'
*/
```

### 7. **Accessibility (a11y) Best Practices**

```jsx
// 1. Semantic HTML
// Bad
<div onClick={handleClick}>Click me</div>

// Good
<button onClick={handleClick}>Click me</button>

// 2. ARIA Attributes
function Modal({ isOpen, onClose, children }) {
  return isOpen ? (
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby="modal-title"
      aria-describedby="modal-description"
    >
      <h2 id="modal-title">Modal Title</h2>
      <div id="modal-description">{children}</div>
      <button onClick={onClose} aria-label="Close modal">
        ×
      </button>
    </div>
  ) : null;
}

// 3. Keyboard Navigation
function Button({ onClick, children }) {
  const handleKeyPress = (e) => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault();
      onClick();
    }
  };
  
  return (
    <button
      onClick={onClick}
      onKeyPress={handleKeyPress}
      tabIndex={0}
    >
      {children}
    </button>
  );
}

// 4. Focus Management
function SearchModal({ isOpen, onClose }) {
  const inputRef = useRef(null);
  
  useEffect(() => {
    if (isOpen && inputRef.current) {
      inputRef.current.focus();
    }
  }, [isOpen]);
  
  return (
    <div role="dialog" aria-modal="true">
      <input
        ref={inputRef}
        type="text"
        aria-label="Search"
        placeholder="Search..."
      />
    </div>
  );
}

// 5. Skip Links
function App() {
  return (
    <>
      <a href="#main-content" className="skip-link">
        Skip to main content
      </a>
      <nav>Navigation</nav>
      <main id="main-content">
        Content
      </main>
    </>
  );
}

// CSS for skip link
/*
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  background: #000;
  color: #fff;
  padding: 8px;
  z-index: 100;
}

.skip-link:focus {
  top: 0;
}
*/

// 6. Image Alt Text
<img 
  src="profile.jpg" 
  alt="User profile picture showing a smiling person" 
/>

// Decorative images
<img src="decoration.jpg" alt="" role="presentation" />

// 7. Form Labels
<label htmlFor="email">
  Email Address
  <input
    id="email"
    type="email"
    aria-required="true"
    aria-invalid={hasError}
    aria-describedby="email-error"
  />
</label>
{hasError && (
  <span id="email-error" role="alert">
    Please enter a valid email
  </span>
)}

// 8. Color Contrast
// Ensure minimum contrast ratios (WCAG 2.1):
// - Normal text: 4.5:1
// - Large text: 3:1
// - UI components: 3:1

// 9. Screen Reader Testing
// Use NVDA (Windows), JAWS (Windows), or VoiceOver (Mac)

// 10. Automated Testing
// npm install --save-dev @axe-core/react

import { useEffect } from 'react';

if (process.env.NODE_ENV !== 'production') {
  const axe = require('@axe-core/react');
  axe(React, ReactDOM, 1000);
}
```

### 8. **SEO Best Practices**

```jsx
// 1. Meta Tags
import { Helmet } from 'react-helmet';

function ProductPage({ product }) {
  return (
    <>
      <Helmet>
        <title>{product.name} | My Store</title>
        <meta name="description" content={product.description} />
        
        {/* Open Graph (Facebook, LinkedIn) */}
        <meta property="og:title" content={product.name} />
        <meta property="og:description" content={product.description} />
        <meta property="og:image" content={product.image} />
        <meta property="og:url" content={`https://example.com/products/${product.id}`} />
        <meta property="og:type" content="product" />
        
        {/* Twitter Card */}
        <meta name="twitter:card" content="summary_large_image" />
        <meta name="twitter:title" content={product.name} />
        <meta name="twitter:description" content={product.description} />
        <meta name="twitter:image" content={product.image} />
        
        {/* Canonical URL */}
        <link rel="canonical" href={`https://example.com/products/${product.id}`} />
      </Helmet>
      
      <div>{/* Product content */}</div>
    </>
  );
}

// 2. Structured Data (JSON-LD)
function ProductStructuredData({ product }) {
  const structuredData = {
    "@context": "https://schema.org",
    "@type": "Product",
    "name": product.name,
    "image": product.image,
    "description": product.description,
    "brand": {
      "@type": "Brand",
      "name": product.brand
    },
    "offers": {
      "@type": "Offer",
      "price": product.price,
      "priceCurrency": "USD",
      "availability": "https://schema.org/InStock"
    }
  };
  
  return (
    <script
      type="application/ld+json"
      dangerouslySetInnerHTML={{ __html: JSON.stringify(structuredData) }}
    />
  );
}

// 3. Sitemap Generation
// sitemap.xml (generated at build time)
/*
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://example.com/</loc>
    <lastmod>2024-01-01</lastmod>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://example.com/products</loc>
    <lastmod>2024-01-01</lastmod>
    <priority>0.8</priority>
  </url>
</urlset>
*/

// 4. robots.txt
/*
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/

Sitemap: https://example.com/sitemap.xml
*/

// 5. Performance for SEO
// - Fast loading times (Core Web Vitals)
// - Mobile-friendly design
// - HTTPS
// - Proper image optimization
// - Minimize JavaScript
```

---

## 💡 Key Concepts

1. **Defense in Depth**: Multiple layers of security
2. **Principle of Least Privilege**: Minimal access rights
3. **Zero Trust**: Never trust, always verify
4. **Secure by Default**: Security built-in from the start
5. **Input Validation**: Never trust user input
6. **Output Encoding**: Properly escape output
7. **Accessibility**: Web for everyone
8. **Performance**: Speed is a feature

---

## 🛠️ Practical Exercises

### Exercise 1: Security Audit
Audit an existing application for security vulnerabilities.

### Exercise 2: Implement Authentication
Build a secure authentication system with JWT and refresh tokens.

### Exercise 3: Accessibility Compliance
Make an application WCAG 2.1 AA compliant.

### Exercise 4: SEO Optimization
Optimize an application for search engines.

### Exercise 5: Penetration Testing
Test an application for XSS, CSRF, and injection vulnerabilities.

---

## 🎨 Project: Secure E-Commerce Platform

Build a production-ready secure application:

**Security Features:**
1. Secure authentication with MFA
2. Role-based access control
3. Input validation and sanitization
4. XSS and CSRF protection
5. Security headers configuration
6. Rate limiting
7. Secure file uploads
8. Error handling without information leakage

**Accessibility Features:**
1. WCAG 2.1 AA compliance
2. Keyboard navigation
3. Screen reader support
4. ARIA attributes
5. Focus management
6. Color contrast compliance

**SEO Features:**
1. Meta tags and Open Graph
2. Structured data
3. Sitemap generation
4. robots.txt configuration
5. Performance optimization
6. Mobile responsiveness

---

## 📖 Resources to Explore

### Documentation
- **OWASP**: https://owasp.org/
- **WCAG Guidelines**: https://www.w3.org/WAI/WCAG21/quickref/
- **MDN Security**: https://developer.mozilla.org/en-US/docs/Web/Security

### Video Tutorials
- **Web Security** by freeCodeCamp
- **Accessibility** by Google Chrome Developers
- **OWASP Top 10** by OWASP

### Tools
- **OWASP ZAP**: Security testing
- **Axe DevTools**: Accessibility testing
- **Lighthouse**: SEO and accessibility audits
- **Snyk**: Dependency vulnerability scanning
- **SecurityHeaders.com**: Header analysis

---

## 🔍 Interview Questions

### Basic
1. What is XSS and how do you prevent it?
2. Explain CSRF attacks.
3. What is HTTPS and why is it important?
4. What are WCAG guidelines?
5. What is SEO?

### Intermediate
1. Explain the OWASP Top 10.
2. How do you implement secure authentication?
3. What are security headers and why are they important?
4. How do you make a website accessible?
5. What is Content Security Policy?
6. How do you prevent injection attacks?
7. Explain CORS and its security implications.

### Advanced
1. Design a secure authentication system with OAuth 2.0.
2. How would you prevent and detect security vulnerabilities?
3. Explain different types of XSS attacks and prevention.
4. How do you implement role-based access control?
5. What's your approach to security testing?
6. How do you handle PCI DSS compliance?
7. Explain security implications of third-party dependencies.

### Practical
1. You discovered a security vulnerability in production. What do you do?
2. Design a secure API authentication system.
3. How would you make a complex web app fully accessible?
4. Audit this code for security issues: [code sample]

---

## ✅ Success Criteria

- [ ] Understand OWASP Top 10 vulnerabilities
- [ ] Implement secure authentication
- [ ] Protect against XSS and CSRF
- [ ] Configure security headers and CSP
- [ ] Validate and sanitize all inputs
- [ ] Implement proper error handling
- [ ] Make applications accessible (WCAG 2.1 AA)
- [ ] Optimize for SEO
- [ ] Handle sensitive data securely
- [ ] Apply security best practices consistently

---

## 🚀 Next Steps

**Week 5 Preview (Days 29-30):**
- **Day 29**: Final Project Planning and Architecture
- **Day 30**: Portfolio Project Implementation

**After 30 Days:**
- Continue building projects
- Contribute to open source
- Join developer communities
- Stay updated with latest trends
- Prepare for interviews
- Build your portfolio
- Network with other developers

**Resources for Continued Learning:**
- Frontend Masters
- MDN Web Docs
- freeCodeCamp
- JavaScript.info
- Web.dev
- CSS-Tricks

---

## 🎓 Congratulations!

You've completed Week 4 and learned:
- ✅ TypeScript fundamentals and React integration
- ✅ Testing with Jest and React Testing Library
- ✅ Build tools and optimization
- ✅ Git workflows and collaboration
- ✅ CI/CD and deployment
- ✅ Web security and best practices

You're now equipped with advanced frontend development skills! 🎉

**Remember**: Security and accessibility aren't optional—they're essential for professional web development! 🔒

---

**"Security is not a product, but a process." - Bruce Schneier**
