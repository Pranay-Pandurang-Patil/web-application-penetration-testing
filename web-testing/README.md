# Web Application Testing

Manual HTTP and browser-based security analysis of the authorized Planetorium web application.

## Scope

Testing focused on HTTP behavior, response headers, browser network activity, JavaScript resources, and Vue/Vite development-server behavior.

## HTTP Analysis

### curl

Used `curl` to inspect HTTP responses, headers, and supported request methods.

Tests performed:

- HTTP response analysis
- GET request analysis
- Verbose HTTP request/response inspection
- Response header inspection
- OPTIONS request analysis
- Selected resource accessibility checks

Key observations:

- The application returned `HTTP 200 OK` for the main page.
- The response used `Content-Type: text/html`.
- `Cache-Control: no-cache` and `ETag` headers were present.
- `Vary: Origin` was present.
- An OPTIONS request returned `204 No Content`.
- The OPTIONS response advertised supported HTTP methods.
- The tested preflight response did not explicitly allow the supplied origin.

The OPTIONS response alone does not establish a CORS vulnerability.

## Browser DevTools Analysis

Firefox Developer Tools were used to inspect:

- Network requests and response status codes
- HTTP request and response headers
- JavaScript resources
- Vue and Vite development resources
- Application initialization
- Browser requests made while loading the application

The observed resources were consistent with a Vue application served through Vite development tooling.

## Vue/Vite Source Inspection

Selected application resources were inspected to understand the exposed development environment.

Observations:

- `/src/main.js` returned JavaScript.
- `/src/App.vue` returned transformed Vue component code.
- `/package.json` returned application dependency and script metadata.
- `/vite.config.js` returned transformed Vite configuration code.
- The Vue response included local filesystem path metadata.
- The transformed resources contained inline source-map data.
- `/src/components/` returned the application HTML page rather than a directory listing.

These observations indicate development-resource exposure. They do not independently establish a production vulnerability.

## Security Header Assessment

The development-server HTTP response was checked for common security headers.

The observed response did not include:

- `Content-Security-Policy`
- `X-Frame-Options`
- `X-Content-Type-Options`

These observations are consistent with the findings identified during OWASP ZAP passive scanning.

Because the initial target was running through a Vite development server, the missing headers were initially documented as observations requiring deployment-context assessment.

### Production Preview Assessment

A production build was generated successfully using:

`npm run build`

The generated `dist/index.html` referenced bundled JavaScript and CSS assets. No standalone `.map` files were found in the production build.

The production preview was subsequently tested at the local authorized preview address.

Observations:

- The production preview returned `HTTP 200 OK`.
- The response did not include `Content-Security-Policy`.
- The response did not include `X-Frame-Options`.
- The response did not include `X-Content-Type-Options`.
- Selected development-resource paths returned the application's HTML fallback rather than the requested development files.
- No standalone source-map files were found.
- The production bundle search returned no matches for the checked development-client references, local filesystem path pattern, or source-map references.

The missing security headers were therefore also observed in the Vite production-preview response.

The production preview is not necessarily representative of the final hosting configuration. These observations should be reassessed if the application is deployed behind a different production web server or hosting platform.

## Evidence

### 1. Initial HTTP Response

![Initial HTTP response](screenshots/01-curl-initial-response.png)

### 2. Verbose HTTP Analysis

![Verbose HTTP request and response](screenshots/02-curl-verbose-request-response.png)

### 3. Browser Network Analysis

![Browser Network analysis](screenshots/03-devtools-network-analysis.png)

### 4. HTTP Headers in Browser DevTools

![HTTP request and response headers](screenshots/04-devtools-http-headers.png)

### 5. Vue Source Metadata

![Vue source metadata disclosure](screenshots/05-vue-source-metadata-disclosure.png)

### 6. Vue Source Path Confirmation

![Vue source path confirmation](screenshots/06-vue-source-path-confirmation.png)

### 7. Vue Component Discovery

![Vue component discovery](screenshots/07-vue-component-discovery.png)

### 8. Components Directory Check

![Components directory check](screenshots/08-vue-components-directory-check.png)

### 9. Browser Network and Session Analysis

![Browser network and session analysis](screenshots/09-browser-network-session-analysis.png)

**Observations:**

- Vue/Vite development resources loaded successfully in the displayed requests.
- HTTP `200`, `304`, and `101` responses were observed.
- The `101` response corresponded to the Vite development WebSocket connection.
- No application authentication or login functionality was identified.
- Cookies, Session Storage, and IndexedDB contained no entries during inspection.
- Local Storage contained Vue DevTools interface preferences.

**Assessment:** No authentication or session-management vulnerability was established by these checks. The observed Local Storage entries were development-tool preferences, not identified authentication credentials.

### 10. Production Security Header Assessment

![Production security header assessment](screenshots/12-production-security-headers.png)

**Observations:**

- The production preview returned `HTTP 200 OK`.
- The inspected response did not include `Content-Security-Policy`.
- `X-Frame-Options` was absent.
- `X-Content-Type-Options` was absent.

**Assessment:** The three missing security headers were confirmed in the inspected Vite production-preview response. Their final severity depends on the actual deployment configuration and application requirements.

## Assessment Notes

- The target was tested with authorization from its owner.
- Initial testing was performed against a Vite development environment.
- A separate production build and local preview were subsequently tested.
- Development-server behavior must be distinguished from production behavior.
- HTTP status codes and exposed resources are not automatically vulnerabilities.
- CORS behavior must be evaluated using the complete response and browser-enforced policy.
- Potential findings require manual verification and impact assessment.
- The production preview does not necessarily represent the final hosting configuration.
- Screenshots and published evidence must not expose private filesystem paths, usernames, tokens, or other sensitive information.

## Status

- [x] HTTP response analysis
- [x] GET and OPTIONS request analysis
- [x] HTTP request and response header inspection
- [x] Browser Network analysis
- [x] JavaScript resource identification
- [x] Vue/Vite development-resource inspection
- [x] Selected endpoint accessibility checks
- [x] Initial security header assessment
- [x] Production build generation
- [x] Production preview assessment
- [x] Production development-resource accessibility checks
- [x] Evidence collection

**Phase status:** Completed — HTTP, browser, and production-preview analysis.

**Note:** This phase does not represent completion of the overall penetration test. Authentication, authorization, input validation, vulnerability validation, remediation, and retesting are tracked separately.