# Web Application Penetration Testing Report

## Planetorium Web Application

**Project Type:** Authorized Web Application Security Assessment  
**Assessment Environment:** Local Development and Production Preview  
**Application Technology:** Vue.js / Vite  
**Testing Approach:** Manual and Automated Security Testing  
**Report Status:** Final Assessment Report

---

# 1. Executive Summary

This project involved an authorized security assessment of the Planetorium web application to identify potential security weaknesses, evaluate application behavior, and assess its development and production-preview configurations.

The assessment combined manual testing techniques with automated security tools, including Nmap, WhatWeb, Wappalyzer, Burp Suite, and OWASP ZAP.

Testing covered network reconnaissance, technology identification, HTTP request and response analysis, browser-based inspection, WebSocket traffic analysis, development-resource exposure, security-header assessment, and initial XSS checks.

A production build was also generated and tested separately to distinguish development-server behavior from production-preview behavior.

### Key Results

- Three missing security headers were observed in the production-preview response.
- Development resources and transformed Vue source files were accessible through the development server.
- The inspected production preview did not expose the tested development-resource paths or selected project files.
- No standalone source-map files were found in the production build.
- No XSS vulnerability was confirmed during limited testing.
- No conventional backend API communication was identified in the inspected project source.
- No application authentication or authorization functionality was identified.
- Automated scanner alerts were manually reviewed, and identified false positives were excluded from confirmed vulnerabilities.

**Overall assessment:** The testing identified security configuration weaknesses and development-environment information exposure. No confirmed exploitable application vulnerability was established through the documented testing.

---

# 2. Project Objectives

The primary objectives were:

- Perform reconnaissance against the authorized application.
- Identify exposed services and application technologies.
- Analyze HTTP requests, responses, and security headers.
- Inspect browser network activity and application resources.
- Analyze application traffic using Burp Suite.
- Perform automated security scanning using OWASP ZAP.
- Investigate potential information disclosure.
- Perform initial input-handling and XSS checks.
- Compare development-server behavior with production-preview behavior.
- Document security observations and recommend improvements.

---

# 3. Scope and Authorization

### Target Application

| Attribute | Details |
|---|---|
| Application | Planetorium |
| Framework | Vue.js |
| Development Server | Vite |
| Environment | Local authorized testing environment |
| Testing Access | Application owner authorization |

### Scope of Testing

The assessment included:

- Network and service reconnaissance
- Technology fingerprinting
- HTTP request and response inspection
- Browser-based network analysis
- JavaScript and Vue resource inspection
- WebSocket traffic inspection
- Security-header assessment
- Development-resource accessibility checks
- Initial XSS checks
- Production build and preview assessment

### Out of Scope / Not Applicable

Based on the functionality and architecture identified during inspection:

- Authentication testing: No application authentication functionality identified.
- Authorization testing: No protected application functionality or access-control mechanisms identified.
- API security testing: No conventional backend API communication identified in the inspected source.
- CSRF testing: No relevant state-changing application functionality identified.

These assessments were considered not applicable to the functionality identified during testing. This does not establish that every possible related vulnerability has been ruled out.

---

# 4. Testing Methodology

The assessment followed a structured testing workflow:

1. Reconnaissance
2. Technology identification
3. HTTP and browser analysis
4. Manual application traffic inspection
5. Automated vulnerability scanning
6. Manual alert validation
7. Endpoint and source discovery
8. Initial input-validation checks
9. Production-build assessment
10. Findings documentation
11. Remediation recommendations

Automated scanner alerts were reviewed before being classified. Observations were not automatically treated as exploitable vulnerabilities.

---

# 5. Tools and Technologies

| Tool | Purpose |
|---|---|
| Nmap | Network reconnaissance and service identification |
| WhatWeb | Web technology fingerprinting |
| Wappalyzer | Application technology identification |
| curl | HTTP request and response analysis |
| Firefox Developer Tools | Browser network and resource inspection |
| Burp Suite | HTTP interception, Repeater, Site Map and WebSocket analysis |
| OWASP ZAP | Passive scanning, active scanning and alert analysis |
| PowerShell | Application source inspection |
| Git and GitHub | Evidence management and project documentation |

