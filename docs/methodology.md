# Penetration Testing Methodology

## Approach

The assessment followed a structured workflow for the authorized security testing of the Planetorium web application.

The primary workflow was:

**Learn → Test → Capture Evidence → Verify → Assess Impact → Document Findings**

Potential vulnerabilities identified by automated tools were manually reviewed before being classified as security findings.

The assessment covered the local Vue/Vite development environment and a locally hosted production preview.

## Phase 1 — Reconnaissance

**Objective:** Understand the application's technologies, structure, network exposure, and client-side behavior.

**Tools:**
- Nmap
- WhatWeb
- Wappalyzer
- Browser Developer Tools
- curl

**Activities:**
- Inspect network requests.
- Analyze HTTP responses and headers.
- Identify technologies and frameworks.
- Identify exposed services and ports.
- Discover application resources.
- Inspect client-side behavior.

## Phase 2 — HTTP and Web Analysis

**Objective:** Understand how the application communicates over HTTP.

**Activities:**
- Request and response analysis.
- HTTP method testing.
- Header analysis.
- Cookie inspection.
- Browser storage analysis.
- CORS behavior checks.
- Client-side resource analysis.
- Development-resource inspection.

## Phase 3 — Manual Web Security Testing

**Objective:** Identify potential security weaknesses through controlled manual testing.

**Areas considered:**
- Authentication.
- Authorization.
- Session management.
- Input validation.
- Cross-Site Scripting (XSS).
- CSRF.
- API security.
- Information disclosure.

Testing depended on the functionality actually present in the application.

No application authentication functionality or conventional API communication was identified during the documented initial source inspection. These areas were therefore treated as not applicable to the identified application functionality.

Limited XSS checks were performed. No XSS vulnerability was confirmed.

## Phase 4 — Burp Suite Testing

**Objective:** Inspect and analyze application HTTP and WebSocket communication.

**Tool:**
- Burp Suite

**Activities:**
- HTTP history inspection.
- Request and response analysis.
- Repeater testing.
- HTTP method testing.
- Site map inspection.
- WebSocket history and handshake analysis.
- Response-header inspection.

## Phase 5 — OWASP ZAP Testing

**Objective:** Identify potential security configuration issues and other scanner-detected observations.

**Tool:**
- OWASP ZAP

**Activities:**
- Passive scanning.
- Alert inspection.
- Manual alert validation.
- Active scanning using the Dev Standard policy.
- Final alert review.

Automated findings were not automatically considered confirmed vulnerabilities. False positives and informational alerts were assessed separately.

## Phase 6 — Production Build and Preview Assessment

**Objective:** Compare selected development-environment observations against a locally hosted production build.

**Activities:**
- Generate the production build.
- Inspect generated build files.
- Check for source-map files.
- Inspect production HTML and bundled resources.
- Start the local production preview.
- Check selected development-related paths.
- Inspect production-preview HTTP response headers.

The production preview was used for local validation. It was not treated as a substitute for testing the final public hosting environment.

## Phase 7 — Finding Validation and Reporting

For each potential finding:

1. Identify the observed behavior.
2. Review the available evidence.
3. Determine whether the observation is reproducible.
4. Assess potential security impact.
5. Distinguish confirmed issues from informational alerts.
6. Document the result.
7. Recommend appropriate remediation where relevant.

## Remediation and Retesting Status

Remediation recommendations were documented in `remediation/fixes.md`.

No application-side remediation was implemented as part of this assessment.

Post-remediation retesting was not performed because no application-side fixes were applied.

If remediation is implemented in the future, the original checks should be repeated and the results documented separately.

## Evidence Collection

Evidence collected during the assessment included:

- Screenshots.
- HTTP requests and responses.
- Command output.
- Browser Developer Tools observations.
- Burp Suite results.
- OWASP ZAP alerts.
- Production-build and preview observations.

Sensitive information was reviewed and redacted before publication.

## Reporting

The final report documents:

- Assessment objective and scope.
- Authorization.
- Methodology.
- Tools used.
- Testing activities.
- Observations and findings.
- Evidence.
- Security impact.
- Remediation recommendations.
- Assessment limitations.
- Remediation and retesting status.