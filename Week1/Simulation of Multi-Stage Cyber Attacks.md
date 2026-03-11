
# Digital Forensic Investigation Report: Phase 1

**Project Title:** Hybrid Network & Web Forensic Investigation of a Multi-Stage Cyber Attack  
**Phase:** Week 1 - Evidence Acquisition & Traffic Generation  
**Date:** March 11, 2026  
**Investigators:** Toqa Ayman & Basmalla Atta
**Target IP:** 192..1 (Ubuntu Server)  
**Attacker IP:** 192..1 (Windows Host)

---

## 1. Attack Scenario 1: Web-Based SQL Injection (SQLi)

### Technical Analysis
The investigator performed a SQL Injection attack targeting the `User ID` input field. The goal was to bypass application-level filtering and interact directly with the backend MariaDB database. By injecting a boolean tautology (`' or 1=1 --`), the SQL query was manipulated to return all records within the `users` table, regardless of the intended logic.

### Execution Steps:
1.  Targeted the DVWA SQL Injection module.
2.  Injected the primary payload: `' or 1=1 --`.
3.  Verified the vulnerability by retrieving the IDs, First Names, and Surnames of all registered users.

### Evidence Captured:
> ![SQLi Result](https://github.com/user-attachments/assets/7fd043f2-5f01-4106-8319-77f2163eab7a)
> *Figure 1: Application output proving successful data exfiltration via SQL Injection.*

### Forensic Artifacts Generated:
*   **Network:** HTTP GET requests containing encoded SQL characters (`%27`, `%20`, `%2D%2D`).
*   **Host Logs:** Entry in `access.log` documenting the malicious URI string.

---

## 2. Attack Scenario 2: Automated Brute Force Attack

### Technical Analysis
To simulate a credential harvesting/account takeover attempt, the investigator launched an automated **Dictionary Attack** against the login interface. Using **Burp Suite Intruder**, a pre-defined list of common usernames and passwords was submitted at high velocity to identify valid credentials based on HTTP response variations.

### Execution Steps:
1.  Intercepted the login POST request.
2.  Configured a "Cluster Bomb" attack type with 20 usernames and 20 passwords (400 total requests).
3.  Identified the correct account through a **302 Redirect** response (indicating successful authentication).
4.  **Successful Credentials Found:** `admin` / `password`.

### Evidence Captured:
> ![Burp Suite Result](https://github.com/user-attachments/assets/658b4414-bc0d-4fe7-b3c6-789413f1d8ed)
> *Figure 2: Burp Suite Intruder dashboard showing the high-frequency attack and the identified valid credential.*

---

## 3. Evidence Acquisition Log (Artifacts Collected)

During this phase, the following forensic artifacts were successfully acquired and hashed for integrity:

| Artifact Name | Format | Source | Forensic Purpose |
| :--- | :--- | :--- | :--- |
| `network_traffic.pcap` | PCAP | Ubuntu (tcpdump) | To analyze the packet-level attack signatures. |
| `apache_access.log` | Log | /var/log/apache2/ | To create a timeline of the HTTP requests. |
| `apache_error.log` | Log | /var/log/apache2/ | To identify failed injection attempts/errors. |
| `auth.log` | Log | /var/log/auth.log | To monitor system-level authentication failures. |

---

## 4. Payloads & Wordlists Reference

### A. SQL Injection Payload Set
*   `' or 1=1 --` (Authentication Bypass)
*   `admin' --` (Account Targeting)
*   `' union select user(), database() --` (Information Gathering)
*   `' and sleep(5) --` (Blind/Time-based testing)

### B. Brute Force Dictionary (Sample)
*   **Usernames:** `admin, root, user, guest, support, administrator...`
*   **Passwords:** `password, 123456, p@ssw0rd, qwerty, welcome1...`

---

## 5. Conclusion & Next Steps
Phase 1 is successfully concluded. The environment has been compromised in a controlled manner, and all necessary forensic artifacts have been collected. 

**Next Objective (Week 2):**  
Move the collected `.pcap` and log files to the **Kali Linux Forensic Workstation** for deep analysis, packet filtering in Wireshark, and performing **Event Correlation** to link network traffic to server log entries.