---

# 6. Reconnaissance Results

## 6.1 Nmap

Nmap was used to identify accessible ports and services.

Testing included:

- Basic port scanning
- Service-version detection
- Selected port scanning
- Port-range inspection
- Default script scanning

The application was identified as running through a Vite development server on port 5173 during initial testing.

Additional Windows services were observed during network reconnaissance.

**Assessment:** Reconnaissance identified the accessible application service and provided initial information about the testing environment.

## 6.2 WhatWeb

WhatWeb was used to identify application technologies and HTTP characteristics.

Observed information included:

- HTTP response status
- HTML5
- Application title: Planetorium
- JavaScript-related characteristics

## 6.3 Wappalyzer

Wappalyzer identified Vue.js as the application's JavaScript framework.

**Reconnaissance status: Completed.**

---

# 7. HTTP and Browser Security Analysis

## 7.1 HTTP Analysis

curl was used to inspect HTTP responses, headers, and supported request methods.

Key observations:

- Main application response returned `HTTP 200 OK`.
- Content type was `text/html`.
- `Cache-Control: no-cache` was present.
- `ETag` was present.
- `Vary: Origin` was observed.
- OPTIONS requests returned `204 No Content`.
- The OPTIONS response advertised supported HTTP methods.

The tested preflight response did not explicitly allow the supplied origin.

The advertised HTTP methods alone did not establish a vulnerability.

## 7.2 Browser Developer Tools

Firefox Developer Tools were used to inspect:

- Network requests
- HTTP response headers
- JavaScript resources
- Application initialization
- Vue/Vite development resources
- Browser storage

HTTP responses including `200`, `304`, and `101` were observed.

The `101 Switching Protocols` response corresponded to the Vite development WebSocket connection.

## 7.3 Development Resource Inspection

The following resources were inspected:

- `/src/main.js`
- `/src/App.vue`
- `/package.json`
- `/vite.config.js`
- `/src/components/`

The development server returned transformed Vue source code and development configuration information.

Some responses included local filesystem path metadata and inline source-map data.

The `/src/components/` request returned the application HTML page rather than a directory listing.

A request to `/.env` also returned HTML, which did not establish that an environment file was exposed.

**Assessment:** Development-resource exposure was observed. Its production impact required separate verification.

---

# 8. Burp Suite Assessment

Burp Suite was used to inspect application traffic and manually analyze HTTP behavior.

Testing included:

- Proxy HTTP History
- Repeater
- Site Map
- HEAD request testing
- OPTIONS request testing
- POST request testing
- HTTP response header inspection
- WebSocket history
- WebSocket handshake inspection

### Key Observations

- Main application requests were captured successfully.
- The HEAD request returned `200 OK`.
- The OPTIONS request returned `204 No Content`.
- A POST request to the tested root path returned `404 Not Found`.
- The Site Map identified Vue/Vite development resources.
- The WebSocket handshake returned `101 Switching Protocols`.
- The WebSocket protocol identified Vite HMR communication.

The observed HMR token was not established as an application authentication or session token.

**Assessment:** Burp Suite testing provided useful visibility into HTTP and WebSocket behavior. No confirmed exploitable vulnerability was established through these checks.

---

# 9. OWASP ZAP Assessment

OWASP ZAP 2.17.0 was used for passive and active security analysis.

### Passive Scan Findings

| Finding | ZAP Risk | Assessment |
|---|---|---|
| Content Security Policy Header Not Set | Medium | Security hardening observation |
| Missing Anti-clickjacking Header | Medium | Security hardening observation |
| X-Content-Type-Options Header Missing | Low | Security hardening observation |
| Information in Browser localStorage | Informational | Not confirmed as a vulnerability |
| Sensitive Information in URL | Informational | Vite HMR token; not confirmed as sensitive application data |
| Suspicious Comments | Informational | No exposed security secrets confirmed |
| Modern Web Application | Informational | Technology/behavior observation |

