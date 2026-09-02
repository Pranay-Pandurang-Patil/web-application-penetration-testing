# Assessment Scope

## Target

**Application:** Planetorium Web Application

**Target Environment:** Authorized local development deployment

**Target Address:** `TARGET_IP:5173`

## Authorization

Testing is performed with explicit permission from the application owner.

The assessment is limited to the Planetorium application and the authorized local testing environment.

## In Scope

The following areas are within scope:

- Web application functionality
- HTTP requests and responses
- Client-side JavaScript
- Application routes and endpoints
- HTTP methods
- Security headers
- Cookies and browser storage
- Authentication, if implemented
- Authorization, if implemented
- Session management, if implemented
- API endpoints, if implemented
- Input validation
- Client-side security
- Business logic
- Information disclosure
- Network services relevant to the testing environment
- Security configuration

## Out of Scope

The following are outside the assessment scope:

- Third-party applications and services
- Other systems on the network
- Netlify infrastructure
- Other users' systems or data
- Denial-of-service testing
- Destructive testing
- Social engineering
- Physical security
- Attacks against systems without authorization

## Testing Approach

Testing will be performed using a combination of:

- Manual testing
- Browser-based analysis
- Command-line security tools
- Automated vulnerability scanners
- HTTP request manipulation
- Fuzzing where appropriate
- Manual verification of potential vulnerabilities

## Evidence Handling

Testing evidence will be collected during the assessment.

Screenshots and command output will be reviewed and sanitized before being published to the public repository.

Sensitive information will not be published.