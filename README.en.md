# 🔒 API Security Checklist

> **A comprehensive and practical checklist** of the most important tasks that must be performed to maintain security during API design, development, testing, and deployment.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Contributions Welcome](https://img.shields.io/badge/Contributions-Welcome-brightgreen.svg)](./CONTRIBUTING.md)

## 🌍 Languages / اللغة

- 🇮🇷 [فارسی (Persian)](./Readme.md)
- 🇬🇧 [English](./README.en.md) - Current version
- 🇸🇦 [العربية (Arabic)](./README.ar.md)

---

## 📖 About the Project

---

## 📖 About the Project

This project is a **comprehensive and practical checklist** for API security that helps developers design, develop, and deploy their APIs securely.

### ✨ Features

- 🔒 **Comprehensive Coverage**: Includes all aspects of API security from authentication to CI/CD
- 📝 **Practical and Usable**: Each item includes explanations and practical examples
- 🎯 **Well-Organized**: Categorized by security domains
- 🔄 **Continuously Updated**: Keeps pace with the latest standards and best practices
- 🌍 **Multi-language Support**: Available in Persian, English, and Arabic

### 🎯 Who is it for?

- Backend developers building APIs
- DevOps and Security teams
- Software architects and Technical Leads
- Students and security enthusiasts

### 🚀 Quick Start

1. Star this repository to stay updated
2. Review the checklist before each deployment
3. Check off items relevant to your project
4. Submit a Pull Request if needed

---

## 📋 Table of Contents

- [How to Use](#-how-to-use)
- [Quick Checklist](#-quick-checklist)
- [🔐 Authentication & Authorization](#-authentication--authorization)
- [🌐 Access & Network](#-access--network)
- [📥 Input & Validation](#-input--validation)
- [⚙️ Processing & Business Logic](#️-processing--business-logic)
- [📤 Output & Responses](#-output--responses)
- [💾 Storage & Data](#-storage--data)
- [📊 Monitoring, Observability & Response](#-monitoring-observability--response)
- [🔧 Error Handling & Versioning](#-error-handling--versioning)
- [🔗 CORS & Browser Security](#-cors--browser-security)
- [🔑 Key & Secrets Management](#-key--secrets-management)
- [🌍 Webhooks & Event Security](#-webhooks--event-security)
- [📚 Documentation & API Documentation](#-documentation--api-documentation)
- [✅ Security Testing](#-security-testing)
- [🚀 CI & CD](#-ci--cd)
- [📖 Additional Resources](#-additional-resources)
- [🤝 Contributing](#-contributing)

---

## 📖 How to Use

This checklist acts as a **Security Kanban board**:

- ✅ Before each release, review each category and check off items to clarify security status
- 📝 If an item doesn't apply to your project, document the reason for non-compliance so it's defensible in future audits
- 🎯 Move items that need action to the backlog and assign owners/deadlines
- 🔄 Periodically review and update this document (e.g., quarterly)

---

## ⚡ Quick Checklist

These items **must** be reviewed before each production deployment:

- [ ] 🔴 **Environments are separated**: Dev/Test and Prod are completely separated (environment, data, accounts)
- [ ] 🔴 **Secrets are secure**: Secrets are not stored in code, email, or messaging apps, and have periodic rotation
- [ ] 🔴 **Rate Limiting is active**: No endpoint is public without Rate Limit
- [ ] 🔴 **Monitoring is active**: Errors and security events are monitored with actionable alerts
- [ ] 🔴 **HTTPS is mandatory**: All traffic goes through HTTPS and HTTP redirects
- [ ] 🔴 **Authentication is protected**: All sensitive endpoints are protected with authentication

---

## 🔐 Authentication & Authorization

### General Authentication

- [ ] Don't use `Basic Auth`. Instead, use standard authentication methods ([JWT](https://jwt.io/), [OAuth 2.0](https://oauth.net/2/), or [OpenID Connect](https://openid.net/connect/))
- [ ] Don't reinvent the wheel for `authentication`, `token generation`, and `password storage`. Use standard, tested libraries
- [ ] Implement `maximum retry attempts` limits for login (e.g., 5 attempts in 15 minutes) and login frequency limits
- [ ] Encrypt all sensitive data (in transit and at rest)
- [ ] Use Multi-Factor Authentication (MFA/2FA) for sensitive accounts
- [ ] Define appropriate session timeout (e.g., 30 minutes of inactivity)
- [ ] Invalidate all previous sessions after password change
- [ ] Use strong password policy (minimum 12 characters, combination of uppercase/lowercase, numbers, special characters)

### JWT (JSON Web Token)

- [ ] Use a complex random key for `JWT Secret` (minimum 256 bits) to make brute force attacks on tokens very difficult
- [ ] Don't extract the algorithm from the header. Enforce the algorithm in the backend (`HS256` or `RS256`)
- [ ] Keep token expiration (`TTL` or `RTTL`) as short as possible (e.g., 15 minutes for Access Token, 7 days for Refresh Token)
- [ ] Don't store sensitive information in JWT payload as it's [easily](https://jwt.io/#debugger-io) decodable
- [ ] Avoid storing excessive data. JWTs are usually shared in headers and have size limitations
- [ ] Use Refresh Token to renew Access Token
- [ ] Implement Token Revocation (Blacklist or Database lookup)
- [ ] Use `jti` (JWT ID) to prevent Replay Attack

### OAuth 2.0 / OpenID Connect

- [ ] Always validate `redirect_uri` on the server side to only allow authorized URLs
- [ ] Always try to exchange code instead of token (don't allow `response_type=token`)
- [ ] Use `state` parameter with a random hash to prevent CSRF on OAuth authentication process
- [ ] Define default scope value and validate scope parameters for each application
- [ ] Use PKCE (Proof Key for Code Exchange) for Public Clients
- [ ] Don't use Client Secret in Public Clients
- [ ] Make Authorization Code single-use

### Authorization

- [ ] Use Principle of Least Privilege (minimum necessary access)
- [ ] Implement Role-Based Access Control (RBAC) or Attribute-Based Access Control (ABAC)
- [ ] Check permissions at Resource and Action level (not just at Endpoint level)
- [ ] Avoid using user resource IDs. Use `/me/orders` instead of `user/654321/orders`
- [ ] Check Ownership and Permission in every request

---

## 🌐 Access & Network

### Network Security

- [ ] Use HTTPS on the server side to prevent Man-in-the-Middle (MITM) attacks
- [ ] Use `HSTS` (Strict-Transport-Security) header to prevent SSL Strip attacks
  ```http
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  ```
- [ ] Redirect HTTP to HTTPS (301 Permanent Redirect)
- [ ] Use TLS 1.2 or higher (TLS 1.3 recommended)
- [ ] Consider Certificate Pinning for mobile clients
- [ ] Disable directory listings
- [ ] For private APIs, only allow access from whitelisted IPs/hosts
- [ ] Use VPN or Private Network for internal API access

### Rate Limiting & Throttling

- [ ] Limit requests (Throttling) to prevent DDoS or brute force attacks
- [ ] Define Rate Limit at multiple levels:
  - **User Level**: e.g., 1000 requests/hour per user
  - **IP Level**: e.g., 100 requests/minute per IP
  - **Endpoint Level**: e.g., 10 requests/second for sensitive endpoints
- [ ] Use an API Gateway service to enable caching and Rate Limit policies (e.g., `Quota`, `Spike Arrest`, or `Concurrent Rate Limit`)
- [ ] Inform about Rate Limit with standard headers:
  ```http
  X-RateLimit-Limit: 1000
  X-RateLimit-Remaining: 999
  X-RateLimit-Reset: 1609459200
  ```
- [ ] Use Distributed Cache for Rate Limit (Redis, Memcached)
- [ ] Configure Rate Limit independently for different endpoints

---

## 📥 Input & Validation

### HTTP Methods & Content Negotiation

- [ ] Use appropriate HTTP method for the operation type:
  - `GET` for reading (Idempotent)
  - `POST` for creating
  - `PUT` for complete replacement (Idempotent)
  - `PATCH` for partial update
  - `DELETE` for deleting a record (Idempotent)
- [ ] If the requested method is not appropriate for the requested resource, respond with `405 Method Not Allowed`
- [ ] Validate `content-type` in Accept header (Content Negotiation) to only allow supported formats (e.g., `application/json`, `application/xml`)
- [ ] Respond with `406 Not Acceptable` if there's no match
- [ ] Validate `content-type` in posted data (e.g., `application/x-www-form-urlencoded`, `multipart/form-data`, `application/json`)

### Input Validation

- [ ] **Always** validate user input (Whitelist approach is better than Blacklist)
- [ ] Perform validation on the **server side** (not just on Client)
- [ ] Use Schema Validation (e.g., JSON Schema, OpenAPI Schema)
- [ ] Limit string length (e.g., username maximum 50 characters)
- [ ] Check data type (String, Number, Boolean, Array, Object)
- [ ] Check value ranges (e.g., age between 0 and 150)
- [ ] Check data format (e.g., Email, Phone Number, URL)
- [ ] Use Regular Expression for complex validation (but beware of ReDoS)

### Preventing Common Vulnerabilities

#### SQL Injection

- [ ] Use Parameterized Queries or Prepared Statements (never String Concatenation)
- [ ] Use ORM that automatically creates Parameterized Queries
- [ ] Use Stored Procedures with caution (if used, they should be Parameterized)
- [ ] Create Database User with minimum necessary access (not root/admin)
- [ ] Use Database Firewall or WAF

#### XSS (Cross-Site Scripting)

- [ ] Escape all user inputs before displaying in HTML
- [ ] Use Content Security Policy (CSP)
- [ ] Use Template Engine that automatically escapes
- [ ] Don't use `innerHTML` in JavaScript (use `textContent`)
- [ ] Use `Content-Type: application/json` for JSON Response

#### Command Injection

- [ ] Avoid direct Command Line execution
- [ ] If necessary, use Whitelist for Commands
- [ ] Escape all parameters
- [ ] Use secure libraries for Command execution

#### Path Traversal

- [ ] Use Absolute Path
- [ ] Prevent `..` and `/` in file names
- [ ] Use Whitelist for file names
- [ ] Use secure libraries for Path Manipulation

### Sensitive Data in URL

- [ ] Don't put sensitive data (authentication data, passwords, security tokens, or API keys) in URL
- [ ] Use standard Authorization header
- [ ] Don't use Query Parameter for sensitive data (stored in Logs)
- [ ] Only use server-side encryption (Client-side encryption is only for Privacy, not Security)

---

## ⚙️ Processing & Business Logic

### Business Logic Security

- [ ] Check that all endpoints are protected by authentication to prevent incomplete authentication process
- [ ] Don't use auto-increment IDs. Use `UUID` or `ULID` instead
- [ ] Use Business Logic Validation (e.g., check inventory before purchase)
- [ ] Use Idempotency Key for sensitive operations (e.g., payment)
- [ ] Consider Race Condition (use Lock or Transaction)
- [ ] Prevent Mass Assignment (only accept allowed fields)

### File Processing

- [ ] If parsing XML files, ensure entity parsing is disabled to prevent `XXE` (XML External entity attack)
- [ ] If parsing XML, YAML, or any other language using anchors and refs, ensure entity expansion is disabled to prevent `Billion Laughs/XML bomb` through exponential entity expansion attack
- [ ] Check file type from Content (not just from Extension)
- [ ] Limit file size
- [ ] Store uploaded files in a separate directory with limited access
- [ ] Use a CDN for file uploads
- [ ] Scan uploaded files (Antivirus)
- [ ] Use Sandbox for processing suspicious files

### Performance & Scalability

- [ ] If dealing with very large amounts of data, use Workers and Queues to process in the background as much as possible and return response quickly to prevent HTTP Blocking
- [ ] Use Pagination for large lists
- [ ] Use Caching for static data
- [ ] Use Database Indexing for frequent queries
- [ ] Define appropriate Timeout for Requests

### Configuration

- [ ] Don't forget to disable DEBUG mode
- [ ] Use non-executable stacks if available (Non-executable Stack)
- [ ] Use Environment Variables for Configuration (not Hard-code)
- [ ] Use Configuration Management Tools (e.g., Vault, AWS Secrets Manager)

---

## 📤 Output & Responses

### Security Headers

- [ ] Send `X-Content-Type-Options: nosniff` header to prevent MIME Type Sniffing
- [ ] Send `X-Frame-Options: deny` or `SAMEORIGIN` header to prevent Clickjacking
- [ ] Send `Content-Security-Policy` header:
  ```http
  Content-Security-Policy: default-src 'none'; script-src 'self'; style-src 'self' 'unsafe-inline'
  ```
- [ ] Send `X-XSS-Protection: 1; mode=block` header (for older browsers)
- [ ] Send `Referrer-Policy: strict-origin-when-cross-origin` header
- [ ] Send `Permissions-Policy` header to limit browser features

### Information Disclosure

- [ ] Remove headers that leave fingerprints, such as:
  - `X-Powered-By`
  - `Server`
  - `X-AspNet-Version`
  - `X-Runtime`
- [ ] Make `content-type` mandatory for responses. If returning `application/json`, then response `content-type` is `application/json`
- [ ] Don't return sensitive information like `authentication data`, `passwords`, and `security tokens`
- [ ] Don't display Stack Trace in Production
- [ ] Display Database Error Message generically

### Status Codes

- [ ] Return appropriate status code based on the operation performed:
  - `200 OK`: Request successful
  - `201 Created`: New resource created
  - `204 No Content`: Request successful but no content to return
  - `400 Bad Request`: Invalid request
  - `401 Unauthorized`: Authentication required
  - `403 Forbidden`: Access not allowed
  - `404 Not Found`: Resource not found
  - `405 Method Not Allowed`: HTTP method not allowed
  - `406 Not Acceptable`: Requested format not supported
  - `409 Conflict`: Conflict in resource state
  - `429 Too Many Requests`: Too many requests
  - `500 Internal Server Error`: Server error
  - `503 Service Unavailable`: Service temporarily unavailable

---

## 💾 Storage & Data

### Data Encryption

- [ ] Encrypt data at rest (Data at Rest) with AES-256 or equivalent and store keys in KMS
- [ ] Use Database Encryption (TDE - Transparent Data Encryption)
- [ ] Encrypt backups and limit their access; perform periodic restore tests
- [ ] Use Key Rotation (periodic key rotation)

### Sensitive Data Management

- [ ] Mask/tokenize sensitive data even in Log and Analytics environments
- [ ] Use Hashing for passwords (bcrypt, Argon2, scrypt) - **never** use MD5 or SHA1
- [ ] Use Salt for Hashing (Unique Salt for each password)
- [ ] Define Retention Policy and delete data that's not needed (Data Minimization)
- [ ] Use Data Anonymization for test data

### Database Security

- [ ] Create Database User with minimum necessary access
- [ ] Use Connection Pooling
- [ ] Keep Connection String in Environment Variables
- [ ] Use Database Firewall
- [ ] Use Database Audit Logging
- [ ] Update Database periodically

---

## 📊 Monitoring, Observability & Response

### Logging

- [ ] Log security events structurally (JSON Format, pattern, Correlation ID, User ID, etc.)
- [ ] Use appropriate Log Levels (DEBUG, INFO, WARN, ERROR)
- [ ] Disable sensitive logs in Production (e.g., Request Body)
- [ ] Use Centralized Logging (e.g., ELK Stack, Splunk)
- [ ] Define Log Retention Policy
- [ ] Use Log Rotation

### Monitoring & Alerting

- [ ] Create alerts for sensitive events:
  - Rate spike (sudden increase in requests)
  - Authentication errors (e.g., 5 times in 1 minute)
  - Security configuration changes
  - 5xx errors
  - High Response Time
- [ ] Use Distributed Tracing (e.g., Jaeger, Zipkin)
- [ ] Distributed trace for sensitive request paths should be active so complex attacks can be tracked
- [ ] Use Health Check Endpoint (`/health`, `/ready`)
- [ ] Use Metrics Collection (e.g., Prometheus)

### Incident Response

- [ ] Practice incident response scenarios (IR Playbook) with checklist
- [ ] Incident Response Team should be defined
- [ ] Use SIEM (Security Information and Event Management)
- [ ] Use Threat Intelligence

---

## 🔧 Error Handling & Versioning

### Error Handling

- [ ] Error messages should never display Stack Trace, Query, or Secret; only error code and trace ID
- [ ] Use unique Error Code for each error type
- [ ] Display Error Message generically (store details in Log)
- [ ] Use Structured Error Response:
  ```json
  {
    "error": {
      "code": "INVALID_INPUT",
      "message": "The provided input is invalid",
      "traceId": "abc123"
    }
  }
  ```

### API Versioning

- [ ] API Versioning should be clear (e.g., `/v1/users`, `/v2/users`)
- [ ] Use Header Versioning (e.g., `Accept: application/vnd.api+json;version=1`)
- [ ] Deprecated versions should have sunset schedule:
  ```http
  Sunset: Sat, 31 Dec 2024 23:59:59 GMT
  ```
- [ ] Document Breaking Changes in Changelog
- [ ] Use Deprecation Warning

### Schema Validation

- [ ] Schemas (OpenAPI/AsyncAPI) should be up-to-date and validated with Contract tests
- [ ] Use Schema Versioning
- [ ] Use Contract Testing (e.g., Pact)

---

## 🔗 CORS & Browser Security

### CORS (Cross-Origin Resource Sharing)

- [ ] Configure CORS correctly:
  ```http
  Access-Control-Allow-Origin: https://example.com
  Access-Control-Allow-Methods: GET, POST, PUT, DELETE
  Access-Control-Allow-Headers: Content-Type, Authorization
  Access-Control-Max-Age: 86400
  ```
- [ ] Don't use Wildcard (`*`) for `Access-Control-Allow-Origin` (except for Public APIs)
- [ ] Use Whitelist for Origin
- [ ] Use Credentials with caution (`Access-Control-Allow-Credentials: true`)
- [ ] Handle Preflight Request correctly

### CSRF Protection

- [ ] Use CSRF Token (for State-Changing Operations)
- [ ] Use SameSite Cookie:
  ```http
  Set-Cookie: session=abc123; SameSite=Strict; Secure; HttpOnly
  ```
- [ ] Use Origin/Referer Header for validation
- [ ] Use Double Submit Cookie Pattern

---

## 🔑 Key & Secrets Management

### Secrets Management

- [ ] Don't commit Secrets to Version Control (Git)
- [ ] Use Secret Management Tools (e.g., HashiCorp Vault, AWS Secrets Manager, Azure Key Vault)
- [ ] Keep Secrets in Environment Variables (not in code)
- [ ] Use Secret Rotation (periodic rotation)
- [ ] Use Least Privilege for Secret access
- [ ] Use Secret Scanning Tools (e.g., GitGuardian, TruffleHog)

### API Keys

- [ ] Generate API Keys securely (Cryptographically Secure Random)
- [ ] Hash API Keys before storing in Database
- [ ] Use API Key Prefix (e.g., `sk_live_...`, `sk_test_...`)
- [ ] Define API Key Expiration
- [ ] Use API Key Scoping (limit access)
- [ ] Implement API Key Revocation

---

## 🌍 Webhooks & Event Security

### Webhook Security

- [ ] Use Webhook Signature (e.g., HMAC-SHA256)
- [ ] Use Webhook Secret (shared between you and Client)
- [ ] Use Idempotency Key for Webhook
- [ ] Use Retry Mechanism for Webhook (Exponential Backoff)
- [ ] Use Webhook Timeout
- [ ] Don't Log Webhook Payload (or mask it)

### Event Security

- [ ] Use Event Sourcing with caution (ensure Integrity)
- [ ] Use Message Queue Encryption
- [ ] Use Message Authentication

---

## 📚 Documentation & API Documentation

### API Documentation

- [ ] API Documentation should be complete and up-to-date (e.g., OpenAPI/Swagger)
- [ ] Request/Response examples should be in Documentation
- [ ] Authentication Method should be explained in Documentation
- [ ] Error Codes and Messages should be in Documentation
- [ ] Rate Limits should be mentioned in Documentation
- [ ] Use Interactive API Documentation (e.g., Swagger UI)

### Security Documentation

- [ ] Security Best Practices should be in Documentation
- [ ] Use Security.txt:
  ```
  https://example.com/.well-known/security.txt
  ```
- [ ] Contact Information for Security Issues should be in Documentation

---

## ✅ Security Testing

### Security Testing

- [ ] Write Unit/Integration tests for Security Features
- [ ] Use Static Application Security Testing (SAST) (e.g., SonarQube, Checkmarx)
- [ ] Use Dynamic Application Security Testing (DAST) (e.g., OWASP ZAP, Burp Suite)
- [ ] Use Dependency Scanning (e.g., Snyk, Dependabot)
- [ ] Use Penetration Testing (periodically)
- [ ] Use Security Code Review

### Vulnerability Scanning

- [ ] Use Container Scanning (e.g., Trivy, Clair)
- [ ] Use Infrastructure Scanning
- [ ] Use Network Scanning

---

## 🚀 CI & CD

### Continuous Integration

- [ ] Review your design and implementation with unit/integration test coverage
- [ ] Use a code review process and ignore self-approval
- [ ] Ensure all components of your services, including used libraries and other dependencies, are statically scanned by antivirus software before production release
- [ ] Continuously run security tests (static and dynamic analysis) on your code
- [ ] Check your dependencies (both software and operating system) for known vulnerabilities
- [ ] CI/CD Pipeline should read Secrets from Vault and sign Artifacts (Sigstore, Cosign)

### Continuous Deployment

- [ ] Design a rollback solution for your deployments
- [ ] Use Blue-Green Deployment or Canary Deployment
- [ ] Use Infrastructure as Code (e.g., Terraform, CloudFormation)
- [ ] Use Container Security (e.g., Image Scanning)
- [ ] Use Secrets Injection at Runtime (not at Build Time)

---

## 📖 Additional Resources

### Standards & Guidelines

- [OWASP API Security Top 10](https://owasp.org/www-project-api-security/) - Top 10 API vulnerabilities
- [OWASP Top 10](https://owasp.org/www-project-top-ten/) - Top 10 web vulnerabilities
- [REST API Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html) - REST API Security Cheat Sheet
- [JWT Best Practices](https://datatracker.ietf.org/doc/html/rfc8725) - RFC 8725: JSON Web Token Best Current Practices

### Tools

- [yosriady/api-development-tools](https://github.com/yosriady/api-development-tools) - A collection of useful resources for building RESTful APIs with HTTP and JSON
- [OWASP ZAP](https://www.zaproxy.org/) - Web security testing tool
- [Burp Suite](https://portswigger.net/burp) - Web security testing platform
- [Postman](https://www.postman.com/) - API testing and documentation tool

### Books & Articles

- [API Security in Action](https://www.manning.com/books/api-security-in-action) - Comprehensive API security book
- [Web Application Security](https://owasp.org/www-project-web-security-testing-guide/) - Web security testing guide

---

## 💰 Funding

<div align="center">

If this project has been useful to you, you can support us through the following methods:

### 💵 Payment in USD

[![PayPal](https://img.shields.io/badge/PayPal-00457C?style=for-the-badge&logo=paypal&logoColor=white)](https://paypal.me/yourusername)
[![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-FFDD00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black)](https://buymeacoffee.com/yourusername)
[![Ko-fi](https://img.shields.io/badge/Ko--fi-F16061?style=for-the-badge&logo=ko-fi&logoColor=white)](https://ko-fi.com/yourusername)

### ₿ Cryptocurrency

**Bitcoin (BTC)**
```
bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh
```

**Ethereum (ETH)**
```
0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb
```

**USDT (TRC20)**
```
TXYZabcdefghijklmnopqrstuvwxyz123456
```

**USDT (ERC20)**
```
0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb
```

> 💡 **Note**: Replace cryptocurrency addresses and payment links with your real addresses.

</div>

---

## 🤝 Contributing

You can easily fork this repository, apply your desired changes, and submit a pull request.

If you have any questions, email us at `info@zahedalfak.ir`.

For more details:
- [CONTRIBUTING.md](./CONTRIBUTING.md) - Contributing guide
- [SECURITY.md](./SECURITY.md) - Security policy

---

<div align="center">

**⭐ If this project was useful to you, please give it a Star! ⭐**

[![GitHub stars](https://img.shields.io/github/stars/MrAlfak/API-Security-Checklist.svg?style=social&label=Star)](https://github.com/MrAlfak/API-Security-Checklist)

</div>

---

<div align="center">

**Made with ❤️ for the developer community**

[API-Security-Checklist on GitHub](https://github.com/MrAlfak/API-Security-Checklist)

</div>

