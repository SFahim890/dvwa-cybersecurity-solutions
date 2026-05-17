DVWA VULNERABILITY ASSESSMENT REPORT

Target Application: DVWA (Damn Vulnerable Web Application)
Assessment Type: Web Application Penetration Testing
Security Level: Low
Environment: Educational Laboratory
Severity: Multiple Critical and High Risk Vulnerabilities Identified

1. INTRODUCTION

This report documents the security assessment conducted against DVWA (Damn Vulnerable Web Application) at the Low security level.

The purpose of the assessment was to identify and exploit common web application vulnerabilities in order to understand insecure coding practices and demonstrate real-world attack scenarios.

Multiple critical vulnerabilities were identified, including:

Brute Force
Command Injection
CSRF
File Inclusion
File Upload
SQL Injection
Blind SQL Injection
Reflected XSS
Stored XSS
2. OBJECTIVE

The objectives of this assessment were to:

Identify web application vulnerabilities
Exploit insecure application functionality
Analyze security weaknesses
Demonstrate attack impact
Understand mitigation techniques
3. TOOLS USED

The following tools and technologies were used during testing:

DVWA
Web Browser
Burp Suite
Netcat
SQL Injection Payloads
JavaScript Payloads
Linux Shell Commands
4. VULNERABILITY FINDINGS
4.1 BRUTE FORCE

Severity: High

Description

The application login functionality lacked brute-force protection mechanisms such as:

Rate limiting
CAPTCHA
Account lockout
Login throttling

This allowed automated password guessing attacks.

Exploitation

Burp Suite Intruder was used to automate login attempts using username and password wordlists.

Successful credentials identified:

Username:
admin

Password:
password

Impact

Attackers may gain unauthorized access to user accounts through credential guessing attacks.

4.2 COMMAND INJECTION

Severity: Critical

Description

User input was passed directly to system shell commands without sanitization.

Exploitation

Example payloads:

127.0.0.1; ls

127.0.0.1 && hostname

127.0.0.1 | whoami

Reverse shell payloads successfully executed.

Impact

Attackers may execute arbitrary system commands and gain remote shell access.

4.3 CROSS-SITE REQUEST FORGERY (CSRF)

Severity: High

Description

The application failed to validate request origin during password change operations.

No CSRF token protections were implemented.

Exploitation

Password change requests were performed using GET requests.

Example:

http://127.0.0.1/DVWA/vulnerabilities/csrf/?password_new=hacked&password_conf=hacked&Change=Change#

Impact

Attackers may force authenticated users to perform unwanted actions.

4.4 FILE INCLUSION

Severity: Critical

Description

The application dynamically included files based on unsanitized user input.

Both:

Local File Inclusion (LFI)
Remote File Inclusion (RFI)

were possible.

Exploitation

LFI Payload:

page=../../../../../etc/passwd

RFI Payload:

page=https://www.google.com

Impact

Attackers may access sensitive files or execute remote malicious code.

4.5 FILE UPLOAD

Severity: Critical

Description

The application allowed unrestricted upload of executable PHP files.

Exploitation

A malicious PHP reverse shell was uploaded and executed successfully.

Netcat listener:

nc -lnvp 1234

Impact

Attackers may gain remote code execution and full server compromise.

4.6 SQL INJECTION

Severity: Critical

Description

User input was directly concatenated into SQL queries without sanitization.

Exploitation

Authentication bypass payload:

' OR 1=1#

Database enumeration payloads:

' UNION SELECT table_name, null FROM information_schema.tables#

Credential extraction payload:

' UNION SELECT user, password FROM users#

Impact

Attackers may retrieve sensitive database information and compromise user accounts.

4.7 BLIND SQL INJECTION

Severity: Critical

Description

The application exposed behavioral differences that allowed Boolean-based SQL Injection attacks.

Exploitation

TRUE condition:

1' OR 1=1#

FALSE condition:

1' AND 1=2#

Character extraction:

1' AND SUBSTRING(user,1,1)='a'#

Impact

Attackers may infer sensitive database information without direct query output.

4.8 REFLECTED XSS

Severity: High

Description

User input was reflected directly into webpage responses without encoding.

Exploitation

Payload:

<script>alert('XSS')</script>

Alternative payloads also executed successfully.

Impact

Attackers may execute JavaScript in victim browsers, hijack sessions, and steal cookies.

4.9 STORED XSS

Severity: High

Description

User-supplied input was stored in the database and rendered without sanitization.

Exploitation

Payload:

<script>alert('XSS')</script>

The payload executed automatically whenever users visited the affected page.

Impact

Attackers may persist malicious JavaScript affecting all users viewing the page.

5. OVERALL SECURITY IMPACT

The assessment identified multiple critical vulnerabilities that could allow attackers to:

Gain unauthorized access
Execute remote commands
Obtain reverse shells
Access sensitive files
Steal credentials
Hijack user sessions
Compromise the application database
Fully control the target server
6. GENERAL RECOMMENDATIONS

The following security improvements are recommended:

Implement Input Validation

Validate and sanitize all user input.

Use Parameterized Queries

Prevent SQL Injection using prepared statements.

Implement Output Encoding

Encode user-controlled content before rendering in webpages.

Restrict File Uploads

Validate file types and disable script execution in upload directories.

Prevent Command Injection

Avoid passing user input directly to system shell commands.

Implement CSRF Protection

Use CSRF tokens and secure request validation.

Apply Authentication Protections

Implement:

Rate limiting
CAPTCHA
Account lockout
Restrict File Inclusion

Use allowlists and disable remote file inclusion.

Deploy Security Headers

Implement:

Content Security Policy (CSP)
HTTPOnly cookies
SameSite cookies
Conduct Regular Security Testing

Perform routine vulnerability assessments and penetration testing.

7. CONCLUSION

The assessment confirmed that DVWA at the Low security level contains multiple intentionally vulnerable components that demonstrate common web application security weaknesses.

The identified vulnerabilities allow attackers to compromise confidentiality, integrity, and availability of the application and underlying server environment.
