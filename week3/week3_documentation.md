# Week 3 Documentation – Analysis Phase

## Project: Hybrid Network & Web Forensic Investigation

---

## 1. Introduction
This document explains the analysis process performed during Week 3, where collected forensic evidence was examined and correlated to reconstruct the cyber attack scenario.

---

## 2. Analysis Methodology

The analysis was performed using a triage-based forensic approach:

1. Web log analysis (Apache logs)
2. System log analysis (authentication logs)
3. Network traffic analysis (Wireshark)
4. Correlation of all evidence sources

---

## 3. Tools Used
- Apache Web Server Logs  
- Linux Authentication Logs (auth.log)  
- Wireshark  
- DVWA (Damn Vulnerable Web Application)

---

## 4. Web Log Analysis
Apache logs were analyzed to identify suspicious HTTP requests targeting the DVWA login page.

Findings:
- Repeated POST requests to /DVWA/login.php
- Automated login behavior detected
- High frequency requests in short time

---

## 5. System Log Analysis
Authentication logs were analyzed to detect unauthorized access attempts.

Findings:
- Multiple failed SSH login attempts
- Same source IP observed
- Target user: devasc

---

## 6. Network Traffic Analysis
Wireshark was used to analyze captured network traffic.

Findings:
- HTTP POST requests confirmed login attempts
- Matching behavior with Apache logs
- HTTP 302 responses observed

---

## 7. Correlation of Evidence
All evidence sources were correlated based on:
- IP address consistency (192.168.56.103)
- Time synchronization
- Similar attack patterns

---

## 8. Conclusion
The analysis confirmed a multi-stage attack involving:
- Reconnaissance activity
- Web application login attempts
- SSH brute force attacks
- Network-level validation of malicious activity
