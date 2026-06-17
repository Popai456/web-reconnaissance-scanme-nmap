# 🔍 Web Reconnaissance and Enumeration Assessment

## Overview

This repository documents a reconnaissance and enumeration assessment performed against **scanme.nmap.org**, an authorized training target provided by the Nmap Project.

The objective of this assessment was to identify exposed services, technologies, accessible directories, supported HTTP methods, and security-related observations using standard reconnaissance techniques.

---

## Scope

**Target:** scanme.nmap.org

**Assessment Type:** Reconnaissance and Enumeration

**Environment:** Kali Linux

---

## Objectives

* Perform service and version enumeration.
* Identify operating system information.
* Analyze web server configuration.
* Discover accessible directories and files.
* Review HTTP methods.
* Assess security-related HTTP headers.
* Document findings and recommendations.

---

## Tools Used

* Nmap
* Gobuster
* Curl
* Kali Linux

---

## Methodology

The assessment followed the following phases:

1. Host Discovery
2. Service Enumeration
3. Operating System Fingerprinting
4. Web Server Analysis
5. Directory Enumeration
6. HTTP Method Analysis
7. Security Header Review
8. Documentation of Findings

---

## Service Enumeration

### Command

```bash
nmap -sV scanme.nmap.org
```

### Results

| Port | Service    | Version              |
| ---- | ---------- | -------------------- |
| 22   | SSH        | OpenSSH 6.6.1p1      |
| 80   | HTTP       | Apache 2.4.7         |
| 9929 | Nping Echo | Nmap Testing Service |

### Observation

The target appears to be running Ubuntu Linux and Apache 2.4.7.

---

## Web Server Analysis

### Command

```bash
curl -I http://scanme.nmap.org
```

### Result

```http
HTTP/1.1 200 OK
Server: Apache/2.4.7 (Ubuntu)
Content-Type: text/html
```

### Observation

The server discloses software version information through HTTP headers.

**Severity:** Low

---

## Directory Enumeration

### Command

```bash
gobuster dir -u http://scanme.nmap.org -w /usr/share/wordlists/dirb/common.txt
```

### Key Results

```text
/.svn
/images
/shared
/.htaccess
/.htpasswd
```

### Observation

Sensitive files were detected but properly protected with HTTP 403 responses.

---

## SVN Directory Verification

### Command

```bash
curl -I http://scanme.nmap.org/.svn/
```

### Result

```http
HTTP/1.1 403 Forbidden
```

### Observation

The SVN directory exists but is not publicly accessible.

**Severity:** Informational

---

## HTTP Method Analysis

### Command

```bash
curl -X OPTIONS -i http://scanme.nmap.org
```

### Result

```http
Allow: GET,HEAD,POST,OPTIONS
```

### Observation

No potentially risky HTTP methods such as PUT, DELETE, TRACE, or CONNECT were enabled.

**Severity:** Informational

---

## Security Header Review

### Command

```bash
curl -s -D - http://scanme.nmap.org -o /dev/null
```

### Observed Headers

```http
Server: Apache/2.4.7 (Ubuntu)
Accept-Ranges: bytes
Vary: Accept-Encoding
Content-Type: text/html
```

### Missing Security Headers

* Content-Security-Policy (CSP)
* Strict-Transport-Security (HSTS)
* X-Frame-Options
* X-Content-Type-Options
* Referrer-Policy
* Permissions-Policy

### Observation

Several common security headers were not present in HTTP responses.

**Severity:** Low

---

## Findings Summary

| ID   | Finding                             | Severity      |
| ---- | ----------------------------------- | ------------- |
| F-01 | Server Version Disclosure           | Low           |
| F-02 | Missing Security Headers            | Low           |
| F-03 | SVN Directory Present but Protected | Informational |
| F-04 | Sensitive Files Properly Protected  | Good Practice |
| F-05 | No Dangerous HTTP Methods Enabled   | Good Practice |

---

## Recommendations

1. Disable unnecessary server version disclosure.
2. Implement Content Security Policy (CSP).
3. Configure HSTS where HTTPS is used.
4. Implement X-Frame-Options and X-Content-Type-Options.
5. Continue restricting access to sensitive files and directories.

---

## Screenshots

Screenshots of commands, outputs, and observations are available in the `screenshots/` directory.

---

## Report

The complete assessment report is available in the `report/` directory.

---

## Disclaimer

This assessment was performed only against **scanme.nmap.org**, an authorized training target provided by the Nmap Project for educational and testing purposes.

No unauthorized testing was conducted against any third-party systems.
