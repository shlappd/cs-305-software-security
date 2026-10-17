# CS 305 Software Security

## Project Overview

This repository contains a software vulnerability assessment report developed for Artemis Financial as part of CS 305: Software Security at Southern New Hampshire University. The project demonstrates the process of evaluating a Java Spring Boot RESTful application for security vulnerabilities through manual code review, static dependency analysis, and risk-based mitigation planning.

The assessment examined application security, database access, input validation, cryptography, API design, error handling, and third-party dependencies. Using OWASP Dependency-Check with Maven, I identified known vulnerabilities in outdated software components and developed recommendations for improving the application's security posture.

## Client and Software Requirements

Artemis Financial is a financial consulting company that develops individualized financial plans involving savings, retirement, investments, and insurance. The company sought to modernize its existing software while strengthening the security of its RESTful web application against external threats.

The primary security requirements involved protecting sensitive financial information, preventing unauthorized access, securing client-server communications, and addressing vulnerabilities in the application's code and dependencies.

To address these requirements, I evaluated the application for risks involving authentication and authorization, unvalidated input, exposed credentials, insecure database configurations, outdated cryptographic components, and vulnerable third-party libraries.

## Vulnerability Assessment

### Manual Code Review

One of the strongest aspects of my assessment was identifying security weaknesses within the application's Java source code and connecting those findings to practical mitigation strategies.

The manual review documented ten findings across several classes and configuration files:

- **Hard-coded credentials:** `DocData.java` contained embedded database credentials, creating a risk of credential exposure.
- **Excessive database privileges:** The application used the MySQL `root` account instead of a dedicated, least-privilege application account.
- **Insecure database transport:** The JDBC configuration did not explicitly enforce encrypted database connections.
- **Improper error handling:** Direct stack-trace output could disclose sensitive implementation details.
- **Insufficient input validation:** Controller parameters lacked explicit restrictions on permitted values, formats, and lengths.
- **Unrestricted HTTP methods:** A general `@RequestMapping` exposed an endpoint to more HTTP methods than necessary.
- **Missing access controls:** The supplied application did not demonstrate authentication or authorization mechanisms for REST endpoints.
- **Insufficient encapsulation:** The `account_balance` field lacked appropriate access restrictions.
- **Missing transaction validation:** The `deposit()` method did not reject invalid financial transaction amounts.
- **Outdated dependencies:** The application relied on obsolete Spring Boot and cryptographic libraries.

These findings reinforced the importance of examining software security beyond whether an application compiles and functions correctly. Secure code must also preserve confidentiality, integrity, and availability under potentially malicious conditions.

### Static Dependency Analysis

I integrated the OWASP Dependency-Check Maven plug-in to examine the application's third-party libraries for publicly documented vulnerabilities.

The resulting assessment reported:

- 38 dependencies scanned
- 20 unique components
- 11 vulnerable dependencies
- 252 vulnerability findings
- Severity classifications ranging from Medium to Critical

Affected components included Spring Framework, Spring Boot, Apache Tomcat, Jackson Databind, SnakeYAML, and Bouncy Castle.

Several findings were particularly significant:

- **CVE-2022-22965 (Spring4Shell):** A Spring Framework vulnerability that can permit remote code execution under specific deployment conditions.
- **CVE-2022-1471 (SnakeYAML):** Unsafe deserialization that can result in remote code execution when untrusted YAML is processed.
- **CVE-2020-1938 (Ghostcat):** An Apache Tomcat AJP vulnerability that can permit unauthorized access to application files under affected configurations.
- **CVE-2020-36518 (Jackson Databind):** Excessive recursion from deeply nested JSON that can cause denial of service.

The most important finding was that the application's dependency-security problems extended beyond isolated libraries. Its outdated Spring Boot platform introduced numerous vulnerable transitive dependencies, making framework modernization a more effective long-term strategy than independently replacing individual JAR files.

## Security Challenges and Lessons Learned

The most challenging part of the vulnerability assessment was interpreting the Dependency-Check results and distinguishing vulnerability severity from actual application risk.

A reported CVE indicates that a component may contain a known vulnerability, but exploitation can depend on its configuration, execution path, and environmental conditions. Consequently, vulnerability remediation requires more than identifying the highest CVSS score.

The assessment demonstrated the importance of considering exploitability, potential impact, affected components, and known exploitation when prioritizing security improvements.

It also showed how manual review and automated dependency scanning complement one another. Manual inspection revealed application-specific problems such as hard-coded credentials, missing input validation, and weak access controls, while Dependency-Check identified known weaknesses in third-party components that would be difficult to discover through source inspection alone.