### Active Scan

An initial default-policy scan was stopped because of its resource-intensive nature.

A subsequent scan used the lighter Dev Standard policy.

| Metric | Result |
|---|---|
| Scan completion | 100% |
| Requests generated | 4,970 |
| Current scans | 0 |
| New active-scan alerts | 0 |

No additional active-scan alerts were identified during the completed Dev Standard scan.

### Manual Validation

Several scanner alerts were manually reviewed.

**Base64 WebSocket message:** Decoded content was associated with a Vite development/import-analysis error message, not confirmed credentials or application secrets.

**Email false positive:** The detected pattern `@keyframes` was CSS syntax, not an email address.

**Sensitive URL:** The detected token parameter was associated with the Vite HMR WebSocket mechanism.

**Suspicious comments:** Reviewed comments were not demonstrated to contain credentials or security secrets.

**Local Storage:** Observed entries were consistent with Vue DevTools interface preferences.

**Assessment:** Scanner alerts were reviewed, and unverified informational observations were not classified as confirmed vulnerabilities.

---

# 10. Production Build Security Assessment

A separate production build was generated using:

`npm run build`

### Build Results

| Metric | Result |
|---|---|
| Build tool | Vite 8.2.1 |
| Modules transformed | 25 |
| Build time | 403 ms |
| JavaScript bundle | 125.24 KB |
| CSS bundle | 16.65 KB |
| Build status | Successful |

The generated production files were stored in the `dist/` directory.

## 10.1 Source Map Inspection

The production build was inspected for standalone `.map` files.

**Result:** No standalone source-map files were found.

The production JavaScript bundle was also searched for selected patterns associated with development resources, local filesystem paths, source-map references, and Vite HMR.

No matching results were returned by the search.

## 10.2 Production HTML Inspection

The generated `dist/index.html` referenced bundled JavaScript and CSS assets.

The inspected HTML did not contain a Vite development-client reference or individual Vue source paths.

## 10.3 Production Development-Resource Accessibility

The production preview was tested for selected development resources and project files.

| Endpoint | Response | Assessment |
|---|---|---|
| `/@vite/client` | HTML fallback | Development client not confirmed exposed |
| `/src/App.vue` | HTML fallback | Vue source not exposed through this request |
| `/src/main.js` | HTML fallback | Source not exposed through this request |
| `/vite.config.js` | HTML fallback | Configuration not exposed through this request |
| `/package.json` | HTML fallback | Package file not exposed through this request |
| `/.env` | HTML fallback | Environment file not confirmed exposed |

Manual inspection of `/src/App.vue` confirmed that the response contained the normal Planetorium HTML document rather than Vue component source code.

**Assessment:** Development-resource exposure observed on the development server was not reproduced through the selected production-preview checks.

This conclusion is limited to the tested paths and the local Vite preview environment.

---

# 11. Security Header Assessment

Three missing security headers were identified during passive scanning and manual HTTP inspection.

They were subsequently checked against the production-preview response.

| Security Header | Development Server | Production Preview |
|---|---|---|
| Content-Security-Policy | Missing | Missing |
| X-Frame-Options | Missing | Missing |
| X-Content-Type-Options | Missing | Missing |

Additional headers checked:

| Header | Production Preview |
|---|---|
| Strict-Transport-Security | Not observed |
| Referrer-Policy | Not observed |
| Permissions-Policy | Not observed |

The production-preview response returned `HTTP 200 OK`.

**Assessment:** Three security hardening observations were confirmed in the inspected production-preview response.

The Vite preview server does not necessarily represent the final hosting configuration. Header behavior should be assessed again if the application is deployed behind a different production server or hosting platform.

HSTS should be evaluated on the actual HTTPS deployment.

---

# 12. XSS and Input Validation Checks

Initial XSS checks included:

