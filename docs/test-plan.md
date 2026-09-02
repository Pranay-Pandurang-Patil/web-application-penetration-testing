# Web Application Penetration Testing Plan

## Testing Workflow

Each test follows:

**Learn → Test → Capture Evidence → Verify → Assess → Remediate → Retest**

---

## Phase 1 — Reconnaissance

### Browser Developer Tools
- [x] Network request analysis
- [x] Resource identification
- [ ] Cookies
- [ ] Local Storage
- [ ] Session Storage
- [ ] JavaScript inspection
- [ ] Client-side information exposure

### curl
- [x] HTTP HEAD request
- [x] GET request
- [x] Verbose request
- [x] Headers + response body
- [x] OPTIONS request
- [ ] Redirect handling
- [ ] Cookie handling
- [ ] Custom HTTP headers
- [ ] POST request testing

### Nmap
- [x] Basic port scanning
- [x] Service/version detection
- [x] Specific port scanning
- [x] Port range scanning
- [x] Targeted service enumeration
- [x] Default NSE scripts
- [ ] Additional relevant enumeration

### WhatWeb
- [x] Basic technology fingerprinting
- [x] Aggressive detection
- [x] Verbose detection

### Wappalyzer
- [x] Technology identification
- [ ] Additional client-side technology analysis

---

## Phase 2 — HTTP & Web Analysis

- [ ] HTTP methods
- [ ] Response headers
- [ ] Security headers
- [ ] Cookies
- [ ] CORS
- [ ] Cache behavior
- [ ] Redirects
- [ ] Client-side resources
- [ ] Source maps
- [ ] Information disclosure

---

## Phase 3 — Burp Suite

- [ ] Proxy configuration
- [ ] HTTP history
- [ ] Request/response inspection
- [ ] Repeater
- [ ] Request modification
- [ ] Parameter testing
- [ ] Authentication testing
- [ ] Authorization testing
- [ ] Session testing
- [ ] Input validation
- [ ] Manual vulnerability verification

---

## Phase 4 — OWASP ZAP

- [ ] Passive scanning
- [ ] Application crawling
- [ ] Spidering
- [ ] Active scanning
- [ ] Alert analysis
- [ ] Manual verification
- [ ] Security report generation

---

## Phase 5 — Endpoint Discovery

### ffuf
- [ ] Directory discovery
- [ ] Endpoint discovery
- [ ] File discovery
- [ ] Response filtering
- [ ] Extension testing
- [ ] Output analysis

### Gobuster
- [ ] Directory enumeration
- [ ] File discovery
- [ ] Relevant content enumeration

### Nikto
- [ ] Web server checks
- [ ] Configuration checks
- [ ] Information disclosure checks

---

## Phase 6 — Vulnerability Testing

### Authentication
- [ ] Login behavior
- [ ] Account enumeration
- [ ] Password policy
- [ ] Authentication bypass
- [ ] Password reset
- [ ] Session handling

### Authorization
- [ ] Access control
- [ ] Horizontal privilege escalation
- [ ] Vertical privilege escalation
- [ ] IDOR/BOLA

### Input Validation
- [ ] XSS
- [ ] Injection
- [ ] Parameter manipulation
- [ ] Malformed input

### Session Security
- [ ] Cookie security
- [ ] Session expiration
- [ ] Session invalidation
- [ ] Token handling
- [ ] CSRF

### API Security
- [ ] Endpoint discovery
- [ ] Authentication
- [ ] Authorization
- [ ] Input validation
- [ ] CORS
- [ ] Rate limiting
- [ ] Data exposure
- [ ] Error handling

### Client-Side Security
- [ ] DOM XSS
- [ ] JavaScript analysis
- [ ] Local Storage
- [ ] Session Storage
- [ ] Sensitive client-side data
- [ ] Third-party resources

### Business Logic
- [ ] Workflow manipulation
- [ ] Parameter tampering
- [ ] Unauthorized actions
- [ ] State manipulation

---

## Phase 7 — Conditional Testing

These tests will only be performed when relevant technologies are identified.

- [ ] SQL injection / SQLMap
- [ ] JWT security
- [ ] File upload security
- [ ] TLS / OpenSSL
- [ ] Custom Python security scripts

---

## Phase 8 — Findings

For every confirmed vulnerability:

- [ ] Reproduce vulnerability
- [ ] Capture evidence
- [ ] Determine impact
- [ ] Assign severity
- [ ] Document affected component
- [ ] Recommend remediation

---

## Phase 9 — Remediation & Retesting

- [ ] Apply fixes
- [ ] Reproduce original test
- [ ] Confirm vulnerability is fixed
- [ ] Check for regression
- [ ] Document retest result

---

## Phase 10 — Final Report

- [ ] Executive summary
- [ ] Scope
- [ ] Methodology
- [ ] Tools
- [ ] Testing results
- [ ] Confirmed vulnerabilities
- [ ] Severity classification
- [ ] Evidence
- [ ] Remediation
- [ ] Retesting
- [ ] Final security assessment