## Security Improvements and Mitigation Strategy

Based on the assessment findings, I developed a layered mitigation plan intended to reduce the application's attack surface and improve its resistance to external threats.

The proposed improvements included:

1. **Authentication and authorization:** Implement Spring Security and require appropriate permissions for protected API operations.
2. **Secrets management:** Remove embedded credentials and use environment variables or a dedicated secrets-management system.
3. **Least privilege:** Replace the MySQL administrative account with a restricted application account.
4. **Secure communication:** Require HTTPS/TLS for application traffic and encrypted database connections where appropriate.
5. **Input validation:** Enforce accepted types, formats, lengths, and value ranges before processing external data.
6. **API restrictions:** Limit endpoints to their intended HTTP methods and apply appropriate authorization rules.
7. **Secure error handling:** Replace uncontrolled stack traces with structured logging and sanitized client responses.
8. **Data encapsulation:** Protect financial state through private fields and validated transaction methods.
9. **Dependency modernization:** Upgrade the unsupported Spring Boot platform and affected cryptographic libraries.
10. **Continuous vulnerability management:** Integrate dependency scanning, patch management, and security review into the development lifecycle.

These recommendations illustrate the principle of **defense in depth**, in which multiple complementary security controls reduce the likelihood that a single weakness will compromise an application.

For future assessments, I would prioritize vulnerabilities by combining CVSS severity, documented exploitation, business impact, exposure, and the actual application configuration.

## Secure Development and Verification

An important distinction in this project was the difference between identifying vulnerabilities and verifying that remediation has succeeded.

Project One focused on assessment and mitigation planning rather than implementing the recommended corrections. Therefore, the resulting report establishes a remediation baseline, not proof that the application has been secured.

After future code refactoring, I would verify both functionality and security through a combination of:

- Manual review of modified code for syntactical, logical, and security weaknesses.
- Unit and integration testing to verify expected application behavior.
- Repeat OWASP Dependency-Check scans to identify remaining vulnerable dependencies.
- Regression testing to determine whether security changes disrupt existing functionality.
- Authentication, authorization, and input-validation testing.
- Review of newly introduced dependencies and configuration changes.

This process would help confirm that vulnerability remediation does not introduce additional defects or undermine existing application functionality.

## Tools and Security Practices

The project provided practical experience with several technologies and security assessment techniques:

- **Java:** Reviewing application code for vulnerabilities involving validation, access control, encapsulation, and error handling.
- **Spring Boot:** Evaluating the security implications of RESTful application architecture and framework dependencies.
- **Apache Maven:** Managing application dependencies and integrating security-analysis plug-ins.
- **OWASP Dependency-Check:** Identifying known vulnerabilities in direct and transitive software dependencies.
- **CVE and CVSS:** Understanding vulnerability identifiers, technical descriptions, and severity classifications.
- **National Vulnerability Database (NVD):** Researching published vulnerabilities and their affected software versions.
- **CISA Known Exploited Vulnerabilities Catalog:** Identifying vulnerabilities with documented exploitation.
- **Secure coding principles:** Applying defense in depth, least privilege, secure configuration, input validation, and dependency maintenance.

A key lesson was that secure software development is an ongoing process rather than a single testing phase. Applications must be reviewed, maintained, and reassessed as vulnerabilities are discovered and dependencies become outdated.

## Portfolio Reflection

This project strengthened my ability to evaluate an existing software application from a security perspective rather than focusing exclusively on functional requirements.

I demonstrated the ability to inspect Java source code, identify security weaknesses, interpret automated vulnerability reports, and translate technical findings into actionable remediation recommendations.

For prospective employers, the completed vulnerability assessment demonstrates my familiarity with security analysis, software supply-chain risk, vulnerability classification, and secure development practices. It also illustrates my ability to communicate technical risks and recommend improvements in a structured professional report.

The most valuable lesson from this assignment was understanding that software security depends on both application-level safeguards and the security of the underlying technology stack. Effective vulnerability management requires examining code, configuration, dependencies, and deployment conditions together.

## Repository Contents

- `Artemis Financial - Software Vulnerability Assessment Report.docx` — Completed vulnerability assessment documenting manual code review, static testing results, identified vulnerabilities, and recommended mitigations.
- `README.md` — Project overview, security concepts, technical findings, and portfolio reflections.

## Course

CS 305: Software Security  
Southern New Hampshire University