- Query parameter reflection inspection.
- Browser URL parameter reading.
- Source-code search for `innerHTML`.
- Filtering of identified framework and dependency paths.

### Results

- The test query parameter was not observed in the initial HTML response.
- Reading the query parameter through the browser URL API worked normally.
- No application-owned `innerHTML` matches were visible in the filtered search.
- Remaining visible matches were associated with Vue DevTools and installed dependencies.

**Assessment:** No XSS vulnerability was confirmed during limited testing.

These checks do not establish that the application is completely free from XSS vulnerabilities.

---

# 13. Consolidated Security Findings

| ID | Finding | Assessment | Status |
|---|---|---|---|
| F-01 | Content Security Policy Missing | Security hardening observation | Observed in production preview |
| F-02 | Anti-clickjacking Protection Missing | Security hardening observation | Observed in production preview |
| F-03 | X-Content-Type-Options Missing | Security hardening observation | Observed in production preview |
| F-04 | Development Resource and Source Metadata Exposure | Development-environment observation | Not reproduced in tested production-preview paths |
| F-05 | Cross-Site Scripting | No vulnerability confirmed | Limited checks completed |
| F-06 | Authentication and Session Management | Not applicable to identified functionality | No authentication functionality identified |

### Severity Considerations

The three missing security headers are configuration weaknesses requiring deployment-context assessment.

No confirmed exploitable application vulnerability was established through the documented tests.

Final severity classification depends on the actual deployment environment, application behavior, and demonstrated security impact.

---

# 14. Remediation Recommendations

The following recommendations were documented:

1. Configure an appropriate Content Security Policy.
2. Implement suitable anti-clickjacking protection using CSP `frame-ancestors` or `X-Frame-Options`.
3. Configure `X-Content-Type-Options: nosniff`.
4. Avoid exposing development servers to untrusted users.
5. Use production build artifacts for deployment.
6. Review source-map publication settings.
7. Keep environment files and secrets outside publicly accessible deployment artifacts.
8. Use HTTPS for the actual production deployment.
9. Evaluate additional browser security headers according to application requirements.

**Implementation status:** Recommendations documented only. Application-side fixes were not implemented or verified as part of this assessment.

---

# 15. Testing Limitations

- Testing was performed against an authorized local environment.
- The development server and Vite production preview were tested separately.
- The production preview may differ from the final hosting configuration.
- Automated scanners cannot identify every application-specific vulnerability.
- XSS testing was limited.
- No authentication or authorization functionality was identified.
- No conventional backend API communication was identified in the inspected project source.
- CSRF testing was considered not applicable to the identified functionality.
- No comprehensive business-logic assessment was performed.
- Remediation was not implemented.
- Retesting after remediation was not performed.

---

# 16. Final Conclusion

The Planetorium web application underwent a structured security assessment involving reconnaissance, HTTP analysis, browser inspection, Burp Suite testing, OWASP ZAP scanning, and production-build verification.

The assessment identified missing browser security headers and development-resource exposure in the development environment.

Production-preview testing showed that the selected development resources were not exposed through the tested paths. However, the three missing security headers were also observed in the Vite production-preview response.

Automated scanner alerts were manually reviewed, and false positives and informational observations were distinguished from security findings.

No confirmed exploitable application vulnerability was established through the documented testing.

The project produced:

- Reconnaissance evidence
- HTTP and browser testing documentation
- Burp Suite testing documentation
- OWASP ZAP testing documentation
- Consolidated security findings
- Remediation recommendations
- Production-build assessment
- Final penetration testing report

The assessment provides a documented view of the application's observed security behavior and identifies improvements for the application owner to consider before deployment.

**Overall Project Status: Assessment and Documentation Completed**

**Remediation and post-remediation retesting: Not performed.**

---

# 17. Project Repository

**GitHub Repository:**  
https://github.com/Pranay-Pandurang-Patil/web-application-penetration-testing

The repository contains testing methodology, tool-specific documentation, sanitized screenshots, security findings, remediation recommendations, and this final report.

---

**End of Report**